# Network Forensic Analysis: SBT-PCAP3.pcapng

### **Challenge Objective**

The goal of this analysis was to investigate a compromised network environment to determine how an attacker intercepted communications between a central server and a host, and to identify what sensitive information was stolen.

---

### Question 1: What is the MAC address of the attacker?

* **Display Filter:** `arp`

* **Methodology:**

1. Applied the `arp` filter to isolate Address Resolution Protocol traffic.

2. Observed the packet list for any "Duplicate IP address detected" warnings.

3. Analyzed Frame 522, where the system flagged that 192.168.56.1 was being claimed by a new MAC address.

4. Inspected the Address Resolution Protocol (reply) section in the packet details.

5. Identified the Sender MAC address field, which officially maps the attacker's hardware to the intercepted IP.

* **Screenshot:** `q1_attacker_mac_identification.png`

* **Description:** This screenshot captures the ARP analysis within Wireshark. It highlights the critical duplicate IP warning and the packet details window where the attacker's physical address is explicitly listed as the sender of the spoofed reply.


* **Answer:** `08:00:27:3d:27:5d` 

* **Observation:** The presence of multiple ARP replies for the same IP address in a short time frame is a definitive indicator of an active spoofing attempt to hijack network traffic.

---


### **Question 2: What is the type of attack which is taking place that allows the attacker to listen in on conversations between the central server and another host?**

* **Display Filter:** `arp`, followed by `arp.duplicate-address-detected` 


* **Methodology:**

1. Applied the `arp` filter to establish a baseline of network discovery traffic.

2. Observed a high volume of broadcast requests from the attacker attempting to map out live hosts on the local network.

3. Applied the specific filter `arp.duplicate-address-detected` to isolate conflicting hardware-to-IP mappings.

4. Identified that the attacker was spamming unsolicited ARP replies to the victim and the default gateway.

5. Determined the attacker's objective was to overwrite the ARP Cache of the target machines, effectively redirecting traffic through their own interface.

**Screenshots:** `q2_arp_network_scan.png`, `q2_arp_poisoning_evidence.png` 

**Description:** The first screenshot (`q2_arp_network_scan.png`) shows the initial baseline discovery where the attacker scans the network. The second screenshot (`q2_arp_poisoning_evidence.png`) captures the exact moment Wireshark flags the duplicate IP detection, proving the attacker's MAC address is masquerading as the gateway.


**Answer:** **ARP Spoofing**, **ARP Poisoning**, or **Man-in-the-Middle (MitM) Attack**.

**Observation:** These terms all describe the same fundamental exploit: manipulating the Layer 2 address table to intercept private communications. The high frequency of unsolicited ARP replies is a textbook indicator of an active poisoning attempt.

---


### **Question 3: What is the file which was downloaded from the central server?**

**Display Filter:** `ftp` 

**Methodology:**

1. Applied the `ftp` filter to isolate File Transfer Protocol traffic within the network capture.

2. Observed a **RETR** command sent by the client to the server.

3. In FTP, the **RETR** command stands for **Retrieve**; it is the specific command a client uses when it wants to download a file from a server.

4. Identified the requested file in the command string: `RETR Alevis_Employee_Information_Chart.csv`.

5. Located the confirmation in **Packet 621**, which contains the server response `226 Transfer complete`, confirming the download was successful.


**Screenshot:** `q3_ftp_file_retrieval.png` 

**Description:** This screenshot shows the filtered FTP traffic. It highlights the client's request to retrieve the CSV file and the subsequent server confirmation indicating that the transfer was successfully completed.

**Answer:** `Alevis_Employee_Information_Chart.csv` 

**Observation:** The use of unencrypted FTP allowed for the clear-text observation of the file retrieval process and the identification of the specific sensitive data being exfiltrated.

---


### **Question 4: What department does Borden Danilevich work in?**

**Display Filter:** `ftp` or `tcp.stream eq 1` 

**Methodology:**

1. Located the **FTP data transfer** packets within the capture that correspond to the exfiltrated employee information file.

2. Right-clicked on a packet associated with the downloaded file (`Alevis_Employee_Information_Chart.csv`) and selected **Follow > TCP Stream**.

3. Set the stream to **Stream 1** to reconstruct the plaintext content of the stolen CSV document.

4. Used the **Find** tool within the stream window to search for the specific keyword **"Borden"**.

5. Analyzed the resulting entry (ID 292), which explicitly lists the employee's details including his name and department.

**Screenshot:** `q4_borden_department_search.png` 

**Description:** This screenshot shows the reconstructed TCP Stream 1 for the downloaded CSV file. The search for "Borden" is active, highlighting the record `292,Borden,Danilevich,...` which identifies his professional placement within the organization.

**Answer:** `Sales` 

**Observation:** The ability to reconstruct the entire file content from a single TCP stream confirms that sensitive organizational data was transmitted without encryption, making it easily readable by the attacker.


---


### **Question 5: What is the SSH password of the Domain Administrator?**

**Display Filter:** `ftp` or `tcp.stream eq 1` 

**Methodology:**

1. Applied the `ftp` filter to locate the control traffic for the sensitive file transfer.

2. Right-clicked on the packet corresponding to the download of `Alevis_Employee_Information_Chart.csv` and selected **Follow > TCP Stream**.

3. Ensured the view was set to **Stream 1** to view the full reconstructed content of the exfiltrated CSV file.

4. Used the search tool to find the keyword **"DomAdmin"**, which is located at the very end of the employee list.

5. Extracted the plaintext password found in the final column of the Domain Administrator's record.

**Screenshot:** `q5_domadmin_password_extraction.png`

**Description:** This screenshot shows the bottom of the reconstructed TCP stream for the stolen employee chart. By searching for the "DomAdmin" user, the final record in the file is revealed, displaying the administrative SSH password in clear text.

**Answer:** `gMR<4eXf]e6W` 

**Observation:** The inclusion of administrative credentials in an unencrypted CSV file, which was then transmitted via an unencrypted protocol (FTP), represents a massive security failure that gave the attacker full domain-level access.


---


### **Tools Used**
 
**Wireshark:** Primary tool for packet capture analysis and protocol filtering.

**Network Protocols:** Analysis of FTP (Application Layer), ARP (Link Layer), and DHCP.



---