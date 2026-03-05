# Network Forensic Analysis: SBT-PCAP2.pcapng

This repository documents the forensic investigation of a multi-stage cyber attack against a Windows host. The analysis focuses on initial access, credential harvesting, and post-exploitation reconnaissance using Wireshark.

## **Forensic Investigation**

### **Question 1: What is the WebAdmin password?**

* **Display Filter:** `http`
* **Methodology:**

1. Applied the `http` filter to focus on web traffic.

2. Used the **Find Packet** (Ctrl+F) tool.

3. Set search criteria to **String** and searched within **Packet bytes** for the keyword **"password"**.

4. Discovered an **HTTP GET** request for the file `/password.txt`.

5. Right-clicked the packet and selected **Follow > HTTP Stream** to reconstruct the conversation.




* **Screenshot:** `q1_find_packet_password.png`

**Description:** This screenshot illustrates the result of the "Find Packet" operation and the subsequent HTTP stream reconstruction. It reveals the server's response for `password.txt`, which explicitly displays the credentials.

**Answer:** `sbt123`

**Observation:** Storing administrative passwords in a publicly accessible plaintext file is a critical security oversight that allowed for immediate credential compromise.


---

### **Question 2: What is the version number of the attacker’s FTP server?**

* **Display Filter:** `ftp`
* **Methodology:**

1. Applied the `ftp` filter to isolate file transfer traffic.

2. Identified the initial connection handshake and server greeting packet.

3. Analyzed the **Response Code 220** (Banner), which servers use to identify their software version to clients.


**Screenshot:** `q2_ftp_banner_version.png`

**Description:** Wireshark packet details pane displaying the FTP server response showing the specific software name and version number.

**Answer:** `1.5.5`

**Observation:** The use of `pyftpdlib` suggests the attacker likely utilized a Python-based FTP server library to deliver malicious payloads.

---

### **Question 3: Which port was used to gain access to the victim Windows host?**

**Display Filter:** `tcp contains "ftp"`
**Methodology:**

1. Used the `tcp contains "ftp"` filter to identify the post-exploitation phase where the attacker initiated file transfers.

2. Identified a TCP stream where the command `ftp -A 192.168.56.1` was executed within a shell.

3. Followed the TCP stream backward to its origin to determine the initial session binding.

4. Confirmed the entry point by observing the Windows command prompt banner transmitted through the same session.


**Screenshot:** `q3_windows_shell_8081.png`

**Description:** Wireshark "Follow TCP Stream" output showing the Windows command prompt access (`Microsoft Windows [Version 6.1.7601]`) gained via the vulnerable service.

**Answer:** **`8081`** 

**Observation:** The victim was running **MiniShare**, a service known to listen on Port 8081. Attackers frequently exploit buffer overflows in this service to gain Remote Code Execution (RCE).

---

### **Question 4: What is the name of a confidential file on the Windows host?**

**Display Filter:** `smb`
**Methodology:**

1. Applied the `smb` filter to isolate Server Message Block traffic used for file system interactions.

2. Analyzed SMB session payloads to identify directory traversal and reconnaissance.

3. Located the packet sequence where a `dir` command was executed within the remote shell.

4. Inspected the resulting directory listing for high-value targets.


**Screenshot 1:** `q4_smb_desktop_recon.png`

**Description:** This screenshot captures the attacker navigating to the Desktop directory (`C:\Users\IEUser\Desktop>`) to begin scanning for sensitive files.


**Screenshot 2:** `q4_confidential_file_discovery.png`

**Description:** This image displays the results of the `dir` command, revealing a text file explicitly labeled as containing confidential information.

**Answer:** **`Employee_Information_CONFIDENTIAL.txt`** 

---

### **Question 5: What is the name of the log file that was created at 4:51 AM on the Windows host?**

**Display Filter:** `smb`
**Methodology:**

1. Applied the `smb` display filter and located **Packet 4187**, a key interaction in the SMB data stream.

2. Used **Follow -> TCP Stream** to reconstruct the communication in **SMB Stream 2075**.

3. Analyzed the directory listing for the Desktop folder.

4. Identified the specific file entry matching the timestamp **04:51 AM**.


**Screenshot:** `q5_smb_logfile_artifact.png`

**Description:** Reconstructed SMB stream showing the file system metadata for the Windows host, linking the specific filename to its creation timestamp.

**Answer:** **`LogFile.log`** 

**Observation:** Identifying file timestamps is critical for establishing a forensic timeline of attacker activity on the host.

---