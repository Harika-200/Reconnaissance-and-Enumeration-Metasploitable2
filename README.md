🔎 Reconnaissance & Enumeration Lab — Metasploitable2

"Kali Linux" (https://img.shields.io/badge/Platform-Kali%20Linux-557C94?logo=kalilinux&logoColor=white)
"Nmap" (https://img.shields.io/badge/Tool-Nmap-4682B4)
"Metasploit" (https://img.shields.io/badge/Tool-Metasploit-2596CD)
"Target" (https://img.shields.io/badge/Target-Metasploitable2-red)
"Status" (https://img.shields.io/badge/Environment-Authorized%20Lab-green)

📌 Project Overview

This project documents a reconnaissance and enumeration exercise performed against Metasploitable2, an intentionally vulnerable Linux virtual machine designed for cybersecurity training and penetration-testing practice.

The assessment was conducted from a Kali Linux attacker machine in an isolated VMware lab environment.

The objective was to identify exposed network services, determine service versions, enumerate SMTP-supported functionality and usernames, and assess SNMP exposure.

«Author: Y. Harika
Environment: Authorized cybersecurity lab
Target: Metasploitable2»

---

🎯 Objectives

The primary objectives of this lab were:

- Identify open network ports.
- Detect running services and their versions.
- Identify potentially outdated or vulnerable services.
- Perform SMTP service enumeration.
- Enumerate valid usernames through SMTP.
- Perform SNMP enumeration.
- Document the discovered attack surface.
- Map reconnaissance activities to relevant MITRE ATT&CK techniques.
- Provide security recommendations based on the findings.

---

🧪 Lab Environment

Component| Details
Attacker Machine| Kali Linux
Virtualization| VMware Workstation
Target Machine| Metasploitable2
Target IP| "192.168.116.130"
Hostname| "metasploitable.localdomain"
Testing Type| Network Reconnaissance & Enumeration
Environment| Authorized / Isolated Lab

---

🛠️ Tools Used

Kali Linux

Used as the attacker and security-testing workstation.

Nmap

Used for:

- Port scanning
- Service detection
- Version detection
- SMTP enumeration scripts

Metasploit Framework

Used for:

- SMTP user enumeration
- SNMP scanning
- Auxiliary reconnaissance modules

---

1. 🔍 Port Scanning & Service Version Detection

The following Nmap command was used to identify services and their versions:

nmap -sV -p 21,22,23,25,139,69,67,445,3389 192.168.116.130

Discovered Services

Port| Protocol| Service| Version / Information| Status
21| TCP| FTP| vsftpd 2.3.4| Open
22| TCP| SSH| OpenSSH 4.7p1 Debian 8ubuntu1| Open
23| TCP| Telnet| Linux telnetd| Open
25| TCP| SMTP| Postfix smtpd| Open
139| TCP| NetBIOS/SMB| Samba 3.X–4.X| Open
445| TCP| SMB| Samba 3.X–4.X| Open
67| UDP/TCP| DHCP| No service detected| Closed
69| UDP| TFTP| No service detected| Closed
3389| TCP| RDP| No service detected| Closed

Observations

The scan identified several exposed services that increase the attack surface of the target.

Notable observations include:

- FTP running "vsftpd 2.3.4".
- SSH running an older OpenSSH release.
- Telnet exposed on port "23".
- SMTP exposed on port "25".
- SMB/NetBIOS exposed on ports "139" and "445".

The environment is intentionally vulnerable, so these findings are expected within Metasploitable2.

---

2. 📧 SMTP Enumeration

SMTP enumeration was performed to identify supported SMTP functionality and determine whether the server disclosed valid usernames.

Nmap SMTP Enumeration

The following command was used:

nmap -p 25 --script smtp-commands,smtp-enum-users 192.168.116.130

Supported SMTP Commands

The server exposed functionality including:

- "VRFY"
- "ETRN"
- "STARTTLS"
- "8BITMIME"
- "DSN"

The presence of commands such as "VRFY" can potentially assist in username enumeration when improperly configured.

---

3. 🔐 SMTP User Enumeration with Metasploit

The Metasploit Framework was used to perform SMTP username enumeration.

Module

auxiliary/scanner/smtp/smtp_enum

Target

RHOSTS 192.168.116.130

The module successfully identified multiple valid system usernames.

Enumerated Usernames

backup
bin
daemon
distccd
ftp
games
gnats
irc
libuuid
list
lp
mail
man
mysql
news
nobody
postfix
postgres
postmaster
proxy
service
sshd
sync
sys
syslog
user
uucp
www-data

Security Impact

Successful username enumeration can disclose information about accounts present on a system.

An attacker could potentially use this information during later authorized security testing activities such as:

- Authentication security assessment
- Password-policy testing
- Privilege analysis
- Account exposure assessment

In a production environment, unnecessary account enumeration should be prevented where possible.

---

4. 📡 SNMP Enumeration

SNMP enumeration was also performed against the target.

The Metasploit Framework was used to identify relevant SNMP modules:

search auxiliary snmp

The following auxiliary module was selected:

auxiliary/scanner/snmp/snmp_login

The target was configured as:

RHOSTS 192.168.116.130

SNMP uses UDP port:

161

Result

The scan completed successfully but did not identify any positive SNMP community string matches or credentials.

Assessment

No valid community string was discovered during this enumeration attempt.

This demonstrates that reconnaissance does not always result in credential or configuration disclosure, and negative results are also important to document during a security assessment.

---

5. 📊 Security Findings

ID| Finding| Severity| Observation
F-01| Outdated FTP service| High| "vsftpd 2.3.4" identified
F-02| Telnet exposed| High| Cleartext remote administration protocol
F-03| Outdated SSH version| Medium| OpenSSH "4.7p1" identified
F-04| SMTP username enumeration| Medium| Multiple valid usernames disclosed
F-05| SMB exposed| Medium| Ports "139" and "445" accessible
F-06| SNMP community enumeration| Informational| No valid community string discovered
F-07| RDP exposure| Informational| Port "3389" was closed

«Severity ratings are contextual to the deliberately vulnerable lab environment and should not be treated as production risk ratings without additional validation.»

---

6. 🧠 MITRE ATT&CK Mapping

Technique| ID| Activity
Network Service Scanning| T1046| Nmap port and service discovery
Account Discovery| T1087| SMTP username enumeration
Active Scanning| T1595| Reconnaissance against the lab target

These mappings demonstrate how network reconnaissance and enumeration activities can be associated with techniques used during real-world attack chains.

---

7. 🛡️ Security Recommendations

Based on the observations from the lab, the following defensive measures are recommended for production environments:

FTP

- Remove FTP where it is not required.
- Replace legacy FTP with secure alternatives such as SFTP.
- Upgrade unsupported software versions.
- Disable unnecessary anonymous access.

Telnet

- Disable Telnet.
- Use SSH for secure remote administration.
- Restrict administrative access through firewall rules or VPN.

SSH

- Keep OpenSSH regularly patched.
- Disable weak authentication mechanisms.
- Prefer key-based authentication.
- Restrict SSH access to authorized networks.

SMTP

- Disable unnecessary SMTP commands such as user-verification mechanisms where appropriate.
- Prevent unauthenticated account enumeration.
- Apply rate limiting and monitoring.
- Keep the mail server patched.

SMB

- Disable SMB versions that are no longer required.
- Restrict SMB access through network segmentation and firewall rules.
- Use strong authentication and access controls.
- Monitor unusual SMB activity.

SNMP

- Avoid default community strings.
- Use SNMPv3 where supported.
- Restrict SNMP access to trusted management systems.
- Monitor unauthorized SNMP requests.

---

8. 📸 Evidence / Screenshots

Screenshots from the lab can be stored in the "screenshots/" directory.

Nmap Service Scan

"Nmap Service Scan" (screenshots/nmap-service-scan.png)

SMTP Enumeration

"SMTP Enumeration" (screenshots/smtp-enumeration.png)

SNMP Enumeration

"SNMP Enumeration" (screenshots/snmp-enumeration.png)

«Replace the screenshot filenames above with the actual filenames uploaded to the repository.»

---

9. 📄 Detailed Report

The complete step-by-step evidence and lab documentation is available in the Word report:

📄 "Reconnaissance & Enumeration Report" (report/Reconnaissance_Enumeration_Report.docx)

Place your Word document inside the "report" directory using the filename shown above, or update the README link to match your actual filename.

---

10. 📁 Repository Structure

Reconnaissance-and-Enumeration-Metasploitable2/
│
├── README.md
│
├── report/
│   └── Reconnaissance_Enumeration_Report.docx
│
└── screenshots/
    ├── nmap-service-scan.png
    ├── smtp-enumeration.png
    └── snmp-enumeration.png

---

11. 💡 Skills Demonstrated

This project demonstrates practical experience with:

- Network reconnaissance
- Port scanning
- Service enumeration
- Service version detection
- Nmap
- Nmap NSE scripts
- Metasploit Framework
- SMTP enumeration
- Username enumeration
- SNMP reconnaissance
- Linux security testing
- Vulnerability identification
- Attack-surface analysis
- MITRE ATT&CK mapping
- Security documentation
- Penetration-testing methodology

---

12. 🎓 Learning Outcomes

Through this exercise, I gained practical experience in:

1. Performing structured network reconnaissance.
2. Identifying exposed network services.
3. Detecting service versions using Nmap.
4. Understanding the security implications of legacy protocols.
5. Performing SMTP enumeration.
6. Identifying valid usernames through enumeration techniques.
7. Performing SNMP reconnaissance.
8. Documenting both positive and negative enumeration results.
9. Connecting offensive security activities with MITRE ATT&CK techniques.
10. Translating technical findings into defensive recommendations.

---

⚠️ Disclaimer

This project was performed only against an intentionally vulnerable Metasploitable2 virtual machine in an authorized and controlled lab environment.

The techniques and tools documented here are intended for:

- Cybersecurity education
- Authorized penetration testing
- Security research
- Defensive security training
- Lab environments

Do not perform scanning, enumeration, or penetration testing against systems that you do not own or do not have explicit authorization to test.

---

👩‍💻 Author

Y. Harika

Cybersecurity | SOC | VAPT | Network Security

Areas of Interest

- Security Operations Center (SOC)
- Vulnerability Assessment & Penetration Testing (VAPT)
- SIEM & Security Monitoring
- Network Security
- Web Application Security
- Incident Detection & Response
- Threat Detection
- MITRE ATT&CK

---

⭐ If you found this project useful, consider starring the repository.
