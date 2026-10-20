# 13 — Windows 11 SOC Endpoint

## Project Overview

This project documents the deployment and security configuration of a Windows 11 virtual machine designed to function as a monitored endpoint within my cybersecurity home lab.

The endpoint provides a controlled environment for generating, collecting, and investigating Windows security telemetry.

It is integrated with Wazuh SIEM and supports adversary simulation using Atomic Red Team and a custom Python-based application called AtomicSOC.

The primary objective is to develop practical experience with Windows endpoint security, event logging, detection engineering, and SOC investigations.

### Learning Objectives

- Deploy and administer a Windows endpoint in a virtualized environment.
- Configure Windows security auditing and telemetry collection.
- Implement Microsoft Sysmon for enhanced endpoint visibility.
- Enable PowerShell execution logging.
- Integrate Microsoft Defender telemetry.
- Forward security events to Wazuh SIEM.
- Generate controlled security events using Atomic Red Team.
- Investigate endpoint activity using SIEM telemetry.
- Develop familiarity with attacker techniques and defensive detection capabilities.

---

## 1. Infrastructure

### Virtual Machine Configuration

The Windows SOC endpoint is hosted on Proxmox VE.

| Component | Configuration |
|---|---|
| Hypervisor | Proxmox VE |
| Virtual Machine | WIN11-SOC-01 |
| Operating System | Windows 11 Pro |
| Architecture | 64-bit |
| Windows Build | 26200 |
| Virtual CPUs | 4 |
| Allocated Memory | 8 GB |
| Reported Memory | 7.9 GB |
| Virtual Disk | 80 GB |
| Network | Dedicated Security VLAN |
| Remote Administration | PowerShell over SSH |
| SIEM Integration | Wazuh |

The virtual machine is isolated within the lab's segmented security network.

Network access is governed by the FortiGate firewall and the associated VLAN policies.

Internal IP addresses, VLAN identifiers, and management endpoints are intentionally excluded from this public documentation.

---

## 2. Endpoint Security Architecture

The Windows SOC endpoint combines native Windows security capabilities with additional telemetry collection and attack simulation tools.

### Installed Components

| Component | Purpose |
|---|---|
| Windows 11 Pro | Endpoint operating system |
| Microsoft Defender Antivirus | Native endpoint protection |
| Microsoft Sysmon | Enhanced endpoint event logging |
| PowerShell Logging | Script and module execution visibility |
| Wazuh Agent | Centralized security telemetry collection |
| Atomic Red Team | Controlled adversary technique simulation |
| AtomicSOC | Custom attack simulation interface |

### Architecture

```text
                  Windows 11 SOC Endpoint
                            |
           +----------------+----------------+
           |                |                |
     Windows Events       Sysmon         PowerShell
           |                |                |
           +----------------+----------------+
                            |
                     Wazuh Agent
                            |
                            v
                      Wazuh Manager
                            |
                            v
                     Detection Rules
                            |
                            v
                      Wazuh Indexer
                            |
                            v
                     Wazuh Dashboard
                            |
                            v
                     SOC Investigation
```

Microsoft Defender contributes additional endpoint protection telemetry through its Windows Event Log channel.

Atomic Red Team and AtomicSOC generate controlled activity that can be investigated through the monitoring pipeline.

---

## 3. Microsoft Defender Configuration

Microsoft Defender Antivirus provides native Windows endpoint protection.

The endpoint's protection status was verified using PowerShell.

### Verification Command

```powershell
Get-MpComputerStatus |
    Select-Object AMServiceEnabled,
                  AntivirusEnabled,
                  RealTimeProtectionEnabled,
                  BehaviorMonitorEnabled,
                  IoavProtectionEnabled
```

### Verified Results

| Protection Component | Status |
|---|---|
| Antivirus Service | Enabled |
| Antivirus Protection | Enabled |
| Real-Time Protection | Enabled |
| Behavior Monitoring | Enabled |
| IOAV Protection | Enabled |

All five checked protection components were enabled during the October 2026 review.

### Windows Service

The Microsoft Defender service was verified as:

```text
Service: WinDefend
Status: Running
Startup Type: Automatic
```

### Security Monitoring Value

Microsoft Defender provides protection and telemetry related to:

- Malware detection
- Suspicious file activity
- Real-time scanning
- Behavioral monitoring
- Downloaded file scanning
- Endpoint protection events

Defender operational events are configured for collection by the Wazuh agent.

---

## 4. Microsoft Sysmon Integration

Microsoft Sysmon is installed to provide additional visibility into Windows endpoint activity.

Sysmon supplements native Windows logging by recording detailed system events according to its active configuration.

### Verified Service Configuration

```text
Service: Sysmon64
Status: Running
Startup Type: Automatic
```

The Sysmon configuration was also confirmed to exist within the Windows registry.

### Sysmon Event Channel

```text
Microsoft-Windows-Sysmon/Operational
```

This channel is included in the Wazuh agent's configured event sources.

### Security Monitoring Applications

Depending on the active Sysmon configuration, Sysmon can provide telemetry involving:

- Process creation
- Parent-child process relationships
- Command-line execution
- Network connections
- File creation
- Registry modifications
- Process access activity

These events are particularly valuable for detecting suspicious execution patterns and investigating potential attacker behavior.

### Parent-Child Process Analysis

Understanding process relationships is an important SOC investigation skill.

For example:

```text
explorer.exe
     |
     v
powershell.exe
     |
     v
cmd.exe
```

A process tree helps an analyst understand how an executable was launched and whether the execution chain is expected.

The example above is illustrative and does not represent a confirmed malicious event from this endpoint.

### Configuration Validation

The Sysmon service and configuration presence were verified.

The exact active Sysmon event filters were not enumerated during this documentation review.

Therefore, individual event categories should be validated separately before claiming specific detection coverage.

---

## 5. PowerShell Security Logging

PowerShell logging is configured to provide visibility into script execution and command activity.

This is important because PowerShell is both a legitimate administration tool and a commonly abused component in Windows attack chains.

### Verified Logging Policies

| Logging Feature | Configuration |
|---|---|
| Script Block Logging | Enabled |
| Module Logging | Enabled |
| Transcription | Not configured at the inspected policy path |

### Script Block Logging

Script Block Logging records PowerShell script content.

The following policy setting was verified:

```text
EnableScriptBlockLogging = 1
```

This provides additional visibility into PowerShell activity, including potentially suspicious script execution.

### Module Logging

Module Logging was also enabled.

```text
EnableModuleLogging = 1
```

Module Logging can record pipeline execution details for configured PowerShell modules.

The specific module selection was not enumerated during this review.

### PowerShell Transcription

PowerShell transcription was not configured at the registry policy path inspected during verification.

This does not prevent Script Block Logging or Module Logging from functioning.

### Wazuh Collection

The endpoint is configured to collect:

```text
Microsoft-Windows-PowerShell/Operational
PowerShellCore/Operational
```

These channels provide collection paths for PowerShell-related telemetry.

### Investigation Applications

PowerShell logging supports investigations involving:

- Suspicious command execution
- Encoded commands
- Script execution
- Reconnaissance activity
- System enumeration
- Potential misuse of administrative tools

---

## 6. Wazuh Agent Integration

The Windows endpoint is integrated with the Wazuh SIEM environment.

### Verified Agent Service

```text
Service: WazuhSvc
Status: Running
Startup Type: Automatic
```

The agent was also confirmed as active from the Wazuh Manager.

### Agent Configuration

The Windows agent configuration is maintained locally at:

```text
C:\Program Files (x86)\ossec-agent\ossec.conf
```

### Configured Windows Event Channels

Seven Windows Event Log channels are configured for collection.

| Event Channel | Purpose |
|---|---|
| Application | Application and software events |
| Security | Authentication and security auditing |
| System | Operating system and service events |
| Microsoft-Windows-Sysmon/Operational | Enhanced endpoint telemetry |
| Microsoft-Windows-PowerShell/Operational | Windows PowerShell activity |
| Microsoft-Windows-Windows Defender/Operational | Endpoint protection events |
| PowerShellCore/Operational | PowerShell Core activity |

### Event Collection Example

```xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

This example illustrates how the Wazuh agent is configured to monitor a Windows Event Log channel.

### Telemetry Pipeline

```text
Windows Security Activity
          |
          v
Windows Event Logs
          |
          v
Wazuh Agent
          |
          v
Wazuh Manager
          |
          v
Event Decoding and Rules
          |
          v
Security Alerts
          |
          v
Wazuh Dashboard
```

The integration allows endpoint activity to be investigated centrally rather than relying exclusively on local Windows logs.

---

## 7. Atomic Red Team Installation

Atomic Red Team is installed on the Windows SOC endpoint.

### Verified Installation Directory

```text
C:\AtomicRedTeam
```

Atomic Red Team provides controlled tests designed to simulate adversary techniques.

These tests can be used to generate endpoint activity for defensive monitoring and investigation.

### Previously Validated Techniques

The following Atomic Red Team tests were previously executed successfully within the lab:

| MITRE ATT&CK Technique | Technique ID | Test |
|---|---|---|
| Process Discovery | T1057 | Atomic Test 2 |
| File Deletion | T1070.004 | Atomic Test 4 |
| System Network Configuration Discovery | T1016 | Atomic Test 1 |

These tests support practical experience with endpoint telemetry generation and investigation.

### Security Monitoring Objective

The purpose of executing Atomic Red Team tests is not simply to run commands.

The objective is to understand:

1. What behavior the test simulates.
2. Which Windows components record the activity.
3. Whether the telemetry reaches Wazuh.
4. Whether a detection rule generates an alert.
5. What evidence an analyst can use during investigation.

Successful test execution does not automatically mean the activity generated a corresponding Wazuh alert.

Detection coverage must be validated separately.

---

## 8. AtomicSOC Integration

AtomicSOC is a custom application developed to provide a browser-based interface for controlled Atomic Red Team testing.

The application is hosted on the Windows SOC endpoint.

### Verified Directories

```text
C:\AtomicSOC
C:\AtomicSOC\web
C:\AtomicSOC\web\static
```

### Application Components

The project uses:

- Python
- FastAPI
- A browser-based frontend
- Atomic Red Team test execution
- Randomized test selection

### Previously Validated Functionality

The application has successfully executed Atomic Red Team tests through its interface.

A randomized test feature was also implemented and tested.

The feature selects an available test and presents the selection before execution.

### Integration Objective

AtomicSOC provides a repeatable way to generate endpoint activity for SOC investigation exercises.

```text
AtomicSOC Web Interface
          |
          v
      Python Backend
          |
          v
    Atomic Red Team
          |
          v
  Windows Endpoint Activity
          |
          v
   Windows Security Logs
          |
          v
       Wazuh SIEM
```

The complete AtomicSOC implementation is documented separately in:

`14_AtomicSOC.md`

---

## 9. Remote Administration

The Windows SOC endpoint is administered primarily through PowerShell over SSH.

This approach provides a command-line-based administration workflow without requiring routine direct-console access.

### Administrative Tasks

PowerShell is used to:

- Inspect Windows services
- Verify security settings
- Review event logging configuration
- Manage application files
- Execute controlled security tests
- Troubleshoot monitoring components

### Example Commands

Check Wazuh:

```powershell
Get-Service WazuhSvc
```

Check Sysmon:

```powershell
Get-Service Sysmon64
```

Check Microsoft Defender:

```powershell
Get-Service WinDefend
```

Verify Defender protection:

```powershell
Get-MpComputerStatus
```

Inspect recent Sysmon events:

```powershell
Get-WinEvent -LogName 'Microsoft-Windows-Sysmon/Operational' -MaxEvents 10
```

Inspect recent PowerShell events:

```powershell
Get-WinEvent -LogName 'Microsoft-Windows-PowerShell/Operational' -MaxEvents 10
```

These commands provide practical administrative and investigative capabilities.

---

## 10. SOC Investigation Workflow

The endpoint is used to practice investigating activity generated through controlled security testing.

### Investigation Process

```text
Atomic Red Team Test
         |
         v
Windows Activity Generated
         |
         v
Security Telemetry Recorded
         |
         v
Wazuh Agent Collection
         |
         v
Wazuh Event Analysis
         |
         v
SOC Investigation
```

### Investigation Questions

During an investigation, relevant questions include:

1. What activity occurred?
2. Which process initiated the activity?
3. What was the parent process?
4. Which account executed the command?
5. When did the activity occur?
6. Was the activity expected?
7. Did the event generate an alert?
8. Are related events present?
9. Does the behavior align with a known attacker technique?
10. What additional evidence would confirm or disprove malicious intent?

### Example: Process Discovery

An Atomic Red Team Process Discovery test can generate commands associated with enumerating running processes.

A SOC investigation can then examine:

- Process execution telemetry
- Command-line arguments
- Parent-child process relationships
- Relevant Windows events
- Corresponding Wazuh alerts, if generated

This helps connect MITRE ATT&CK techniques to observable endpoint behavior.

---

## 11. Security Validation

The October 2026 review verified the following components.

| Component | Verification Result |
|---|---|
| Windows 11 Pro | Confirmed |
| Windows Build 26200 | Confirmed |
| 64-bit Operating System | Confirmed |
| 8 GB Allocated Memory | Confirmed |
| Wazuh Agent | Running / Automatic |
| Sysmon64 | Running / Automatic |
| Microsoft Defender | Running / Automatic |
| Defender Antivirus | Enabled |
| Defender Real-Time Protection | Enabled |
| Defender Behavior Monitoring | Enabled |
| Defender IOAV Protection | Enabled |
| PowerShell Script Block Logging | Enabled |
| PowerShell Module Logging | Enabled |
| PowerShell Transcription | Not configured at inspected path |
| Seven Wazuh Event Channels | Configured |
| Atomic Red Team Directory | Present |
| AtomicSOC Directory | Present |
| AtomicSOC Web Directory | Present |

### Validation Scope

The review confirmed the current service status, security settings, and configured telemetry collection sources.

It did not include a complete enumeration of active Sysmon filters or end-to-end validation of every possible Windows event category.

---

## 12. Challenges and Lessons Learned

### Endpoint Visibility

Native Windows security logging provides important information, but additional telemetry sources can improve investigative visibility.

Sysmon and PowerShell logging provide complementary data for understanding endpoint activity.

### Process Relationships

Understanding parent-child process relationships is essential when investigating suspicious execution.

An individual process name may not be sufficient to determine whether activity is malicious.

Execution context matters.

### Telemetry Collection Versus Detection

Collecting an event does not guarantee that Wazuh will generate an alert.

The event must be processed and match applicable detection conditions.

This distinction is important when evaluating detection coverage.

### Controlled Testing

Atomic Red Team provides a structured method for generating known behaviors.

This allows security monitoring to be evaluated against repeatable activity rather than relying exclusively on naturally occurring events.

### Operational Validation

Running services, configured event channels, and enabled logging policies provide evidence that the monitoring infrastructure is configured.

Actual detection effectiveness requires additional event-level testing and investigation.

---

## 13. Current Limitations

| Area | Current State |
|---|---|
| Windows endpoint | Operational |
| Wazuh integration | Connected |
| Sysmon | Running |
| Sysmon filter coverage | Not fully documented |
| PowerShell logging | Script Block and Module Logging enabled |
| PowerShell transcription | Not configured at inspected policy path |
| Defender protection | Enabled |
| Atomic Red Team | Installed and previously tested |
| AtomicSOC | Installed and previously tested |
| Custom Windows detection coverage | Continued development |
| Active Directory domain membership | Planned |
| Group Policy management | Planned |

---

## 14. Future Improvements

### Active Directory Integration

Deploy a Windows Server virtual machine to provide:

- Active Directory Domain Services
- DNS
- Domain user accounts
- Group Policy
- Centralized Windows administration

Join the Windows SOC endpoint to the lab domain.

This will enable additional security exercises involving authentication, identity management, and domain-related activity.

### Detection Engineering

- Expand custom Windows detection rules.
- Validate additional Sysmon event categories.
- Develop detections for suspicious PowerShell behavior.
- Correlate Atomic Red Team activity with Wazuh events.
- Document detection coverage against MITRE ATT&CK.

### SOC Investigation

- Develop repeatable investigation scenarios.
- Create incident investigation notes and playbooks.
- Practice identifying suspicious process trees.
- Improve event correlation across Windows telemetry sources.

### Endpoint Hardening

- Review Windows security baselines.
- Evaluate additional audit policy settings.
- Test security configuration changes.
- Document security control effectiveness.

---

## 15. Skills Demonstrated

**Windows Administration**
- Windows 11 deployment
- PowerShell administration
- Windows service management
- Remote administration over SSH

**Endpoint Security**
- Microsoft Defender configuration
- Sysmon deployment
- Windows Event Log monitoring
- PowerShell logging

**SIEM Integration**
- Wazuh agent deployment
- Windows event channel collection
- Endpoint telemetry forwarding
- Security event investigation

**Detection Engineering**
- Controlled attack simulation
- MITRE ATT&CK technique testing
- Process execution analysis
- Telemetry validation

**Security Tool Development**
- Atomic Red Team integration
- Python-based security tooling
- Custom browser-based test execution

---

## 16. Project Outcome

Successfully deployed a Windows 11 SOC endpoint integrated with the home lab's Wazuh SIEM platform.

Key accomplishments include:

- Deployed Windows 11 Pro on Proxmox VE.
- Configured the endpoint within a segmented security network.
- Installed and configured the Wazuh agent.
- Enabled collection of seven Windows Event Log channels.
- Installed Microsoft Sysmon.
- Enabled PowerShell Script Block Logging.
- Enabled PowerShell Module Logging.
- Verified Microsoft Defender protection.
- Installed Atomic Red Team.
- Executed controlled adversary simulation tests.
- Developed and tested the custom AtomicSOC application.

The endpoint provides a controlled platform for developing practical SOC investigation, Windows security monitoring, and detection engineering skills.

---

## Related Documentation

- `01_Project_Overview.md` — Overall HomeLAB architecture
- `12_Wazuh_SIEM.md` — SIEM architecture and detection engineering
- `14_AtomicSOC.md` — Atomic Red Team and custom attack simulation platform

---

**Project Status:** Operational — Continued Development

**Last Reviewed:** October 20, 2026
