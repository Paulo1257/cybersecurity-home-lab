# Incident 002 — File Integrity Modification

## Overview

| Field | Details |
|---|---|
| Date | 27 September 2026 |
| Environment | Isolated cybersecurity home lab |
| Host | Kali Linux |
| SIEM | Wazuh |
| Severity | Level 7 |
| Rule ID | 550 |
| Detection | Integrity checksum changed |
| Monitored directory | `/opt/wazuh-lab` |
| Affected file | `/opt/wazuh-lab/test.txt` |

## Summary

A controlled file modification was performed against a file monitored by Wazuh File Integrity Monitoring (FIM).

Wazuh detected the modification and generated Rule 550, indicating that the file's integrity checksum had changed.

## Detection

Wazuh generated:

- Rule ID: 550
- Level: 7
- Description: Integrity checksum changed
- Agent: Kali

## Investigation

The monitored file was:

`/opt/wazuh-lab/test.txt`

A controlled modification was made to the file to test the realtime FIM configuration.

Wazuh detected the change and recorded the integrity modification.

## Impact

The test was intentionally performed within the isolated cybersecurity laboratory.

No production or unauthorised system was affected.

In a real environment, an unexpected modification to a monitored file could indicate unauthorised configuration changes, malware activity, or another security event and would require investigation.

## Response

1. Verify whether the file modification was authorised.
2. Review the file's previous and current integrity information.
3. Identify the account or process responsible for the modification.
4. Review surrounding system activity.
5. Restore the file if the modification was unauthorised.
6. Continue monitoring for additional changes.

## Evidence

Wazuh Rule 550 demonstrated successful realtime File Integrity Monitoring.

Evidence is stored in:

`03_Wazuh_SIEM/02_FIM_Detection/`

## Conclusion

The test successfully demonstrated realtime File Integrity Monitoring using Wazuh.

The system detected a controlled modification to a monitored file and generated a Level 7 security alert.