# Incident 001 — Repeated Failed Sudo Authentication

## Overview

| Field | Details |
|---|---|
| Date | 27 September 2026 |
| Environment | Isolated cybersecurity home lab |
| Host | Kali Linux |
| SIEM | Wazuh |
| Severity | High — Level 10 |
| Rule ID | 5404 |
| Detection | Three failed attempts to run sudo |
| User | kali |
| Target | root |
| Command | /usr/bin/whoami |

## Summary

A controlled authentication test was performed on the Kali Linux system. Three incorrect passwords were entered during a sudo authentication attempt.

Wazuh collected the authentication events through journald and correlated the failed attempts using rule 5404.

## Detection

Wazuh generated:

- Rule ID: 5404
- Level: 10
- Description: Three failed attempts to run sudo
- Groups: syslog, sudo

## Evidence

The Wazuh event recorded:

- Source user: kali
- Target user: root
- Command: `/usr/bin/whoami`
- Working directory: `/home/kali`
- TTY: `pts/0`
- Log source: journald
- Decoder: sudo

## Investigation

The surrounding events were reviewed to establish the timeline.

The investigation identified multiple password failures followed by the Level 10 correlation alert.

The activity was intentionally generated as part of the cybersecurity home lab and was not an uncontrolled attack.

## Impact

No unauthorized privilege escalation was identified from this controlled test.

The alert demonstrates that Wazuh can detect repeated failed sudo authentication attempts and assign a high-severity alert.

## Recommended Response

1. Verify whether the authentication attempts were legitimate.
2. Review surrounding authentication events.
3. Investigate any subsequent successful privilege escalation.
4. Review the affected account for additional suspicious activity.
5. Apply appropriate authentication controls if repeated failures occur in a production environment.

## MITRE ATT&CK

Wazuh associated the detection with:

- Privilege Escalation
- Defense Evasion

## Conclusion

The test successfully demonstrated end-to-end SOC detection:

Security Event → Log Collection → Wazuh Correlation → High-Severity Alert → Analyst Investigation