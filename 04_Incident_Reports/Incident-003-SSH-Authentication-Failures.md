# Incident 003 — Repeated SSH Authentication Failures

## Overview

| Field | Details |
|---|---|
| Date | 27 September 2026 |
| Environment | Isolated cybersecurity home lab |
| Host | Kali Linux |
| SIEM | Wazuh |
| Severity | Level 10 |
| Rule ID | 2502 |
| Detection | User missed the password more than one time |
| Service | SSH |

## Summary

A controlled series of failed SSH authentication attempts was performed against the Kali Linux SSH service.

Wazuh collected the authentication events and generated a Level 10 alert under Rule 2502.

## Detection

Wazuh recorded multiple authentication-related events, including:

- SSH authentication failure
- PAM user login failure
- Password check failure
- Rule 2502 correlation alert

## Investigation

The surrounding authentication events were reviewed to establish the sequence of activity.

The failed authentication attempts were intentionally generated as part of the cybersecurity home lab.

The investigation focused on:

- Authentication failures
- SSH service activity
- Username involved
- Source information
- Event timestamps
- Wazuh rule severity

## Impact

The activity was performed against the user's own Kali Linux virtual machine within the isolated laboratory.

No unauthorised external system was targeted.

No successful unauthorised authentication was identified as part of this controlled test.

## Recommended Response

1. Verify whether the failed authentication attempts were authorised.
2. Identify the source of repeated authentication failures.
3. Review surrounding SSH authentication events.
4. Check for any successful login following the failures.
5. Investigate repeated attempts from unexpected sources.
6. Apply appropriate SSH authentication controls where necessary.

## Evidence

Wazuh detection and investigation screenshots are stored in:

`03_Wazuh_SIEM/03_SSH_Detection/`

## Conclusion

The test successfully demonstrated Wazuh's ability to detect and correlate repeated SSH authentication failures.

The resulting Level 10 alert provided an analyst with an event requiring investigation.