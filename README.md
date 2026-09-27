# Cybersecurity Home Lab & SOC Detection Environment

## Overview

This project documents a controlled cybersecurity home lab built to develop practical skills in:

- Security monitoring
- SIEM administration
- Network reconnaissance
- Vulnerability assessment
- Web application security testing
- File Integrity Monitoring
- Authentication monitoring
- Security event investigation
- Incident documentation

All security testing was performed within an isolated virtualised laboratory environment.

---

## Lab Architecture

The environment consists of three virtual machines:

| System | Purpose | IP Address |
|---|---|---|
| Kali Linux | Security testing and monitoring agent | 192.168.100.129 |
| Metasploitable2 | Intentionally vulnerable target | 192.168.100.128 |
| Wazuh | SIEM and security monitoring | 192.168.100.130 |

The virtual machines communicate through an isolated VMware VMnet2 network.

```text
                    Windows 11 Host
                         |
                  VMware Workstation
                         |
                 Isolated VMnet2
                 192.168.100.0/24
                         |
          +--------------+--------------+
          |              |              |
          v              v              v
       Kali          Metasploitable2    Wazuh
     .100.129           .100.128       .100.130
       |                   |              |
       |                   |              |
       +------ Security Testing ----------+
                           |
                          DVWA
Technologies
Virtualisation
VMware Workstation Pro
VMware Host-Only Networking
Operating Systems
Kali Linux
Metasploitable2
Wazuh virtual appliance
Security Tools
Wazuh
Nmap
Gobuster
Nikto
Wireshark
DVWA
1. Network Reconnaissance

Network reconnaissance was performed against the intentionally vulnerable Metasploitable2 machine.

Nmap Service Enumeration

Nmap was used to identify open ports and running services.

nmap -sV -T3 192.168.100.128

The scan identified multiple exposed services and provided service/version information for further investigation.

Vulnerability Enumeration

Nmap vulnerability scripts were also used to identify known vulnerabilities associated with exposed services.

nmap -sV --script vuln 192.168.100.128

Evidence:

2. Web Application Security Testing

DVWA running on Metasploitable2 was used as a controlled environment for web application security testing.

SQL Injection

A SQL injection test demonstrated that user input could alter the underlying database query.

Evidence:

Blind SQL Injection

Boolean-based SQL injection testing was performed using true and false conditions to observe differences in application responses.

Evidence:

Cross-Site Scripting

Both reflected and stored XSS were tested within DVWA.

Evidence:

Command Injection

Controlled command injection testing demonstrated that operating system commands could be executed through the vulnerable application.

Evidence:

Local File Inclusion

A controlled Local File Inclusion test successfully accessed /etc/passwd through the vulnerable file inclusion parameter.

Evidence:

File Upload

Testing demonstrated that the application accepted a non-image file and subsequently made the uploaded file accessible through the web server.

Evidence:

3. Wazuh SIEM

Wazuh was deployed as the SIEM and security monitoring platform.

The Kali Linux system was enrolled as a Wazuh agent and configured to send security events to the Wazuh manager.

Sudo Authentication Detection

A controlled test generated multiple failed sudo authentication attempts.

Wazuh correlated the events using:

Rule 5404 — Three failed attempts to run sudo

Severity:

Level 10

Evidence:

File Integrity Monitoring

Wazuh File Integrity Monitoring was configured to monitor:

/opt/wazuh-lab

A controlled file modification generated:

Rule 550 — Integrity checksum changed

Severity:

Level 7

Evidence:

SSH Authentication Monitoring

Repeated failed SSH authentication attempts were generated against the Kali Linux SSH service.

Wazuh generated:

Rule 2502

Severity:

Level 10

Evidence:
4. SOC Investigation Methodology

The lab was used to practise a simplified SOC workflow:

Security Event
      |
      v
Log Collection
      |
      v
Wazuh Detection
      |
      v
Alert Investigation
      |
      v
Timeline Analysis
      |
      v
Impact Assessment
      |
      v
Incident Documentation
      |
      v
Remediation Recommendations

The investigations focused on identifying:

What happened
Which system was affected
Which account was involved
Which command or process was involved
When the event occurred
Why the alert was generated
Whether the activity was expected
What response would be appropriate
5. Incident Reports

Detailed investigation reports are stored in:

04_Incident_Reports/

Current investigations include:

Repeated failed sudo authentication
File integrity modification
Repeated SSH authentication failures
6. Security Principles Demonstrated

This project demonstrates practical experience with:

Network reconnaissance
Service enumeration
Vulnerability identification
Web application security
Authentication monitoring
SIEM alert analysis
File Integrity Monitoring
Log analysis
Incident investigation
Security documentation
Defensive security monitoring

Disclaimer

This project was conducted in an isolated personal cybersecurity laboratory using intentionally vulnerable systems.

Testing was restricted to systems owned or controlled within the laboratory environment.

No unauthorised systems were targeted.


