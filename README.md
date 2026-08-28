# Blue Team Lab — Cowrie + Wazuh

> Hands-on Blue Team lab for SSH attack simulation, honeypot telemetry, detection, correlation, alerting, and tested containment.

## Project Overview

This project demonstrates a practical Blue Team workflow using Cowrie as an SSH/Telnet honeypot and Wazuh as the detection and response platform.

The lab simulates attacker activity from Kali Linux, captures the activity through Cowrie telemetry, analyzes the events with Wazuh, and validates a response mechanism that can block the attacker source IP through the firewall.

## Architecture

```text
Kali Linux (Attacker)
        |
        | SSH attack
        v
Cowrie Honeypot
        |
        | JSON telemetry
        v
Wazuh Agent
        |
        v
Wazuh Manager
   |         |         |
Detection  Correlation  Dashboard
        |
        v
Wazuh Active Response
        |
        v
iptables / Firewall
        |
        v
Source IP blocked
```

See [`architecture/`](architecture/) for the project architecture reference.

## Attack Scenarios

### 1. Successful SSH Login
An attacker connects to the Cowrie honeypot using SSH. Wazuh detects the successful authentication event with rule `100100`.

### 2. Command Execution
After authentication, the attacker executes commands inside the emulated shell. Wazuh detects Cowrie command events with rule `100102`.

### 3. SSH Brute Force
Repeated failed SSH authentication attempts from the same source IP are correlated by Wazuh rule `100103`.

## Detection Rules

| Rule | Detection | Level |
|---|---|---:|
| `100100` | Successful SSH login | 8 |
| `100101` | Failed SSH login | 7 |
| `100102` | Command execution | 7 |
| `100103` | SSH brute-force correlation | 12 |

Rule `100103` correlates 3 matching failed-login events from the same `src_ip` within 60 seconds.

The validated rule set is available in [`detection/rules-cowrie.xml`](detection/rules-cowrie.xml).

## Data Collection

Cowrie generates JSON telemetry that is monitored by the Wazuh Agent through a `<localfile>` configuration.

```xml
<localfile>
  <log_format>json</log_format>
  <location>/home/ubuntu/cowrie/var/log/cowrie/cowrie.json</location>
</localfile>
```

See [`setup/cowrie-log-collection.md`](setup/cowrie-log-collection.md).

## Active Response

The project also validates Wazuh Active Response for brute-force containment.

```text
Failed SSH attempts
        |
        v
Rule 100103
        |
        v
Active Response
        |
        v
cowrie-firewall-drop
        |
        v
iptables
        |
        v
Source IP blocked
```

Automatic blocking is kept disabled during routine attack simulation so that repeated testing can continue without locking out the attacker. Active Response is enabled during the containment demonstration to verify the blocking action.

See [`response/active-response.md`](response/active-response.md).

## Evidence

The repository contains screenshots captured from the validated lab:

- Successful SSH detection — Rule `100100`
- Command execution detection — Rule `100102`
- Brute-force correlation — Rule `100103`
- Raw Cowrie event

See [`evidence/`](evidence/).

## Validation

The following behaviors were validated during the lab:

| Test | Result |
|---|---|
| Successful SSH detection | Passed |
| Command execution detection | Passed |
| Brute-force correlation | Passed |
| Active Response containment | Passed |
| Firewall cleanup after testing | Passed |

See [`validation/validation-results.md`](validation/validation-results.md).

## Repository Structure

```text
mini-soc-v2/
├── architecture/
├── detection/
├── evidence/
├── response/
├── scenarios/
├── setup/
├── validation/
├── README.md
├── SECURITY.md
└── .gitignore
```

## Security Notes

Do not commit passwords, API keys, tokens, private keys, `.env` files, or other sensitive data.

Credentials exposed during development should be rotated before publishing the repository.

## Project Status

**Status:** Completed lab validation

**Focus:** Cowrie telemetry, Wazuh detection, event correlation, alerting, and tested containment.
