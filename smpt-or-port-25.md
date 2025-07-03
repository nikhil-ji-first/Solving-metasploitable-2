Penetration Testing Report:– Port 25 (SMTP)

Target: vulnerable machine
IP Address: 192.168.1.101
Port: 25 (SMTP)
Date: 15-06-2025
Tested By: [ NIKHIL ]

---

# 1. Port Scanning and Service Identification

Tool Used: nmap
```
 nmap -A -sV -p 25  192.168.56.101 
```

Output:
PORT   STATE SERVICE VERSION
25/tcp open  smtp    Postfix smtpd

Findings:
- Port 25 is open and running Postfix SMTP.
- The server is not using encryption (no SMTPS or STARTTLS detected).
- No authentication mechanism is immediately enforced.


# 2. Banner Grabbing and Enumeration

Tool Used: telnet, netcat, smtp-user-enum

```
 telnet 192.168.56.101 25 
```  
```
 nc 192.168.231.109 25
```
 It tries to connect to port 25 on the target IP. It’s often used for debugging, banner grabbing, or scripting network tests.
 
- 2.1 SMTP Enumeration    

```
smtp-user-enum -M VRFY -U /usr/share/seclists/Usernames/top-usernames-shortlist.txt -t 192.168.56.101
```
Vulnerability Identified:
User Enumeration via VRFY Command

- 2.1 Metasploit Module Used
 
```
msfconsole
auxiliary/scanner/smtp/smtp_enum    
use auxiliary/scanner/smtp/smtp_enum  
set RHOSTS 192.168.1.101   
set USER_FILE /usr/share/seclists/Usernames/top-usernames.txt    
run  
``` 
You will list all user/possible directory

# 4. Impact

 Information Disclosure: Attackers can determine valid system usernames.
 Facilitates Further Attacks: Usernames can be used in brute force  attacks.
 Misconfiguration: Lack of security controls such as disabling VRFY and EXPN commands.

# 5. Recommendations

1. Disable VRFY and EXPN Commands:
   Modify Postfix configuration (main.cf):
     disable_vrfy_command = yes
   Reload Postfix:
     service postfix reload

2. Implement Authentication and Encryption:
   Enable STARTTLS and SMTP AUTH to prevent unauthorized access.

3. Limit Access to SMTP Service:
   Use firewall rules to restrict port 25 to known, trusted IPs.

4. Monitor SMTP Logs:
   Regularly check logs for unusual activity and brute-force attempts.

# Summary

 Vulnerability  
- Lack of Encryption
- Open SMTP to the World
- VRFY User Enumeration

# Conclusion

The SMTP service presents multiple misconfigurations that allow user enumeration and insecure communication.   
While no direct remote code execution was discovered via port 25, the exposed information significantly aids in further penetration activities.
