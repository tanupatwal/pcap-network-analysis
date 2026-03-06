### PCAP Network Analysis Repository

**This repository contains multiple network traffic analysis investigations performed on packet capture (PCAP) files using Wireshark, tcpdump, and other command-line forensic tools.**

** The goal of this project is to practice and demonstrate skills in:**

* Network packet inspection and deep-packet analysis

* Protocol distribution and behavioral analysis

* Metadata extraction (User-Agents, SSH banners, Checksums)

* Network forensics and incident response investigation

* Advanced traffic filtering using bitwise offsets

**Data Source & Credits**

* The PCAP files analyzed in this repository are part of the training labs provided by Security Blue Team (SBT). These investigations simulate real-world scenarios encountered by SOC analysts and network forensic investigators, focusing on identifying malicious activity and extracting technical artifacts from raw traffic.

**Repository Structure**

pcap-network-analysis/
│
├── images/
│   ├── SBT-PCAP1/
│   │   ├── q1_ssdp_port3942.png
│   │   ├── q2_icmp_ping.png
│   │   ├── q3_dns_responses.png
│   │   └── q4_endpoints_list.png
│   │
│   ├── SBT-PCAP2/
│   │   ├── q1_webadmin_credential_harvesting.png
│   │   ├── q1_webadmin_pass.png
│   │   ├── q2_ftp_banner_version.png
│   │   ├── q3_ftp_command_execution.png
│   │   ├── q4_confidential_file_discovery.png
│   │   ├── q4_smb_desktop_recon.png
│   │   └── q5_smb_logfile_artifact.png
│   │
│   ├── SBT-PCAP3/
│   │   ├── q1_attacker_mac_identification.png
│   │   ├── q2_arp_network_scan.png
│   │   ├── q2_arp_poisoning_evidence.png
│   │   ├── q3_ftp_file_retrieval.png
│   │   ├── q4_borden_department_search.png
│   │   └── q5_domadmin_password_extraction.png
│   │
│   ├── SBT-PCAP4/
│   │   ├── q1_udp_packet_count.png
│   │   ├── q2_tcp_syn_ack_count.png
│   │   ├── q3_chrome_version_identification.png
│   │   └── q4_ttl_field_analysis.png
│   │
│   └── SBT-PCAP5/
│       ├── Terminal_PNG_Extraction.png
│       ├── Terminal_SSH_Version_Handshake.png
│       ├── Terminal_Zip_Port_Extraction.png
│       ├── Wireshark_HTTP_Initial_Request.png
│       └── Wireshark_TCP_Checksum_Filter.png
│
├── pcap_files/
│   ├── SBT-PCAP1.pcapng
│   ├── SBT-PCAP2.pcapng
│   ├── SBT-PCAP3.pcapng
│   ├── SBT-PCAP4.pcap
│   └── SBT-PCAP5.pcap
│
├── writeups/
│   ├── PCAP1.md
│   ├── PCAP2.md
│   ├── PCAP3.md
│   ├── PCAP4.md
│   └── PCAP5.md
│
└── README.md

**Folder Description**

* images/

Contains visual evidence and screenshots captured during investigation. Each challenge (SBT-PCAP1 through SBT-PCAP5) has its own dedicated sub-folder for organized reference.

* pcap_files/

Contains the raw binary packet capture files. These are the primary data sources used for the forensic investigations.

* writeups/

Contains detailed, structured analysis reports in Markdown format. Each report documents tools used, methodology, and final findings.

**Tools Used**

* Wireshark – Graphical packet analysis and protocol dissection.

* tcpdump – Command-line packet capture and bitwise offset filtering.

* strings / grep – Identification of cleartext data and metadata within binary streams.

* awk / cut – Automated parsing of network header information.

**Key Investigation Topics**

* Credential Harvesting: Extracting cleartext passwords from HTTP/FTP/SMB.

* Banner Grabbing: Identifying server OS and software versions (e.g., OpenSSH 7.9p1 on Debian).

* Protocol Distribution: Quantifying traffic volume across TCP, UDP, and ICMP.

* Forensic Metadata: Locating specific packets using TCP checksums and identifying User-Agent strings.

* Network Reconnaissance: Identifying ARP scanning and MAC spoofing attempts.

**Purpose**

* This repository serves as a portfolio for Cybersecurity and Network Defense skills, focusing on the ability to translate raw network data into actionable intelligence.