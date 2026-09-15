# Wazuh SIEM — SSH Brute-Force Detection & Incident Investigation Report

## 1. Incident Overview

This report documents a controlled security testing scenario performed in a lab environment using Wazuh SIEM.

The purpose of the test was to generate repeated failed SSH authentication attempts, verify that Wazuh detected the activity, investigate the generated alerts, and perform basic containment and cleanup.

The activity was performed only on the author's own virtual machines in an isolated lab environment.

---

## 2. Lab Environment

| Component            | Details      |
| -------------------- | ------------ |
| SIEM                 | Wazuh        |
| Server VM            | Ubuntu Linux |
| Server Hostname      | ashu         |
| Server IP            | 192.168.64.3 |
| Client VM            | Ubuntu Linux |
| Client Hostname      | clint        |
| Client IP            | 192.168.64.4 |
| Wazuh Manager        | Server VM    |
| Wazuh Dashboard      | Server VM    |
| Wazuh Agent          | Client VM    |
| Attack/Test Protocol | SSH          |

---

## 3. Objective

The objectives of this test were:

* Generate failed SSH authentication events.
* Confirm that the Wazuh agent collected the events.
* Verify that Wazuh generated security alerts.
* Investigate the source, target account, and affected endpoint.
* Map the activity to MITRE ATT&CK techniques.
* Perform basic containment.
* Remove the temporary test account after the investigation.
* Verify that the Wazuh services and agent remained operational.

---

## 4. Security Test

A temporary test account named `siem-test` was created on the client VM.

The SSH service was then used to generate multiple failed authentication attempts against the test account.

The test was performed locally on the client VM using SSH:

```bash
ssh siem-test@127.0.0.1
```

Incorrect credentials were intentionally entered multiple times.

The SSH service eventually returned:

```text
Permission denied, please try again.
Permission denied (publickey,password).
```

This generated authentication-related events that were collected by the Wazuh agent.

---

## 5. Detection

Wazuh detected the authentication failures and generated multiple security alerts.

### Rule 2502 — Repeated Password Failure

* Rule ID: `2502`
* Severity: Level 10
* Description: Repeated password failure
* MITRE ATT&CK: `T1110` — Brute Force

This alert indicated repeated authentication failures associated with the test activity.

### Rule 5760 — SSH Authentication Failure

* Rule ID: `5760`
* Severity: Level 5
* Description: SSH authentication failed
* MITRE ATT&CK: `T1110.001` — Password Guessing
* MITRE ATT&CK: `T1021.004` — SSH

This alert provided SSH-specific authentication failure information.

### Rule 5503 — PAM Login Failure

* Rule ID: `5503`
* Severity: Level 5
* Description: PAM user login failed
* MITRE ATT&CK: `T1110.001` — Password Guessing

This alert provided additional evidence from the PAM authentication subsystem.

---

## 6. Investigation

The generated alerts were reviewed in the Wazuh Dashboard.

The investigation identified:

* Affected endpoint: `clint`
* Agent ID: `001`
* Client IP: `192.168.64.4`
* Target account: `siem-test`
* Authentication protocol: SSH
* Source address observed in the event: `127.0.0.1`
* Activity type: Repeated failed authentication attempts

The source address was `127.0.0.1` because the SSH test was intentionally performed from the client VM against its own SSH service.

The alerts showed repeated authentication failures, but no successful login was observed during the controlled test.

---

## 7. Containment

To demonstrate a basic containment action, the temporary test account was locked:

```bash
sudo passwd -l siem-test
```

The account status was checked using:

```bash
passwd -S siem-test
```

The account was then unable to authenticate through SSH.

This demonstrated a basic account-level containment response after detecting suspicious authentication activity.

---

## 8. Cleanup

After completing the investigation and evidence collection, the temporary test account was removed:

```bash
sudo userdel -r siem-test
```

The test environment was then returned to its normal state.

---

## 9. MITRE ATT&CK Mapping

| Technique            | ID        | Relevance                        |
| -------------------- | --------- | -------------------------------- |
| Brute Force          | T1110     | Repeated authentication attempts |
| Password Guessing    | T1110.001 | Failed password authentication   |
| Remote Services: SSH | T1021.004 | SSH authentication activity      |

---

## 10. Service Validation

After completing the test, the main Wazuh components were checked.

### Wazuh Manager

```bash
sudo systemctl is-active wazuh-manager
```

Result:

```text
active
```

### Wazuh Dashboard

```bash
sudo systemctl is-active wazuh-dashboard
```

Result:

```text
active
```

### Wazuh Agent

On the client VM:

```bash
sudo systemctl is-active wazuh-agent
```

Result:

```text
active
```

The Wazuh agent connectivity was also verified from the Server VM.

The agent list showed both the Server VM and Client VM as active.

---

## 11. Evidence

The following screenshots are included in the project repository:

1. Wazuh Dashboard Overview
2. Rule 2502 — Brute Force Detection
3. Rule 5760 — SSH Authentication Failure
4. Rule 5503 — PAM Login Failure
5. Wazuh Agent Connectivity
6. Wazuh Manager Service Status
7. Wazuh Dashboard Service Status
8. Wazuh Agent Service Status

These screenshots provide visual evidence of the detection and validation steps performed during the lab.

---

## 12. Outcome

The controlled test successfully demonstrated a basic SIEM monitoring and incident-response workflow:

```text
Security Event
      ↓
Wazuh Agent Collection
      ↓
Wazuh Detection Rules
      ↓
Security Alerts
      ↓
Dashboard Investigation
      ↓
MITRE ATT&CK Mapping
      ↓
Account Containment
      ↓
Cleanup
      ↓
Service Validation
```

The project demonstrates practical experience with Wazuh, Linux authentication logs, SSH security monitoring, alert investigation, basic incident response, and MITRE ATT&CK mapping.

---

## 13. Skills Demonstrated

* Wazuh SIEM
* Security event monitoring
* Linux system administration
* SSH security monitoring
* Authentication log analysis
* PAM event analysis
* Alert investigation
* Brute-force detection
* MITRE ATT&CK mapping
* Basic incident containment
* Incident documentation
* SIEM-based security monitoring

---

## 14. Disclaimer

This project was performed in a controlled personal lab environment using virtual machines owned and managed by the author.

No unauthorized systems, accounts, or networks were targeted.
