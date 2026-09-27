# Cybersecurity Home Lab

A practical cybersecurity home lab demonstrating **network reconnaissance, web application security testing, vulnerability analysis, SIEM monitoring, file integrity monitoring, authentication monitoring, and incident investigation**.

The lab was built using VMware Workstation with an isolated virtual network containing Kali Linux, Metasploitable2, and Wazuh.

---

## Lab Architecture

```text
                    ┌─────────────────────────┐
                    │       Windows 11        │
                    │        Host PC          │
                    │                         │
                    │ Ryzen 7 5800X           │
                    │ RTX 3070 Ti             │
                    │ 16 GB RAM               │
                    └────────────┬────────────┘
                                 │
                         VMware Workstation
                                 │
                    ┌────────────┴────────────┐
                    │      VMnet2              │
                    │  192.168.100.0/24       │
                    │     Isolated Lab        │
                    └──────┬──────┬──────┬────┘
                           │      │      │
                           │      │      │
                    ┌──────▼──┐ ┌─▼──────┐ ┌──────▼──────┐
                    │  Kali   │ │Metaspl. │ │   Wazuh     │
                    │ Linux   │ │2        │ │   SIEM       │
                    │         │ │         │ │              │
                    │.129     │ │.128     │ │.130          │
                    └─────────┘ └─────────┘ └──────────────┘
```

### Virtual Machines

| System          |        IP Address | Purpose                                 |
| --------------- | ----------------: | --------------------------------------- |
| Kali Linux      | `192.168.100.129` | Security testing and reconnaissance     |
| Metasploitable2 | `192.168.100.128` | Intentionally vulnerable target         |
| Wazuh           | `192.168.100.130` | SIEM, security monitoring and detection |

The offensive testing was performed against the intentionally vulnerable Metasploitable2 system inside the isolated lab environment.

---

## Technologies

* **Kali Linux**
* **Metasploitable2**
* **Wazuh SIEM**
* **VMware Workstation**
* **Nmap**
* **Gobuster**
* **Nikto**
* **cURL**
* **Wireshark**
* **DVWA**
* **Linux SSH**
* **Linux sudo**
* **File Integrity Monitoring (FIM)**
* **Git & GitHub**

---

# 01 — Network Reconnaissance

The first stage of the lab focused on identifying exposed services and potential vulnerabilities on the Metasploitable2 target.

### Nmap Service Enumeration

A service-version scan was performed against the target:

```bash
nmap -sV -T3 192.168.100.128
```

This identified numerous services running on the target, including FTP, SSH, HTTP, Telnet, SMB, MySQL and other legacy services.

### Top-Port Enumeration

```bash
nmap -sV --top-ports 20 192.168.100.128
```

### Vulnerability Enumeration

```bash
nmap -sV --script vuln 192.168.100.128
```

The vulnerability scan identified several known weaknesses associated with the deliberately vulnerable Metasploitable2 operating system and services.

### Evidence

- [Nmap Service Scan](01_Network_Recon/01_Nmap/01_Nmap_Service_Scan.png)
- [Nmap Vulnerability Scan](01_Network_Recon/01_Nmap/02_Nmap_Vulnerability_Scan.png)
- [Nmap Top 20 Services](01_Network_Recon/01_Nmap/03_Nmap_Top20_Services.png)
---

# 02 — Web Enumeration

Web server reconnaissance was performed against the HTTP service.

### HTTP Enumeration

```bash
nmap -p 80 --script http-title,http-headers 192.168.100.128
```

### Directory Enumeration

Gobuster was used to identify accessible directories and resources:

```bash
gobuster dir -u http://192.168.100.128 \
-w /usr/share/wordlists/dirb/common.txt
```

### Web Vulnerability Scanning

Nikto was used to identify common web server misconfigurations and vulnerabilities.

The enumeration identified resources including:

* `/phpinfo.php`
* `/phpMyAdmin/`
* `/dav/`
* `/test/`
* `/tikiwiki/`

Additional findings included directory indexing, missing security headers, HTTP TRACE/XST exposure indicators, outdated Apache/PHP components and exposed information through `phpinfo.php`.

### Evidence

* [Gobuster Directory Enumeration](01_Network_Recon/02_Web_Enumeration/01_Gobuster_Directory_Enumeration.png)

---

# 03 — DVWA Web Application Security Testing

The **Damn Vulnerable Web Application (DVWA)** was used to demonstrate common web application vulnerabilities in a controlled environment.

Testing was performed against the intentionally vulnerable application hosted within the isolated lab.

---

## SQL Injection

A SQL injection vulnerability was tested using:

```text
' OR '1'='1' #
```

A UNION-based SQL injection was also used to demonstrate database information disclosure.

The testing exposed database information including:

```text
root@localhost
dvwa
```
### Evidence

- [SQL Injection Evidence](02_DVWA/01_SQL_Injection/02_SQL%20INJECTION.png)
## Blind SQL Injection

Boolean-based testing was performed using true and false conditions to demonstrate how application behaviour can reveal database information.

### Evidence

* [Blind SQL Injection — True](02_DVWA/02_Blind_SQL_Injection/01_Blind_SQLi_True.png)
* [Blind SQL Injection — False](02_DVWA/02_Blind_SQL_Injection/02_Blind_SQLi_False.png)

---

## Cross-Site Scripting (XSS)

### Reflected XSS

The following payload was tested:

```html
<script>alert('XSS Test')</script>
```

### Stored XSS

A persistent JavaScript payload was stored within the application to demonstrate stored XSS behaviour.
### Evidence

- [XSS Evidence](./02_DVWA/3-%20XSS/01_XSS.png)
- [Stored XSS Evidence](./02_DVWA/3-%20XSS/02_XSS.png)

---
## Command Injection

Command injection was demonstrated using:

```text
127.0.0.1; whoami
```

The application executed the injected command as:

```text
www-data
```

The following command was also tested:

```text
127.0.0.1; id
```

This returned:

```text
uid=33(www-data)
```

### Evidence

* [Command Injection Evidence](02_DVWA/04_Command_Injection/01_Command_injection.png)

---

## File Inclusion

Local File Inclusion was demonstrated by accessing the Linux password file:

```text
../../../../../etc/passwd
```

This successfully exposed the contents of `/etc/passwd`.

### Evidence

* [File Inclusion Evidence](02_DVWA/05_File_Inclusion/01_File_inclusion.png)

---

## File Upload

The file upload functionality was tested using a controlled text file.

The application accepted the file and made it accessible through the application's upload directory.

### Evidence

* [File Upload Test](02_DVWA/06_File_Upload/01_file_upload.png)
* [Uploaded File Evidence](02_DVWA/06_File_Upload/02_File_upload.png)

---

# 04 — Wazuh SIEM

Wazuh was deployed as the SIEM and security monitoring platform for the lab.

The Wazuh manager was hosted at:

```text
192.168.100.130
```

The Kali Linux machine was enrolled as a Wazuh agent:

```text
Agent ID: 001
Agent Name: Kali
```

The agent was configured to collect system logs and security events.

---

## Sudo Failed Authentication Detection

A controlled sudo authentication test was performed by intentionally entering an incorrect password multiple times.

Wazuh generated:

```text
Rule ID: 5404
Level: 10
Description: Three failed attempts to run sudo
```

The event recorded details including:

* User: `kali`
* Target: `root`
* Command: `/usr/bin/whoami`
* Working directory: `/home/kali`
* TTY: `pts/0`
* Log source: `journald`

### Evidence

* [Wazuh Rule 5404 Detection](03_Wazuh_SIEM/01_Sudo_Detection/01_Wazuh_Sudo_Rule_5404.png)
* [Sudo Incident Investigation](03_Wazuh_SIEM/01_Sudo_Detection/02_Wazuh_Sudo_Incident.png)
* [Sudo Event Timeline](03_Wazuh_SIEM/01_Sudo_Detection/03_Wazuh_Sudo_Timeline.png)

---

## File Integrity Monitoring (FIM)

Wazuh File Integrity Monitoring was configured to monitor:

```text
/opt/wazuh-lab
```

A controlled modification was then made to:

```text
/opt/wazuh-lab/test.txt
```

Wazuh detected the modification and generated:

```text
Rule ID: 550
Level: 7
Description: Integrity checksum changed
```

This demonstrates the ability to detect unauthorised or unexpected file modifications.

### Evidence

* [Wazuh Rule 550 Detection](03_Wazuh_SIEM/02_FIM_Detection/01_Wazuh_FIM_Rule_550.png)
* [FIM File Modification Investigation](03_Wazuh_SIEM/02_FIM_Detection/02_Wazuh_FIM_File_Change.png)

---

## SSH Authentication Monitoring

SSH was enabled on the Kali system and controlled failed authentication attempts were generated.

Wazuh detected the repeated failures and generated:

```text
Rule ID: 2502
Level: 10
Description: SSH authentication failures
```

The event demonstrated how repeated authentication failures can be identified and investigated through a SIEM.

### Evidence

* [Wazuh Rule 2502 Detection](03_Wazuh_SIEM/03_SSH_Detection/01_Wazuh_SSH_Rule_2502.png)
* [SSH Authentication Investigation](03_Wazuh_SIEM/03_SSH_Detection/02_Wazuh_SSH_Investigation.png)

---

# 05 — SOC Detection Summary

| Detection                    | Wazuh Rule | Level | Security Relevance                                    |
| ---------------------------- | ---------: | ----: | ----------------------------------------------------- |
| Sudo Authentication Failures |     `5404` |    10 | Detects repeated failed privilege escalation attempts |
| File Integrity Change        |      `550` |     7 | Detects changes to monitored files                    |
| SSH Authentication Failures  |     `2502` |    10 | Detects repeated failed SSH authentication            |

These detections demonstrate core SOC monitoring activities including **authentication monitoring, privilege escalation detection, file integrity monitoring and security event investigation**.

---

# 06 — Incident Reports

Detailed incident reports were created for the Wazuh detections.

### Incident 001 — Sudo Failed Authentication

[View Incident-001-Sudo-Failed-Authentication.md](04_Incident_Reports/Incident-001-Sudo-Failed-Authentication.md)

**Detection:** Wazuh Rule `5404`
**Severity:** Level 10
**Category:** Authentication / Privilege Escalation

---

### Incident 002 — FIM File Modification

[View Incident-002-FIM-File-Modification.md](04_Incident_Reports/Incident-002-FIM-File-Modification.md)

**Detection:** Wazuh Rule `550`
**Severity:** Level 7
**Category:** File Integrity Monitoring

---

### Incident 003 — SSH Authentication Failures

[View Incident-003-SSH-Authentication-Failures.md](04_Incident_Reports/Incident-003-SSH-Authentication-Failures.md)

**Detection:** Wazuh Rule `2502`
**Severity:** Level 10
**Category:** Authentication Monitoring

---

# 07 — Skills Demonstrated

This project demonstrates practical experience in:

* Network reconnaissance
* Service enumeration
* Vulnerability identification
* Web application security testing
* SQL injection
* Blind SQL injection
* Cross-Site Scripting
* Command injection
* Local File Inclusion
* File upload testing
* Linux security
* SSH authentication monitoring
* Privilege escalation detection
* File Integrity Monitoring
* SIEM configuration
* Security event analysis
* Incident investigation
* Incident reporting
* Git and GitHub
* Virtualised cybersecurity lab development

---

# 08 — Project Structure

```text
Cybersecurity-Home-Lab/
│
├── 01_Network_Recon/
│   ├── 01_Nmap/
│   └── 02_Web_Enumeration/
│
├── 02_DVWA/
│   ├── 01_SQL_Injection/
│   ├── 02_Blind_SQL_Injection/
│   ├── 3-XSS/
│   ├── 04_Command_Injection/
│   ├── 05_File_Inclusion/
│   └── 06_File_Upload/
│
├── 03_Wazuh_SIEM/
│   ├── 01_Sudo_Detection/
│   ├── 02_FIM_Detection/
│   └── 03_SSH_Detection/
│
├── 04_Incident_Reports/
│   ├── Incident-001-Sudo-Failed-Authentication.md
│   ├── Incident-002-FIM-File-Modification.md
│   └── Incident-003-SSH-Authentication-Failures.md
│
└── README.md
```

---

# 09 — Lab Objectives

The main objectives of this project were to:

1. Build an isolated cybersecurity testing environment.
2. Perform network and service reconnaissance.
3. Identify vulnerabilities on an intentionally vulnerable system.
4. Perform controlled web application security testing.
5. Deploy and configure Wazuh as a SIEM.
6. Configure endpoint monitoring using a Wazuh agent.
7. Generate controlled security events.
8. Investigate SIEM detections.
9. Document findings using incident reports.
10. Build a professional cybersecurity portfolio demonstrating practical SOC and security-testing skills.

---

## Disclaimer

This project was conducted in an **isolated home lab environment** using intentionally vulnerable systems and applications.

All security testing was performed against systems owned or controlled for educational purposes.

No unauthorised systems were targeted.
