# Cowrie Log Collection

## Purpose

Wazuh Agent on the Cowrie VM reads Cowrie JSON telemetry and forwards the events to the Wazuh Manager for analysis.

## Validated Configuration

```xml
<localfile>
  <log_format>json</log_format>
  <location>/home/ubuntu/cowrie/var/log/cowrie/cowrie.json</location>
</localfile>
```

## Data Flow

```text
Cowrie
   |
   v
/home/ubuntu/cowrie/var/log/cowrie/cowrie.json
   |
   | Wazuh Agent <localfile>
   v
Wazuh Manager
   |
   v
Custom Detection Rules
```

## Notes

The configuration above was verified on the lab environment.

No credentials, API keys, or private secrets are included in this repository.
