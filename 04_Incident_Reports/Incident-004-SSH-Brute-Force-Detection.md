A controlled SSH authentication-failure simulation was performed within the isolated cybersecurity home lab.

Multiple incorrect passwords were entered during an SSH connection attempt to the Kali Linux system using:

```bash
ssh kali@localhost

The activity generated multiple SSH and PAM authentication-failure events. Wazuh successfully detected the repeated failures and generated a Level 10 alert using Rule ID 2502.
2. Detection
Field	Value
SIEM	Wazuh
Agent	Kali
Agent ID	001
Agent IP	192.168.7.128
Rule ID	2502
Severity	Level 10
Detection	syslog: User missed the password more than one time
Decoder	sshd
Log Source	journald
Username	kali
Source IP	::1
Rule Groups	syslog, access_control, authentication_failed


3. Attack Simulation
The authentication-failure scenario was generated using:
ssh kali@localhost

Incorrect credentials were deliberately entered multiple times.
The Kali system recorded messages including:
Failed password for kali from ::1

and:
PAM 2 more authentication failures

This was a controlled test against the local Kali system and was not performed against an external or unauthorised system.
4. Wazuh Detection
Wazuh generated the following high-severity detection:
Rule ID: 2502
Level: 10
Description: syslog: User missed the password more than one time

The alert was decoded using the SSH daemon (sshd) decoder and originated from the Kali agent's journald logs.
5. Supporting Events
Additional events were generated during the authentication-failure sequence:
Rule	Level	Description
5760	5	sshd: authentication failed
5557	5	unix_chkpwd: Password check failed
5503	5	PAM: User login failed
2502	10	syslog: User missed the password more than one time


Rule 2502 was treated as the primary detection because it generated the highest severity level during the controlled test.
6. Investigation
The Wazuh event contained the following relevant information:
- Agent: Kali
- Agent ID: 001
- Agent IP: 192.168.7.128
- Username: kali
- Source IP: ::1
- Decoder: sshd
- Log source: journald
- Rule ID: 2502
- Severity: Level 10
- Event category: Authentication failure
The source address ::1 represents the local IPv6 loopback address. This is consistent with the controlled localhost SSH test.
The Wazuh event also recorded:
PAM 2 more authentication failures; logname=uid=0 euid=0 tty=ssh ruser= rhost=::1 user=kali

7. Timeline
Time	Event
12:30:35	Password authentication failure recorded
12:30:38	Failed SSH password attempt
12:30:41	Failed SSH password attempt
12:30:43	Failed SSH password attempt
12:30:45	Additional authentication failures recorded
12:30:52	Wazuh generated Rule 2502 Level 10 detection


8. Impact
No unauthorised access occurred.
The activity was intentionally generated as part of a controlled cybersecurity lab exercise.
The test demonstrated that repeated SSH authentication failures can be collected by the Wazuh agent and detected by the Wazuh manager.
9. Response
The event was investigated using the Wazuh dashboard.
The investigation confirmed:
1. The authentication failures originated from the local system.
2. The affected account was kali.
3. Wazuh correctly identified the activity as repeated authentication failures.
4. Rule 2502 generated a Level 10 alert.
5. Supporting SSH, PAM and password-check events were also recorded.
No containment action was required because the activity was part of a controlled security test.
10. Recommendations
In a production environment, repeated SSH authentication failures should be investigated for potential brute-force activity.
Recommended controls include:
- Disable password-based SSH authentication where practical.
- Use SSH public-key authentication.
- Enforce strong authentication controls.
- Monitor repeated authentication failures through a SIEM.
- Restrict SSH access to trusted networks where possible.
- Implement rate limiting or account lockout controls where appropriate.
- Investigate unusual source IP addresses and authentication patterns.
11. Evidence
- [Wazuh Rule 2502 Detection](../03_Wazuh_SIEM/04_SSH_Brute_Force_Detection/01_Wazuh_Rule_2502.png)
- [SSH Failed Login Test](../03_Wazuh_SIEM/04_SSH_Brute_Force_Detection/02_SSH_Failed_Login_Test.png)
12. Conclusion
The controlled SSH authentication-failure test successfully demonstrated Wazuh's ability to detect repeated failed login attempts.
The Wazuh agent collected the SSH and PAM events through journald, and the Wazuh manager generated a Level 10 Rule 2502 alert.
This demonstrates practical experience with SIEM monitoring, authentication-event analysis, security alert investigation and incident documentation.