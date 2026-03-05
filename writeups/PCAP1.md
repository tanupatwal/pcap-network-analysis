# Network Traffic Analysis Report: SBT-PCAP1

**Tool Used:** Wireshark 

**File:** `SBT-PCAP1.pcapng`

## Overview

This report documents the forensic analysis of a network packet capture to identify specific protocols, communication patterns, and data volume statistics.

---

### **Question 1: What is the WebAdmin password?**

**Display Filter:** `http` 


* **Methodology:**
1. Applied a general `http` filter to isolate web traffic and reduce network noise. 


2. Used the **Find Packet** (Ctrl+F) tool with the search type set to **String** and searched in **Packet bytes** for the keyword **"password"**. 


3. Located a packet containing an **HTTP GET** request for the file `/password.txt`. 


4. Right-clicked the corresponding server response and selected **Follow > HTTP Stream** to reconstruct the plaintext data. 




* 
**Screenshot: `q1_webadmin_credential_harvesting.png**` 


* 
**Description:** This screenshot captures the reconstructed HTTP data stream showing the server's response to the GET request for `password.txt`. It clearly reveals the administrative credentials stored in plaintext: `WebAdmin Password: sbt123`. 


* 
**Answer:** **`sbt123`** 


* 
**Observation:** Storing sensitive credentials in a publicly accessible plaintext file within the web root is a severe misconfiguration. This oversight allowed the attacker to gain administrative access without needing to deploy complex exploits or brute-force tools. 

For your GitHub writeup, the most professional and descriptive name for this screenshot would be:

**Screenshot Name: `q1_webadmin_credential_harvesting.png**` 

---

### **Question 1: What is the WebAdmin password?**

* 
**Display Filter:** `http` 


* **Methodology:**
1. Applied a general `http` filter to isolate web traffic and reduce network noise. 


2. Used the **Find Packet** (Ctrl+F) tool with the search type set to **String** and searched in **Packet bytes** for the keyword **"password"**. 


3. Located a packet containing an **HTTP GET** request for the file `/password.txt`. 


4. Right-clicked the corresponding server response and selected **Follow > HTTP Stream** to reconstruct the plaintext data. 




* 
**Screenshot: `q1_webadmin_credential_harvesting.png**` 


* 
**Description:** This screenshot captures the reconstructed HTTP data stream showing the server's response to the GET request for `password.txt`. It clearly reveals the administrative credentials stored in plaintext: `WebAdmin Password: sbt123`. 


* 
**Answer:** **`sbt123`** 


* 
**Observation:** Storing sensitive credentials in a publicly accessible plaintext file within the web root is a severe misconfiguration. This oversight allowed the attacker to gain administrative access without needing to deploy complex exploits or brute-force tools. 




**Question 2:** What is the IP address of the host that was pinged twice?

* **Display Filter:** `icmp`
* **Methodology:**

1. Filtered for all ICMP traffic to locate diagnostic "ping" messages.
2. Navigated to Statistics > Endpoints and selected the IPv4 tab.
3. Enabled the "Limit to display filter" checkbox to narrow down the hosts involved in ICMP traffic.
4. Counted the packets; the destination host 8.8.4.4 received exactly two Echo Requests.

* Screenshot: pcap-network-analysis/images/SBT-PCAP1/q2_icmp_ping.png
* Description: ICMP Echo Request packets showing the destination host being pinged.

* **Answer:** **8.8.4.4**
* **Verification:** The address `8.8.4.4` shows exactly 2 packets under this specific filter, whereas other hosts like `8.8.8.8` show higher frequencies.

---


**Question 3:** How many DNS query response packets were captured?

* **Display Filter:** `dns.flags.response == 1`
* **Methodology:**
1. To exclude queries and only count the answers, I used the DNS flag filter where the response bit is set to 1.
2. Once applied, I looked at the **Status Bar** (bottom right) of the Wireshark interface.

* Screenshot: pcap-network-analysis/images/SBT-PCAP1/q3_dns_responses.png
* Description: Wireshark showing DNS response packets after applying the response flag filter.

* **Answer:** **90**
* **Command Alternative:** In a terminal using `tshark`, the command would be:
`tshark -r SBT-PCAP1.pcapng -Y "dns.flags.response == 1" | wc -l`

---

**Question 4:** What is the IP address of the host that sent the most bytes?

* **Navigation:** **Statistics —> Endpoints**
* **Methodology:**
1. Opened the Endpoints window and selected the **IPv4** tab.
2. Clicked on the **Tx Bytes** (Transmitted Bytes) column header to sort in descending order.
3. Identified the top IP address based on the total volume of data originating from that host.

* Screenshot: pcap-network-analysis/images/SBT-PCAP1/q4_endpoints_list.png
* Description: Wireshark Endpoints statistics sorted by transmitted bytes showing the top data sender.

* **Answer:** **115.178.9.18**
* **Data Insight:** This host is the primary source of data in this capture, likely representing an external server delivering content to the local network.

---

## Summary of Filters Used

| Goal | Wireshark Display Filter |
| --- | --- |
| **Filter by Port** | `udp.port == 3942` |
| **Filter Ping Requests** | `icmp` |
| **Filter DNS Responses** | `dns.flags.response == 1` |
| **Filter by Specific IP** | `ip.src == 115.178.9.18` |
