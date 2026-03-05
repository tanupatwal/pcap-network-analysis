# PCAP Network Analysis Repository

This repository contains multiple **network traffic analysis investigations** performed on packet capture (PCAP) files using **Wireshark** and related tools.

The goal of this project is to practice and demonstrate skills in:

* Network packet inspection
* Protocol analysis
* Traffic filtering
* Network forensics investigation
* Wireshark usage for cybersecurity analysis

Each PCAP file is analyzed and documented with a structured methodology including filters used, observations, screenshots, and conclusions.

---

# Repository Structure

```
pcap-network-analysis
│
├── images
│   ├── SBT-PCAP1
│   │   ├── q1_ssdp_port3942.png
│   │   ├── q2_icmp_ping.png
│   │   ├── q3_dns_responses.png
│   │   └── q4_endpoints_list.png
│
├── pcap_files
│   ├── SBT-PCAP1.pcapng
│   ├── SBT-PCAP2.pcapng
│   ├── SBT-PCAP3.pcapng
│   ├── SBT-PCAP4.pcap
│   └── SBT-PCAP5.pcap
│
├── writeups
│   ├── pcap1.md
│   ├── pcap2.md
│   ├── pcap3.md
│   ├── pcap4.md
│   └── pcap5.md
│
└── README.md
```

---

# Folder Description

### images/

Contains screenshots taken during Wireshark analysis.

Each PCAP has its own folder containing screenshots used in the corresponding investigation report.

Example:

```
images/SBT-PCAP1/
```

---

### pcap_files/

Contains the original **packet capture files** used for analysis.

These files are opened in Wireshark for investigation.

Example:

```
SBT-PCAP1.pcapng
```

---

### writeups/

Contains detailed **analysis reports** for each PCAP file.

Each report includes:

* Investigation questions
* Display filters used
* Methodology
* Observations
* Screenshots
* Final answers

Example:

```
writeups/pcap1.md
```

---

# Tools Used

* **Wireshark** – Packet capture analysis
* **tshark** – Command-line packet analysis
* **GitHub** – Documentation and project hosting
* **tcpdump** – Packet capture analysis

---

# Example Investigation Topics

Some of the network analysis tasks included in this repository:

* Identifying protocols used on specific ports
* Investigating ICMP traffic
* Counting DNS responses
* Identifying hosts transmitting the most data
* Analyzing network communication patterns

---

# Purpose of this Project

This project is part of **cybersecurity and network analysis practice** to build hands-on experience in:

* Network traffic investigation
* Packet-level analysis
* Digital forensics techniques
* Blue Team / SOC analyst skill development

---

