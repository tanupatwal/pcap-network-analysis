# Network Forensic Analysis: SBT-PCAP5.pcap

### **Challenge Objective**

The objective of this analysis was to perform a deep-packet inspection of the `SBT-PCAP5.pcap` file to identify hosted web content, determine service versions, and extract specific packet metadata using both command-line forensic tools (`strings`, `grep`) and graphical analysis tools (Wireshark).

---

### **Question 1: What is the name of the PNG file on the webserver at 192.168.56.111?**


**Tools used:** `strings`, `grep` 


* **Command:**
strings <file> | grep ".png"


**Methodology:**

1. **Challenge Identification:** An initial attempt was made using `tcpdump` with ASCII flags to locate the filename. However, it was noted that HTTP GET requests for image files were fragmented across multiple TCP packets, preventing the filename from appearing in a single readable line in `tcpdump`.


2. **String Extraction:** The `strings` utility was used to scan the binary `.pcap` file and extract human-readable text sequences regardless of packet boundaries.


3. **Pattern Matching:** The output was piped to `grep ".png"` to filter specifically for image file extensions within the extracted text.


4. **Result Verification:** The command revealed an HTML directory listing served by a `SimpleHTTP/0.6 Python/2.7.16` server, which explicitly contained the file in an anchor tag.




* **Screenshot Name:** `Terminal_PNG_Extraction.png`
* **Answer:** `proprietary.png`

---


### **Question 2: Which version of OpenSSH is running on the server?**

* **Tool used:** `tcpdump`
* **Command:** 
tcpdump -nn -r <file> -A port 22 | grep "SSH-"


* **Methodology:**

1. **Protocol Filtering:** Used the `port 22` filter to isolate Secure Shell (SSH) traffic.
2. **ASCII Inspection:** Applied the `-A` flag to print the packet payload in ASCII, which allows for reading the human-readable banner exchange.
3. **String Filtering:** Piped the output to `grep "SSH-"` to capture the specific version strings exchanged during the initial connection.
4. **Analysis:** The output shows two versions. The first is the client's version (`OpenSSH_7.6`). The second, originating from the server IP `192.168.56.111`, identifies the server software.


* **Screenshot Name:** `Terminal_SSH_Version_Handshake.png`
* **Answer:** `OpenSSH_7.9p1 Debian-10`

---


### **Question 3: On which port is the .zip file being served?**

* **Tools used:** `tcpdump`, `grep`, `awk`, `cut`, `sort`
* **Command:** 
tcpdump -nn -r <file> -A | grep -B 5 ".zip" | grep "IP" | awk '{print $3}' | cut -d'.' -f5 | sort -u

* **Methodology:** 

1.  **Contextual Search:** Used `tcpdump -A` to display packet payloads in ASCII format. I then piped this into `grep -B 5 ".zip"` to search for the zip extension while capturing the 5 lines of metadata (packet headers) immediately preceding the match.
2.  **Header Isolation:** Piped the result into `grep "IP"` to specifically isolate the packet headers containing Source and Destination IP/Port information.
3.  **Data Parsing:** Used `awk '{print $3}'` to select the third column (the Source address). In `tcpdump` output, addresses are formatted as `IP.IP.IP.IP.PORT`.
4.  **Extraction:** Used `cut -d'.' -f5` to isolate the fifth field delimited by a period, which corresponds to the port number. Finally, `sort -u` was used to ensure only unique port numbers were displayed.
5.  **Result:** The command successfully identified that the `.zip` file (containing the internal file `ziptest.txt`) was being served over the identified port.
* **Screenshot Name:** `Terminal_Zip_Port_Extraction.png`
* **Answer:** `3016`


---


### **Question 4: When was a packet with a TCP checksum value of 53203 captured? (Format: xx:xx:xx.xxxxxx)**

* **Tool used:** Wireshark

* **Display Filter:** tcp.checksum == 0xcfd3

* **Methodology:** 

1. **Decimal to Hex Conversion:** The target TCP checksum value provided was 53203 (decimal). To filter for this in Wireshark, the value was converted to hexadecimal, resulting in 0xcfd3.

2. **Applying the Filter:** Opened SBT-PCAP5.pcap in Wireshark and entered the display filter tcp.checksum == 0xcfd3.

3. **Packet Identification:** The filter isolated Frame 177, which is an encrypted SSHv2 packet sent from the client (192.168.56.1) to the server (192.168.56.111).

4. **Timestamp Extraction:** By inspecting the Frame details, the arrival time was identified. While the raw UTC time is 11:04:46.207925, the system arrival time in the local timezone (IST, which is UTC + 5:30) confirms the captured time.

* **Screenshot Name:** `Wireshark_TCP_Checksum_Filter.png`

* **Answer:** `16:34:46.2079`
