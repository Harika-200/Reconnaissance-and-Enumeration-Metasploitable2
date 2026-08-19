
🔎 Reconnaissance & Enumeration — Metasploitable2

"Kali Linux" (https://img.shields.io/badge/Platform-Kali%20Linux-557C94?logo=kalilinux&logoColor=white)
"Nmap" (https://img.shields.io/badge/Tool-Nmap-4682B4)
"Metasploit" (https://img.shields.io/badge/Tool-Metasploit-2596CD)
"Cybersecurity" (https://img.shields.io/badge/Domain-Cybersecurity-red)
"Authorized Lab" (https://img.shields.io/badge/Environment-Authorized%20Lab-green)

«Network Reconnaissance, Service Enumeration, SMTP Username Enumeration & SNMP Security Testing»

📌 Project Overview

This project documents a network reconnaissance and enumeration exercise performed against Metasploitable2, an intentionally vulnerable Linux virtual machine, in an authorized and isolated cybersecurity laboratory environment.

The assessment focused on identifying exposed network services, detecting service versions, performing SMTP enumeration, testing SMTP username enumeration using Metasploit, performing SNMP reconnaissance, analyzing the resulting attack surface, and mapping selected activities to the MITRE ATT&CK framework.

Author: Y. Harika
Target: Metasploitable2
Testing Type: Network Reconnaissance & Enumeration
Environment: Authorized / Isolated Cybersecurity Lab

---

🎯 Objectives

The main objectives of this lab were to:

- Identify open network ports.
- Detect running services and their versions.
- Identify potentially outdated or insecure services.
- Perform SMTP service enumeration.
- Identify valid usernames through SMTP enumeration.
- Perform SNMP reconnaissance.
- Analyze the exposed network attack surface.
- Document both positive and negative enumeration results.
- Map reconnaissance activities to relevant MITRE ATT&CK techniques.
- Provide defensive security recommendations.

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

Used as the security-testing workstation for conducting network reconnaissance and enumeration.

Nmap

Used for:

- Port scanning
- Service discovery
- Service version detection
- SMTP reconnaissance
- SMTP enumeration scripts

Metasploit Framework

Used for:

- SMTP username enumeration
- SNMP reconnaissance
- Auxiliary module discovery
- Authorized security-testing activities

---

🔍 1. Port Scanning & Service Version Detection

The following Nmap command was used to identify services and their versions on selected ports:

nmap -sV -p 21,22,23,25,139,69,67,445,3389 192.168.116.130

Discovered Services

Port| Protocol| Service| Version / Information| Status
21| TCP| FTP| vsftpd 2.3.4| Open
22| TCP| SSH| OpenSSH 4.7p1 Debian 8ubuntu1| Open
23| TCP| Telnet| Linux telnetd| Open
25| TCP| SMTP| Postfix smtpd| Open
139| TCP| NetBIOS/SMB| Samba| Open
445| TCP| SMB| Samba| Open
67| UDP/TCP| DHCP| No service detected| Closed
69| UDP| TFTP| No service detected| Closed
3389| TCP| RDP| No service detected| Closed

Key Observations

The reconnaissance scan identified several services that significantly increase the target's attack surface.

Notable observations included:

- vsftpd 2.3.4 running on FTP port 21.
- An older OpenSSH 4.7p1 release on port 22.
- Telnet exposed on port 23.
- SMTP/Postfix exposed on port 25.
- SMB/NetBIOS exposed on ports 139 and 445.
- No service was detected on the tested DHCP, TFTP, and RDP ports.

«Note: Metasploitable2 is intentionally vulnerable, so outdated services and insecure configurations are expected in this environment.»

---

📧 2. SMTP Enumeration

SMTP reconnaissance was performed against port 25 to identify supported SMTP functionality and determine whether the service disclosed valid usernames.

Nmap SMTP Enumeration

The following command was used:

nmap -p 25 --script smtp-commands,smtp-enum-users 192.168.116.130

SMTP Functionality Identified

The SMTP service exposed functionality including:

- "VRFY"
- "ETRN"
- "STARTTLS"
- "8BITMIME"
- "DSN"

The presence of the "VRFY" command can potentially assist with username enumeration when a mail server is improperly configured.

---

🔐 3. SMTP Username Enumeration with Metasploit

The Metasploit Framework was used to perform SMTP username enumeration.

Module

auxiliary/scanner/smtp/smtp_enum

Target

RHOSTS 192.168.116.130

The module successfully identified multiple valid usernames on the intentionally vulnerable target.

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

In a real environment, exposed account information could potentially support subsequent authorized security assessments involving:

- Authentication security
- Password-policy assessment
- Account exposure analysis
- Privilege analysis
- Identity and access management

Organizations should avoid unnecessary account enumeration through externally accessible services.

---

📡 4. SNMP Enumeration

SNMP reconnaissance was also performed against the target.

The Metasploit Framework was used to search for relevant SNMP auxiliary modules:

search auxiliary snmp

The following module was selected:

auxiliary/scanner/snmp/snmp_login

Target Configuration

RHOSTS 192.168.116.130

SNMP Port

SNMP commonly uses:

UDP/161

Result

The enumeration attempt completed successfully but did not identify any positive SNMP community-string matches or credentials.

Assessment

No valid SNMP community string was discovered during this enumeration attempt.

This is an important assessment result because security testing should document both successful and unsuccessful findings.

---

📊 5. Security Findings

ID| Finding| Severity| Observation
F-01| Outdated FTP service| High| vsftpd 2.3.4 identified
F-02| Telnet exposed| High| Cleartext remote administration protocol
F-03| Outdated SSH version| Medium| OpenSSH 4.7p1 identified
F-04| SMTP username enumeration| Medium| Multiple valid usernames disclosed
F-05| SMB exposed| Medium| Ports 139 and 445 accessible
F-06| SNMP community enumeration| Informational| No valid community string discovered
F-07| RDP exposure| Informational| Port 3389 was closed

«Note: Severity ratings are contextual to the intentionally vulnerable laboratory environment. Production risk ratings would require additional validation, business context, exploitability analysis, and asset criticality assessment.»

---

🧠 6. MITRE ATT&CK Mapping

Technique| ID| Activity
Network Service Scanning| T1046| Nmap port and service discovery
Account Discovery| T1087| SMTP username enumeration
Active Scanning| T1595| Reconnaissance against the authorized lab target

These mappings demonstrate how reconnaissance and enumeration activities can be associated with techniques used during real-world attack chains.

---

🛡️ 7. Security Recommendations

The following recommendations are applicable to production environments.

FTP

- Disable FTP when it is not required.
- Replace legacy FTP with secure alternatives such as SFTP.
- Keep file-transfer software patched.
- Restrict access to authorized systems.
- Disable unnecessary anonymous access.

Telnet

- Disable Telnet wherever possible.
- Use SSH for secure remote administration.
- Restrict administrative access through firewalls or VPNs.

SSH

- Keep OpenSSH regularly patched.
- Disable weak authentication mechanisms.
- Prefer strong key-based authentication.
- Restrict SSH access to authorized networks.

SMTP

- Disable unnecessary SMTP commands such as user-verification mechanisms where appropriate.
- Prevent unauthenticated account enumeration.
- Implement rate limiting and monitoring.
- Keep mail-server software patched.

SMB

- Restrict SMB access to trusted networks.
- Disable legacy SMB protocols that are no longer required.
- Apply strong authentication and access controls.
- Monitor unusual SMB activity.

SNMP

- Avoid default or weak community strings.
- Prefer SNMPv3 where supported.
- Restrict SNMP access to trusted management systems.
- Monitor unauthorized SNMP requests.

---

📈 8. Attack Surface Summary

The reconnaissance phase demonstrated that the target exposed multiple network services.

Service| Port| Protocol| Security Consideration
FTP| 21| TCP| Legacy/outdated service
SSH| 22| TCP| Older software version
Telnet| 23| TCP| Cleartext administration
SMTP| 25| TCP| Username enumeration
NetBIOS/SMB| 139| TCP| Network file-sharing exposure
SMB| 445| TCP| Network service exposure

The results demonstrate why systematic reconnaissance is an important first phase of a security assessment.

---

🧰 9. Skills Demonstrated

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
- Attack-surface analysis
- MITRE ATT&CK mapping
- Security documentation
- Penetration-testing methodology
- Defensive security recommendations

---

🎓 10. Learning Outcomes

Through this exercise, I gained practical experience in:

- Performing structured network reconnaissance.
- Identifying exposed network services.
- Detecting service versions using Nmap.
- Understanding the security implications of legacy protocols.
- Performing SMTP reconnaissance.
- Identifying valid usernames through SMTP enumeration.
- Performing SNMP reconnaissance using Metasploit.
- Documenting successful and unsuccessful enumeration results.
- Connecting offensive security activities with MITRE ATT&CK techniques.
- Translating technical findings into defensive recommendations.

---

📄 11. Detailed Lab Report

The complete step-by-step evidence, commands, screenshots, and execution details are available in the accompanying Word report.

📄 "Reconnaissance & Enumeration Report" (Reconnaissance_Enumeration_Report.docx)

The report contains supporting evidence for:

- Nmap service reconnaissance
- Service version detection
- SMTP enumeration
- SMTP Metasploit auxiliary-module activity
- SMTP username enumeration
- SNMP auxiliary-module activity
- SNMP configuration and execution
- Security findings
- MITRE ATT&CK mapping
- Security recommendations

---

📁 12. Repository Structure

Reconnaissance-and-Enumeration-Metasploitable2/
│
├── README.md
└── Reconnaissance_Enumeration_Report.docx

The repository intentionally contains the detailed Word report rather than separate screenshot files.

---

⚠️ Disclaimer

This project was performed only against an intentionally vulnerable Metasploitable2 virtual machine in an authorized and controlled laboratory environment.

The techniques and tools documented in this project are intended for:

- Cybersecurity education
- Authorized penetration testing
- Security research
- Defensive security training
- Intentionally vulnerable laboratory environments

Do not perform scanning, enumeration, vulnerability testing, or penetration testing against systems that you do not own or do not have explicit authorization to test.

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
