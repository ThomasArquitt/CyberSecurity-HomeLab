# 12 — Wazuh SIEM & Detection Engineering

## Project Overview

This project documents the deployment and configuration of Wazuh as the central Security Information and Event Management (SIEM) platform within my cybersecurity home lab.

The objective was to build a functional security monitoring environment capable of collecting, analyzing, and correlating telemetry from multiple operating systems, network infrastructure, and custom applications.

Rather than relying exclusively on default Wazuh detections, I implemented custom detection rules, integrated a live SSH honeypot, configured Windows endpoint monitoring, and developed custom telemetry collection for a dedicated gaming server.

The environment supports hands-on experience with:

- Security Information and Event Management (SIEM)
- Endpoint detection and monitoring
- Windows Event Log analysis
- Sysmon telemetry
- PowerShell logging
- Network firewall log ingestion
- SSH honeypot monitoring
- Custom detection engineering
- JSON log parsing
- Security alert investigation
- Detection tuning and false-positive reduction
- Security monitoring across multiple operating systems

---

## 1. Infrastructure Architecture

### Wazuh Deployment

Wazuh is deployed as a dedicated Ubuntu Server virtual machine hosted on Proxmox VE.

The deployment uses a single-node architecture with the Wazuh Manager, Indexer, Dashboard, and Filebeat installed on the same virtual machine.

### Virtual Machine Specifications

| Component | Configuration |
|---|---|
| Hypervisor | Proxmox VE |
| Operating System | Ubuntu Server 26.04.1 LTS |
| Wazuh Version | 4.14.9 |
| Virtual CPUs | 4 |
| Memory | 8 GB |
| Virtual Disk | 80 GB |
| Network Placement | Dedicated Security VLAN |
| Deployment Model | Single-node |
| Management | SSH and Wazuh Web Dashboard |

### Installed Components

| Component | Version | Purpose |
|---|---|---|
| Wazuh Manager | 4.14.9 | Agent communication, event analysis, and detection rules |
| Wazuh Indexer | 4.14.9 | Security event indexing and storage |
| Wazuh Dashboard | 4.14.9 | Alert visualization, searching, and investigation |
| Filebeat | 7.10.2 | Forwards Wazuh alert data to the indexer |

All four services were verified as active during the October 2026 infrastructure review.

### Architecture Overview

```text
                    HOME LAB SECURITY NETWORK
                               |
                     FortiGate Firewall
                               |
                    Network Segmentation
                               |
                      Proxmox Hypervisor
                               |
                      Wazuh SIEM Server
                               |
             +-----------------+-----------------+
             |                 |                 |
       Wazuh Manager     Wazuh Indexer     Wazuh Dashboard
             |
             |
       Security Telemetry
             |
     +-------+------------+----------------+
     |                    |                |
 Windows 11 SOC       DShield/Cowrie   Windows Game Server
     |                    |                |
 Windows Events       SSH Activity     Windows Events
 Sysmon               Login Attempts   Palworld JSON
 PowerShell           Commands         Server Metrics
 Defender             Attack Patterns  Player Activity
     |
     +--------------------+
                          |
                   FortiGate Syslog
                          |
                   Firewall Events
```

The FortiGate sends syslog directly to the Wazuh Manager. The Windows and Linux endpoints communicate through their installed Wazuh agents.

---

## 2. Wazuh Agent Deployment

Three remote Wazuh agents are registered with the manager.

### Registered Endpoints

| Endpoint | Operating System | Primary Monitoring Purpose |
|---|---|---|
| WIN11-SOC-01 | Windows 11 Pro | Endpoint security telemetry and attack simulation |
| DSHIELD-01 | Ubuntu Server | Cowrie SSH honeypot monitoring |
| GAME-SERVER-01 | Windows | Windows security and custom Palworld telemetry |

All three agents were confirmed active during the October 2026 review.

The Wazuh Manager also maintains its own local manager entry.

### Agent Group Configuration

All three remote agents belong to the default Wazuh agent group.

```text
Agent Groups:
    default
        WIN11-SOC-01
        DSHIELD-01
        GAME-SERVER-01
```

The default centralized agent configuration currently contains no custom collection directives.

Endpoint-specific telemetry collection is configured locally through each agent's `ossec.conf` file.

### Agent Communication

The Wazuh Manager is configured to receive secure agent communications over TCP port 1514.

```xml
<remote>
  <connection>secure</connection>
  <port>1514</port>
  <protocol>tcp</protocol>
  <queue_size>131072</queue_size>
</remote>
```

Agent connectivity was verified using the Wazuh agent management utilities.

Example administrative command:

```bash
sudo /var/ossec/bin/agent_control -l
```

This provides visibility into registered agents and their connection status.

---

## 3. Windows 11 SOC Endpoint Integration

The Windows 11 SOC virtual machine functions as the primary endpoint for Windows security monitoring and attack simulation.

It is configured to generate and forward security-relevant Windows telemetry to Wazuh.

### Wazuh Agent

The Windows Wazuh agent was verified as running.

```powershell
Get-Service -Name WazuhSvc
```

### Configured Event Channels

Seven Windows Event Log channels are configured for collection.

| Event Channel | Monitoring Purpose |
|---|---|
| Application | Application failures and operational events |
| Security | Authentication, account activity, and security auditing |
| System | Windows services, drivers, and operating system activity |
| Microsoft-Windows-Sysmon/Operational | Detailed endpoint telemetry |
| Microsoft-Windows-PowerShell/Operational | PowerShell execution and operational activity |
| Microsoft-Windows-Windows Defender/Operational | Microsoft Defender detections and security events |
| PowerShellCore/Operational | PowerShell 7+ operational telemetry |

### Sysmon Integration

Microsoft Sysmon is installed on the Windows SOC endpoint.

The Sysmon64 service was verified as:

```text
Service: Sysmon64
Status: Running
Startup Type: Automatic
```

Sysmon provides additional endpoint telemetry beyond standard Windows Event Logs.

Depending on its configured event filters, Sysmon can record:

- Process creation
- Parent-child process relationships
- Network connections
- File creation activity
- Registry modifications
- Process access events
- Other security-relevant endpoint activity

Sysmon events are collected through:

```text
Microsoft-Windows-Sysmon/Operational
```

This telemetry supports investigations involving suspicious process execution, command-line activity, and potential attacker behavior.

### PowerShell Logging

The Windows SOC endpoint has the following PowerShell logging policies enabled:

| Logging Capability | Verified Configuration |
|---|---|
| Script Block Logging | Enabled |
| Module Logging | Enabled |
| Transcription | Not configured at the inspected policy path |

Script Block Logging helps capture PowerShell script content.

Module Logging provides additional visibility into execution activity for modules covered by its configuration.

These capabilities are particularly useful when investigating PowerShell-based attack techniques and simulated adversary activity.

### Microsoft Defender Integration

The Wazuh agent is configured to collect events from:

```text
Microsoft-Windows-Windows Defender/Operational
```

This provides a collection path for endpoint protection telemetry, including malware detections and other Defender security events.

### SOC Investigation Applications

The Windows telemetry environment supports investigation of:

- Suspicious process execution
- Parent-child process relationships
- PowerShell activity
- Authentication failures
- Account activity
- Endpoint protection detections
- Windows service activity
- Simulated adversary techniques

The endpoint is also used with Atomic Red Team and a custom AtomicSOC application.

Those components are documented separately.

---

## 4. DShield Honeypot Integration

A dedicated Ubuntu Server virtual machine hosts a DShield/Cowrie SSH honeypot.

The honeypot is designed to capture and record unsolicited SSH activity, including authentication attempts and commands submitted during attacker sessions.

### Honeypot Components

| Component | Purpose |
|---|---|
| Ubuntu Server | Honeypot operating system |
| Cowrie | SSH honeypot |
| DShield | Honeypot integration and attack telemetry |
| Wazuh Agent | Security event collection |
| JSON Logs | Structured Cowrie activity records |

### Cowrie Log Collection

Cowrie generates structured JSON events.

The Wazuh agent is configured to monitor:

```text
/srv/cowrie/var/log/cowrie/cowrie.json
```

The relevant agent configuration is:

```xml
<localfile>
  <log_format>json</log_format>
  <location>/srv/cowrie/var/log/cowrie/cowrie.json</location>
</localfile>
```

The JSON log file was verified as present and actively updated during the October 2026 review.

At the time of inspection, the file was approximately 22 MB.

### Verified Honeypot Activity

The following Cowrie event types were observed:

| Event Type | Description |
|---|---|
| cowrie.session.connect | Incoming SSH connection |
| cowrie.client.version | SSH client identification |
| cowrie.client.kex | SSH key exchange activity |
| cowrie.login.failed | Failed authentication attempt |
| cowrie.session.closed | SSH session termination |

Additional Cowrie event types are used by the custom detection rules described later in this document.

### Telemetry Pipeline

```text
External SSH Activity
         |
         v
   Cowrie Honeypot
         |
         v
   Structured JSON Log
         |
         v
      Wazuh Agent
         |
         v
      Wazuh Manager
         |
         v
    Custom Detection
         |
         v
      Wazuh Alerts
         |
         v
    SOC Investigation
```

### Additional Linux Monitoring

The DShield agent also collects operating system telemetry.

Configured sources include:

- System disk utilization
- Listening network ports
- Recent login activity
- systemd journal events
- Package management activity
- Wazuh active-response logs

These sources provide additional visibility into the Linux operating system hosting the honeypot.

---

## 5. FortiGate Firewall Integration

The FortiGate firewall forwards security and administrative events to the Wazuh Manager through syslog.

This provides centralized visibility into network infrastructure activity.

### Syslog Configuration

The Wazuh Manager is configured with a dedicated UDP syslog listener.

```xml
<remote>
  <connection>syslog</connection>
  <port>514</port>
  <protocol>udp</protocol>
  <allowed-ips>FIREWALL_SOURCE_IP</allowed-ips>
</remote>
```

`FIREWALL_SOURCE_IP` is a placeholder representing the authorized firewall syslog source.

The actual internal address is intentionally excluded from this public documentation.

### Verified Listener

The Wazuh Manager was verified as listening for UDP syslog traffic.

```bash
sudo ss -lunp
```

The Wazuh remote logging service was confirmed bound to UDP port 514.

### FortiGate Event Decoding

Received firewall events were decoded using Wazuh's built-in FortiGate decoder.

```text
fortigate-firewall-v5
```

### Verified Firewall Alerts

The October 2026 review confirmed firewall events reaching Wazuh.

Observed activity included:

| Event | Wazuh Rule | Severity |
|---|---|---|
| Firewall administrator login | 81626 | Level 3 |
| Firewall administrator logout | 81616 | Level 4 |
| Administrative session timeout | Observed in firewall event data | Varies by matching rule |

These events demonstrated successful firewall log forwarding, decoding, and alert generation.

### Security Monitoring Value

Centralized firewall logs can support investigations involving:

- Administrative access
- Authentication activity
- Firewall configuration changes, when logged
- Network policy activity, when logged
- Security-relevant traffic events, when logged
- Potential unauthorized access

The verified events establish administrative log ingestion. Additional firewall event categories depend on the FortiGate logging configuration.

---

## 6. Custom Detection Engineering

Custom Wazuh detection rules were implemented to identify specific behaviors within Cowrie honeypot events, reduce known Windows false positives, and process custom Palworld telemetry.

The custom rules are maintained in:

```text
/var/ossec/etc/rules/local_rules.xml
```

### Detection Rule Categories

The reviewed configuration contains 12 custom rule definitions.

| Category | Rules |
|---|---:|
| SSH authentication example | 1 |
| Cowrie honeypot detections | 6 |
| Windows false-positive suppression | 1 |
| Palworld telemetry detections | 4 |
| **Total** | **12** |

The SSH authentication example is a default-style sample rule and is not presented as a validated production detection.

### Cowrie Detection Rules

Six custom rules were configured to classify attacker activity observed by the honeypot.

| Rule ID | Level | Detection |
|---|---:|---|
| 100100 | 7 | Successful SSH authentication |
| 100101 | 8 | SSH command execution |
| 100102 | 10 | Suspicious executable or background execution patterns |
| 100103 | 10 | SSH persistence-related command patterns |
| 100104 | 11 | Payload download or file-transfer commands |
| 100105 | 12 | Payload download and execution patterns |

### Rule 100100 — Successful SSH Authentication

Detects Cowrie events matching:

```text
cowrie.login.success
```

A successful authentication event inside a honeypot can indicate that an attacker has progressed beyond unsuccessful credential attempts.

### Rule 100101 — Command Execution

Detects:

```text
cowrie.command.input
```

This provides visibility into commands submitted during honeypot sessions.

The rule also serves as a parent for more specific command-pattern detections.

### Rule 100102 — Suspicious Execution Patterns

Identifies command patterns associated with executable permission changes or background execution.

Examples include:

```text
chmod +x
nohup
```

These commands can be associated with preparing downloaded files for execution or launching processes that continue running after a session ends.

### Rule 100103 — SSH Persistence Patterns

Identifies command patterns associated with SSH key-based persistence.

Examples include references to:

```text
authorized_keys
.ssh/
ssh-rsa
ssh-ed25519
```

These patterns may indicate attempts to establish persistent SSH access.

A detection match indicates suspicious command content, not confirmed persistence.

### Rule 100104 — Payload Download Commands

Identifies commands involving common file-transfer utilities.

Examples include:

```text
wget
curl
ftp
tftp
```

These tools have legitimate administrative uses but are also frequently observed during automated attacks against exposed systems.

### Rule 100105 — Payload Download and Execution

Identifies command patterns associated with downloading content and executing it through a shell.

The detection is intended to identify higher-risk command combinations involving tools such as:

```text
wget
curl
sh
bash
```

A matching alert indicates that the command pattern was observed in honeypot telemetry.

It does not independently prove successful malware execution.

### Detection Hierarchy

The Cowrie rules use parent-child relationships to support more specific classifications.

```text
Cowrie JSON Event
       |
       v
Command Input Detection
     Rule 100101
       |
       +--------------------------+
       |                          |
       v                          v
Suspicious Execution       SSH Persistence
   Rule 100102               Rule 100103
       |
       +--------------------------+
                                  |
                                  v
                           Payload Download
                             Rule 100104
                                  |
                                  v
                         Download + Execution
                             Rule 100105
```

This diagram represents the intended detection categories.

The actual rule relationships are defined through Wazuh `if_sid` conditions, including rule 100105 inheriting from rule 100104.

### Observed Custom Detection Results

The October 2026 review analyzed the manager's current `alerts.json` file.

The following alert counts were verified:

| Rule ID | Detection | Observed Alerts |
|---|---|---:|
| 100100 | Successful SSH authentication | 110 |
| 100101 | Command execution | 1,548 |
| 100102 | Suspicious execution patterns | 4 |
| 100103 | SSH persistence patterns | 83 |
| 100104 | Payload download commands | 0 |
| 100105 | Download and execution patterns | 3 |
| **Total** | | **1,748** |

These results demonstrate that five of the six Cowrie detection rules generated alerts in the reviewed log file.

Rule 100104 did not appear as a separate alert in that file.

Because Wazuh can apply rule inheritance and select more specific matching rules, the absence of a standalone parent alert does not automatically indicate a detection failure.

### Alert Count Limitations

The counts above represent events found in the current Wazuh alert file at the time of inspection.

They are not lifetime totals.

Older events may exist in rotated logs or historical indexer data.

---

## 7. Windows Detection Tuning

A custom Windows detection rule was implemented to suppress a known benign event pattern.

### Rule 100200

| Property | Value |
|---|---|
| Rule ID | 100200 |
| Severity | Level 0 |
| Parent Rule | 92910 |
| Purpose | Suppress a known benign process-access pattern |

The rule targets a specific interaction involving Microsoft OneDrive and Windows Explorer.

### Detection Engineering Objective

The purpose of this rule is to reduce alert noise from an identified benign process interaction.

This supports an important SOC detection engineering principle:

> Effective security monitoring requires both identifying suspicious behavior and reducing unnecessary alerts.

### Operational Considerations

The suppression is narrowly scoped to a known process pattern.

Because level-0 suppression rules do not generate ordinary security alerts, their effectiveness should be evaluated through targeted testing and event review rather than alert counts alone.

The original rule includes a local Windows user profile path, which is intentionally excluded from this public document.

---

## 8. Custom Palworld Telemetry Integration

A separate Windows gaming server hosts a dedicated Palworld server.

Custom JSON telemetry was developed to provide additional visibility into server activity through Wazuh.

This integration demonstrates the ability to ingest security-relevant information from applications that do not provide native Wazuh integrations.

### Custom Log Collection

The Wazuh agent monitors JSON files matching:

```text
C:\GameServers\Palworld\Logs\wazuh-palworld-*.json
```

The agent uses the JSON log format.

```xml
<localfile>
  <log_format>json</log_format>
  <location>C:\GameServers\Palworld\Logs\wazuh-palworld-*.json</location>
</localfile>
```

### Configured Event Types

Four custom rules were created.

| Rule ID | Level | Event Type |
|---|---:|---|
| 100300 | 3 | server_metrics |
| 100301 | 5 | player_join |
| 100302 | 5 | player_leave |
| 100303 | 7 | server_restart |

The rules identify custom JSON events using fields including:

```text
source
event_type
```

### Verified Historical Detection Results

Historical Wazuh Dashboard records from September 2, 2026 confirmed successful detection of Palworld events.

Observed alerts included:

| Rule ID | Dashboard Description | Severity |
|---|---|---:|
| 100300 | Palworld server metrics received | Level 3 |
| 100301 | Palworld player joined server | Level 5 |
| 100302 | Palworld player left server | Level 5 |

These historical alerts confirm that the custom Palworld telemetry pipeline successfully generated Wazuh detections.

Rule 100303 is configured, but a corresponding historical alert was not verified during this review.

### Operational Status

The gaming server was powered down for an extended period before being brought back online in October 2026.

During the October review:

- The gaming server's Wazuh agent was connected.
- Standard Windows telemetry was reaching Wazuh.
- Custom Palworld JSON files were present.
- Historical Palworld alerts were located in the dashboard.
- No Palworld alerts appeared in the current manager alert file.

The lack of recent custom alerts is consistent with limited recent server activity.

The historical dashboard records establish that the custom integration operated successfully.

### Detection Engineering Value

This integration provided practical experience with:

- Custom application telemetry
- Structured JSON logging
- Wazuh log collection
- Custom rule development
- Event classification
- Application health monitoring
- Player activity monitoring
- Historical alert validation

---

## 9. Security Event Archiving

Wazuh is configured to archive received events in JSON format.

### Verified Configuration

```xml
<logall>no</logall>
<logall_json>yes</logall_json>
```

### Archiving Behavior

| Setting | Configuration |
|---|---|
| Plain-text event archiving | Disabled |
| JSON event archiving | Enabled |
| JSON archive file | Present |

The archived event file is located at:

```text
/var/ossec/logs/archives/archives.json
```

At the time of inspection, the current archive file was approximately 195 MB.

### Why Archiving Matters

Wazuh distinguishes between collected events and generated alerts.

An event may be received and archived without generating an alert.

JSON archiving provides a way to investigate telemetry that does not meet the configured alert-generation criteria.

Archived events are not automatically equivalent to indexed, searchable historical events. Dashboard availability depends on indexer and Filebeat configuration.

---

## 10. Detection Validation

The Wazuh analysis engine was validated using:

```bash
sudo /var/ossec/bin/wazuh-analysisd -t
```

### Validation Result

```text
Exit Code: 0
```

This confirmed that the installed rules and decoder configuration passed the analysis engine's configuration validation.

### Additional Verification

The following operational checks were performed:

| Verification | Result |
|---|---|
| Wazuh Manager service | Active |
| Wazuh Indexer service | Active |
| Wazuh Dashboard service | Active |
| Filebeat service | Active |
| Remote agent connectivity | 3 active agents |
| Windows SOC Wazuh service | Running |
| Sysmon service | Running |
| PowerShell logging policies | Enabled |
| Cowrie Wazuh agent | Active |
| Cowrie JSON file | Present and updated |
| FortiGate syslog listener | Active |
| FortiGate alerts | Verified |
| Cowrie custom detections | Verified |
| Palworld historical alerts | Verified |
| Wazuh rule configuration test | Passed |

### Validation Limitations

Successful configuration validation does not guarantee that every custom detection behaves exactly as intended.

Additional testing with representative sample events is necessary to validate individual detection conditions and rule inheritance.

---

## 11. Security Investigation Workflow

The Wazuh environment supports a structured approach to investigating security alerts.

### Investigation Process

```text
Security Event Generated
          |
          v
Telemetry Collected
          |
          v
Wazuh Decoding
          |
          v
Detection Rule Evaluation
          |
          v
Security Alert
          |
          v
Initial Triage
          |
          v
Event Context Review
          |
          v
Determine Scope and Severity
          |
          v
Document Findings
```

### Investigation Questions

When reviewing an alert, the primary questions include:

1. What activity triggered the alert?
2. Which endpoint or system generated the event?
3. When did the activity occur?
4. What process, account, or source was involved?
5. Is the activity expected or suspicious?
6. Are related events present before or after the alert?
7. Does the activity indicate a larger sequence of attacker behavior?
8. What additional evidence is required?
9. Does the event require containment, escalation, or further investigation?

### Example: Cowrie Command Execution

A Cowrie command execution alert can be investigated by examining:

- Event timestamp
- SSH session identifier
- Authentication activity
- Commands submitted during the session
- Related persistence or payload download detections
- Subsequent session activity

This supports investigation of attacker behavior rather than treating each alert as an isolated event.

---

## 12. Operational Administration

### Check Wazuh Services

```bash
sudo systemctl status wazuh-manager
sudo systemctl status wazuh-indexer
sudo systemctl status wazuh-dashboard
sudo systemctl status filebeat
```

### List Registered Agents

```bash
sudo /var/ossec/bin/agent_control -l
```

### List Agent Groups

```bash
sudo /var/ossec/bin/agent_groups -l
```

### Validate Detection Configuration

```bash
sudo /var/ossec/bin/wazuh-analysisd -t
```

### Inspect Wazuh Manager Logs

```bash
sudo journalctl -u wazuh-manager -n 100 --no-pager
```

### Review Recent Alerts

```bash
sudo tail -n 20 /var/ossec/logs/alerts/alerts.json
```

### Inspect Archived Events

```bash
sudo tail -n 20 /var/ossec/logs/archives/archives.json
```

### Verify Syslog Listener

```bash
sudo ss -lunp
```

These commands support routine administration, troubleshooting, and validation.

---

## 13. Challenges and Lessons Learned

### Detection Noise

Security monitoring generates large volumes of events, not all of which require investigation.

Custom rule tuning helped demonstrate the importance of distinguishing expected activity from potentially malicious behavior.

### Detection Specificity

General command execution events provide useful visibility, but more specific detections can help prioritize suspicious behaviors.

The Cowrie rule hierarchy was developed to identify patterns associated with execution, persistence, and payload delivery.

### Multiple Telemetry Formats

The environment required integrating several telemetry sources:

- Windows Event Channels
- Sysmon events
- PowerShell operational logs
- Linux journald
- Cowrie JSON
- FortiGate syslog
- Custom Palworld JSON

Working with these sources provided experience understanding how log format, decoding, and rule conditions affect security monitoring.

### Historical Event Validation

The Palworld integration demonstrated why a lack of current alerts does not necessarily indicate a broken detection.

Historical dashboard searches confirmed that custom Palworld rules had previously generated alerts, even though the current manager alert file contained no matching entries.

### Configuration Versus Operational Validation

A configured log source is not automatically proof of successful ingestion.

Likewise, a syntactically valid detection rule is not automatically proof of correct detection behavior.

This project reinforced the importance of verifying each stage of the telemetry and detection pipeline.

---

## 14. Current Limitations

The environment is operational as a learning and detection engineering platform, but several areas remain candidates for improvement.

| Area | Current State |
|---|---|
| Wazuh deployment | Single-node |
| Agent groups | All endpoints use the default group |
| Centralized agent configuration | No custom default-group collection directives |
| Cowrie detection rules | Five of six generated alerts in the reviewed file |
| Palworld detection rules | Three of four historically verified |
| Windows false-positive suppression | Configured; targeted effectiveness test not documented |
| Detection rule testing | Configuration validation completed; individual test cases not fully documented |
| Historical retention | Full retention policy not established during review |
| Alert notifications | Not documented as configured |
| Incident response automation | Not documented as configured |
| High availability | Not implemented |

These limitations provide opportunities for additional hands-on security engineering work.

---

## 15. Future Improvements

### Detection Engineering

- Develop additional Windows and Sysmon detection rules.
- Create controlled test cases for existing Cowrie detections.
- Validate rule inheritance and detection specificity.
- Improve detection coverage for common attacker techniques.
- Map custom detections to MITRE ATT&CK techniques where appropriate.

### Endpoint Monitoring

- Expand Windows event analysis.
- Validate additional Sysmon event categories.
- Investigate PowerShell execution patterns.
- Correlate Windows telemetry with Atomic Red Team simulations.

### SIEM Administration

- Evaluate dedicated Wazuh agent groups.
- Implement centralized agent configuration where beneficial.
- Review event retention and storage requirements.
- Develop additional dashboards.
- Establish repeatable detection validation procedures.

### Network Monitoring

- Expand FortiGate event analysis.
- Investigate firewall traffic patterns.
- Develop additional network-oriented detections.

### Incident Response

- Develop structured investigation playbooks.
- Document alert triage procedures.
- Evaluate appropriate automated response capabilities.
- Create repeatable incident investigation exercises.

---

## 16. Skills Demonstrated

This project demonstrates practical experience with:

**SIEM Administration**
- Wazuh deployment and service management
- Agent registration and connectivity
- Log collection configuration
- Security event archiving
- Dashboard investigation

**Detection Engineering**
- Custom Wazuh rule development
- Rule inheritance
- Regular-expression-based detections
- Alert severity classification
- False-positive suppression
- Detection validation

**Endpoint Security**
- Windows Event Log collection
- Sysmon integration
- PowerShell logging
- Microsoft Defender telemetry
- Linux system monitoring

**Network Security**
- FortiGate syslog integration
- Firewall administrative event monitoring
- Segmented security infrastructure

**Threat Monitoring**
- Cowrie SSH honeypot integration
- Authentication monitoring
- Suspicious command detection
- SSH persistence pattern detection
- Payload delivery pattern detection

**Security Investigation**
- Alert triage
- Event correlation
- Historical event searches
- Telemetry verification
- Detection troubleshooting

---

## 17. Project Outcome

Successfully deployed a centralized Wazuh SIEM platform supporting multiple endpoint types, firewall telemetry, honeypot monitoring, and custom application logging.

Key accomplishments include:

- Deployed a functioning single-node Wazuh SIEM environment.
- Connected three remote Wazuh agents.
- Configured seven Windows SOC event channels.
- Integrated Sysmon and PowerShell logging.
- Configured Cowrie JSON log ingestion.
- Integrated FortiGate firewall syslog.
- Created six custom Cowrie detection rules.
- Implemented a Windows false-positive suppression rule.
- Created four custom Palworld telemetry detection rules.
- Verified historical Palworld detections in the Wazuh Dashboard.
- Observed 1,748 custom Cowrie alerts in the reviewed manager alert file.
- Validated Wazuh detection configuration successfully.

The resulting environment provides a practical foundation for SOC analyst investigations, detection engineering exercises, and continued cybersecurity skill development.

---

## Related Documentation

- `01_Project_Overview.md` — Overall HomeLAB architecture
- `02_Network_Architecture.md` — Network segmentation and infrastructure
- `13_Windows_11_SOC_Endpoint.md` — Windows endpoint security configuration
- `14_AtomicSOC.md` — Atomic Red Team and custom attack simulation platform

---

**Project Status:** Operational — Continued Development

**Last Reviewed:** October 20, 2026
