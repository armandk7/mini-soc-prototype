# Active Response

## Purpose

Wazuh Active Response was used to demonstrate containment of a detected SSH brute-force source.

## Validated Configuration

```xml
<active-response>
  <disabled>yes</disabled>
  <command>cowrie-firewall-drop</command>
  <location>local</location>
  <rules_id>100103</rules_id>
  <timeout>300</timeout>
</active-response>
```

## Response Flow

```text
Repeated failed SSH attempts
          |
          v
Wazuh Rule 100103
          |
          v
Wazuh Active Response
          |
          v
cowrie-firewall-drop
          |
          v
iptables / Firewall
          |
          v
Source IP blocked
```

## Testing Behavior

Automatic blocking is intentionally disabled during routine attack simulation so that repeated tests can be performed without immediately locking out the attacker.

During the containment demonstration, Active Response is enabled to verify that the detected source IP can be blocked through the firewall.

## Validation

The blocking mechanism was successfully demonstrated and the firewall state was restored after testing.
