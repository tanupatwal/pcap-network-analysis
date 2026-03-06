# Network Forensic Analysis: SBT-PCAP4.pcap

### **Challenge Objective**

The objective of this analysis was to perform a deep-packet inspection of the `SBT-PCAP4.pcap` file using command-line forensic tools (`tcpdump`) to quantify protocol distribution, identify specific TCP handshake states, and extract metadata related to user-agent strings and TTL values.

---

### **Question 1: How many UDP packets have been captured?**

* **Tool used:** `tcpdump`
* **Command:** ```bash
tcpdump -r SBT-PCAP4.pcap udp --count

* **Methodology:** 

1. Used the `-r` flag to read the offline pcap file.
2. Applied the `udp` primitive filter to isolate User Datagram Protocol traffic.
3. Utilized the `--count` flag to generate a terminal summary of the matching packet total.
* **Screenshot Name:** `q1_udp_packet_count.png`
* **Answer:** `3290`


---


### **Question 2: How many TCP packets have both the SYN and ACK flags set?**

* **Tool Used:** `tcpdump`
* **Command:**
tcpdump -r "SBT-PCAP4.pcap" "tcp[tcpflags] == 18" --count

---

### **Methodology**

1. **Handshake Identification:** The objective was to isolate the second step of the TCP three-way handshake (the **SYN-ACK** response from the server).
2. **Bitmask Logic:** TCP flags are stored in the 13th byte of the TCP header. Each flag corresponds to a specific decimal value. To find a packet with both SYN and ACK set, their values are added:
* **SYN** (Synchronize) = **2**
* **ACK** (Acknowledgment) = **16**
* **Total** = **18**


3. **Filtering & Counting:** The filter `tcp[tcpflags] == 18` ensures that only packets with these exact flags are returned. The `--count` flag provides a direct numerical summary for efficiency.

---

### **Reference: TCP Flag "Magic Numbers"**

The following table outlines the decimal values used for common TCP flag combinations in `tcpdump` filtering:

| TCP Flag State | Decimal Value | Command Filter |
| --- | --- | --- |
| **SYN** | 2 | `tcp[tcpflags] == 2` |
| **SYN-ACK** | **18** | `tcp[tcpflags] == 18` |
| **ACK** | 16 | `tcp[tcpflags] == 16` |
| **PSH-ACK** | 24 | `tcp[tcpflags] == 24` |
| **FIN-ACK** | 17 | `tcp[tcpflags] == 17` |
| **RST** | 4 | `tcp[tcpflags] == 4` |

---

* **Screenshot Name:** `q2_tcp_syn_ack_count.png`
* **Answer:** `20`

---


### **Question 3: Which version of Chrome was used to connect to securityblue.team?**

* **Tool Used:** `tcpdump` + `grep`
* **Command:**

tcpdump -A -r "SBT-PCAP4.pcap" | grep -i "Chrome"

### **Methodology**

1. **ASCII Output:** The `-A` flag was used to display the packet payload in ASCII, making the HTTP headers readable.
2. **String Filtering:** The output was piped to `grep -i "Chrome"` to quickly scan the traffic for browser identity strings.
3. **User-Agent Analysis:** The browser version was extracted from the `User-Agent` field within the HTTP GET requests.

---

* **Screenshot Name:** `q3_chrome_version_identification.png`
* **Answer:** `80.0.3987.87`


---


### **Question 4: How many packets have a TTL value of 38?**

* **Tool Used:** `tcpdump`
* **Command:** 
tcpdump -r "SBT-PCAP4.pcap" "ip[8] == 38" --count

---

### **Methodology**

1. **Field Targeting:** The **Time to Live (TTL)** value is a 1-byte field located at **offset 8** in the IPv4 header.
2. **Filter Logic:** The expression `ip[8] == 38` was used to instruct `tcpdump` to inspect that specific byte and filter for packets where the value is exactly 38.
3. **Efficiency:** The built-in `--count` flag was used to provide a direct numerical total, ensuring a faster and more reliable result than piping to external counting utilities.

---

* **Screenshot Name:** `q4_ttl_field_analysis.png`
* **Answer:** `710`

---


### **Summary of Analysis**

| Metric | Result |
| --- | --- |
| **Total UDP Packets** | 3,290 |
| **TCP SYN+ACK Packets** | 20 |
| **Target Browser Version** | Chrome 80.0.3987.87 |
| **TTL 38 Packets** | 710 |

---