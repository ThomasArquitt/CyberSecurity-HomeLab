# Network Architecture

## Purpose

This document describes the physical and logical network architecture of the CyberSecurity-HomeLab environment.

The laboratory is designed using enterprise-inspired networking principles, including network segmentation, centralized firewall management, controlled access between network segments, and centralized security monitoring.

The architecture supports infrastructure administration, Security Operations Center (SOC) investigations, detection engineering, endpoint monitoring, and controlled adversary simulation.

The environment combines physical networking equipment, virtualized infrastructure, dedicated security monitoring systems, and a segmented gaming server environment.

**Last Updated:** October 20, 2026

---

## Design Goals

The network was designed with the following objectives:

- Separate trusted and untrusted systems.
- Minimize lateral movement between network segments.
- Centralize routing and firewall policy enforcement.
- Implement logical network segmentation using VLANs.
- Isolate security testing infrastructure from personal devices.
- Restrict administrative access to authorized systems.
- Collect network and endpoint security telemetry.
- Support controlled adversary simulation.
- Maintain reliable network connectivity.
- Provide a scalable architecture for future expansion.

The overall design emphasizes security, maintainability, visibility, and controlled communication between systems.

---

## High-Level Architecture

The CyberSecurity-HomeLab utilizes a FortiGate 90D firewall as its primary network gateway and security enforcement point.

Internet connectivity is provided through a Breezeline fiber connection terminating at an Optical Network Terminal (ONT).

The ONT provides an Ethernet handoff to the FortiGate WAN1 interface.

The FortiGate provides:

- Internet gateway functionality
- VLAN routing
- Inter-VLAN firewall policies
- Network Address Translation (NAT)
- DHCP services
- Network traffic logging
- Administrative access restrictions

A Ubiquiti Switch Lite 8 PoE provides managed Layer 2 connectivity between the firewall, virtualization infrastructure, wireless access points, and other network devices.

The primary virtualization platform is a Proxmox VE 9.2 host running three virtual machines.

A dedicated physical Windows 11 server hosts the Palworld game server within a segmented gaming network.

### Logical Network Architecture

```text
                         INTERNET
                            |
                            |
                     Breezeline Fiber
                            |
                            |
                         Fiber ONT
                            |
                            |
                      FortiGate 90D
                     Firewall / Gateway
                            |
                            |
                  Ubiquiti Switch Lite
                         8 PoE
                            |
          +-----------------+------------------+
          |                 |                  |
          |                 |                  |
      Proxmox VE        Ubiquiti APs       Other Network
      Hypervisor         (2 Units)          Devices
          |
          |
    +-----+-----------------------+
    |             |               |
    |             |               |
 DSHIELD-01   WIN11-SOC-01     WAZUH-01
    |             |               |
    |             |               |
 SSH Honeypot   SOC Endpoint    Wazuh SIEM
    |             |               |
    +-------------+---------------+
                  |
                  |
          Security Monitoring


          SEPARATE GAMING NETWORK

                Gaming VLAN
                    |
                    |
             GAME-SERVER-01
              Windows 11 Pro
                    |
                    |
             Palworld Server
                    |
                    |
              Wazuh Agent
```

This diagram represents the logical relationships between infrastructure components.

The exact physical switch ports, VLAN assignments, and network interface configurations are documented separately.

The detailed network architecture diagram will be updated after the documentation refresh is completed.

---

## Network Segments

The environment utilizes multiple logical network segments to separate systems based on purpose and security requirements.

| Network Segment | Purpose |
|-----------------|---------|
| Home / Internal LAN | Trusted personal devices and internal infrastructure |
| Work VLAN 50 | Work-related devices and network isolation |
| Management Access | Restricted infrastructure administration |
| Public Wireless Network | Less-trusted wireless connectivity |
| Private Wireless Network | Trusted wireless connectivity |
| Gaming VLAN | Dedicated gaming infrastructure |

### Home / Internal LAN

The internal network provides connectivity for trusted personal devices and authorized laboratory infrastructure.

The primary internal network utilizes:

`192.168.1.0/24`

This network supports communication between authorized systems and internal infrastructure services.

### Work VLAN

VLAN 50 provides a dedicated network segment for work-related systems.

Separating work devices from personal and laboratory systems helps reduce unnecessary communication between different device categories.

Traffic between network segments is controlled through FortiGate firewall policies.

### Management Access

Infrastructure management access is restricted to authorized administrative systems.

Managed services include:

- FortiGate administration
- Proxmox administration
- Ubiquiti network management
- SSH administration
- Wazuh dashboard administration

Administrative access is controlled through firewall policies and device-level authentication.

Management access restrictions do not necessarily require a dedicated management VLAN.

### Public and Private Wireless Networks

Two Ubiquiti wireless access points provide wireless connectivity across the property.

The wireless environment includes:

- Public SSID
- Private SSID

These networks provide connectivity options for devices with different trust requirements.

Wireless traffic is integrated into the broader network segmentation strategy.

### Gaming VLAN

The Gaming VLAN provides logical separation for gaming-related infrastructure.

The dedicated Palworld server operates within this network segment.

**Verified server information:**

| Component | Details |
|-----------|---------|
| Hostname | GAME-SERVER-01 |
| Operating System | Windows 11 Pro |
| IP Address | 10.1.90.100 |
| Primary Application | Palworld Dedicated Server |
| Security Monitoring | Wazuh Agent |
| Monitoring Status | Active |

The Gaming VLAN supports:

- Dedicated game server hosting
- DHCP reservations
- Controlled firewall access
- UDP application traffic
- Network isolation
- Endpoint security monitoring

FortiGate firewall policies control permitted communication between the Gaming VLAN and other network segments.

---

## Infrastructure Components

### FortiGate 90D Firewall

The FortiGate 90D serves as the primary network gateway and firewall.

Responsibilities include:

- WAN connectivity
- Network routing
- VLAN gateway interfaces
- Inter-VLAN firewall policies
- Network Address Translation
- DHCP services
- Administrative access restrictions
- Traffic logging
- Syslog forwarding

The FortiGate provides centralized control over communication between network segments.

Firewall policies permit authorized communication while restricting unnecessary access.

The firewall's implicit deny behavior blocks traffic that does not match an applicable allow policy.

### Ubiquiti Switch Lite 8 PoE

The Ubiquiti managed switch provides Layer 2 connectivity for the laboratory infrastructure.

Responsibilities include:

- VLAN-aware switching
- Connectivity between network devices
- Power over Ethernet delivery
- Wireless access point connectivity
- Managed network administration

The switch provides connectivity between the firewall, virtualization infrastructure, wireless access points, and additional network devices.

### Ubiquiti Wireless Access Points

Two Ubiquiti wireless access points provide network coverage across multiple physical locations.

The access points support Public and Private wireless networks.

Wireless connectivity is integrated into the laboratory's network segmentation strategy.

### Proxmox VE Hypervisor

The primary virtualization host runs Proxmox VE 9.2.

The host provides virtualized infrastructure for security monitoring, endpoint testing, and adversary simulation.

**Verified virtual machine inventory:**

| VM ID | Hostname | Operating System | RAM | Boot Disk |
|-------|----------|------------------|-----|-----------|
| 101 | DSHIELD-01 | Ubuntu Linux | 4 GB | 40 GB |
| 102 | WIN11-SOC-01 | Windows 11 | 8 GB | 80 GB |
| 103 | WAZUH-01 | Ubuntu Server | 8 GB | 80 GB |

The virtual machines communicate through the network interfaces and virtual networking configuration provided by Proxmox.

Network access is governed by the applicable firewall policies and network segmentation configuration.

---

## Verified Security Endpoint Inventory

The Wazuh dashboard was reviewed on October 20, 2026, to verify registered security monitoring agents.

### Active Wazuh Agents

| Agent ID | Hostname | IP Address | Operating System | Agent Version | Status |
|----------|----------|------------|------------------|---------------|--------|
| 001 | WIN11-SOC-01 | 10.1.69.71 | Windows 11 Pro | 4.14.7 | Active |
| 002 | DSHIELD-01 | 10.1.69.70 | Ubuntu 26.04 LTS | 4.14.7 | Active |
| 003 | GAME-SERVER-01 | 10.1.90.100 | Windows 11 Pro | 4.14.7 | Active |

### Agent Monitoring Summary

| Metric | Value |
|--------|-------|
| Registered Agents | 3 |
| Active Agents | 3 |
| Disconnected Agents | 0 |
| Pending Agents | 0 |
| Windows Endpoints | 2 |
| Ubuntu Endpoints | 1 |
| Agent Group | default |
| Agent Version | 4.14.7 |

All three monitored endpoints were confirmed as actively connected to Wazuh.

The environment provides centralized endpoint monitoring across both Windows and Linux systems.

---

## Security Monitoring Architecture

The laboratory utilizes Wazuh as its centralized security monitoring platform.

Wazuh collects and analyzes telemetry from multiple infrastructure components.

### Security Telemetry Sources

| Source | IP Address | Collection Method | Status |
|--------|------------|-------------------|--------|
| WIN11-SOC-01 | 10.1.69.71 | Wazuh Agent | Active |
| DSHIELD-01 | 10.1.69.70 | Wazuh Agent / Cowrie Logs | Active |
| GAME-SERVER-01 | 10.1.90.100 | Wazuh Agent | Active |
| FortiGate 90D | Not Documented | Syslog Forwarding | Configured |

The three endpoint agents were verified as active on October 20, 2026.

FortiGate firewall logs are forwarded separately through syslog and do not require an endpoint agent.

### Telemetry Collection Architecture

```text
               SECURITY TELEMETRY SOURCES

    FortiGate 90D
         |
         | Syslog
         |
         +---------------------------+
                                     |
    WIN11-SOC-01                     |
    10.1.69.71                       |
         |                           |
         | Wazuh Agent               |
         |                           |
         +---------------------------+
                                     |
    DSHIELD-01                       |
    10.1.69.70                       |
         |                           |
         | Wazuh Agent / Cowrie      |
         |                           |
         +---------------------------+
                                     |
    GAME-SERVER-01                   |
    10.1.90.100                      |
         |                           |
         | Wazuh Agent               |
         |                           |
         +---------------------------+
                                     |
                                     v
                                 WAZUH-01
                                     |
                                     |
                           Security Event Analysis
                                     |
                                     |
                           Custom Detection Rules
                                     |
                                     |
                            Alerts / Investigation
```

This diagram illustrates logical telemetry relationships rather than exact transport protocols.

The specific ingestion methods, agent configurations, and detection rules are documented in the Wazuh SIEM documentation.

---

## Security Design

### Network Segmentation

Network segmentation is implemented through VLANs and firewall policies.

This design helps limit unnecessary communication between systems with different trust requirements.

Segmentation provides several benefits:

- Reduced exposure between network segments
- Improved traffic control
- Separation of trusted and less-trusted devices
- Greater visibility into permitted network communication
- Improved control over administrative access

### Firewall Policy Enforcement

The FortiGate firewall controls traffic between network segments.

Policies are configured to permit authorized communication while restricting unnecessary access.

The firewall's implicit deny behavior provides an additional layer of protection for traffic that does not match an applicable allow policy.

### Administrative Access

Management access to infrastructure components is restricted to authorized administrative devices and accounts.

SSH and web-based administration are used where appropriate.

Management access is controlled through firewall policies and device-level access restrictions.

### Honeypot Isolation

The DShield/Cowrie honeypot collects suspicious SSH activity.

External connectivity is controlled through FortiGate firewall policies and destination NAT configurations.

The honeypot provides an environment for observing unauthorized connection attempts and attacker behavior.

Its exposure is intentionally limited to the services required for honeypot operation.

### Endpoint Security Monitoring

Windows endpoints generate security telemetry through native Windows logging and additional monitoring components.

WIN11-SOC-01 utilizes:

- Windows Event Logs
- Sysmon
- Windows Defender
- PowerShell Logging
- Wazuh Agent

GAME-SERVER-01 is also monitored through its installed Wazuh agent.

This telemetry supports security investigations, detection engineering, and controlled security testing.

---

## Adversary Simulation Architecture

The laboratory includes a dedicated Windows 11 SOC endpoint for controlled security testing.

AtomicSOC is installed on WIN11-SOC-01 and provides a web interface for executing selected Atomic Red Team tests.

The application generates controlled endpoint activity that can be analyzed through Windows telemetry and Wazuh.

### Testing Workflow

```text
                 AtomicSOC
               Web Interface
                     |
                     v
                FastAPI Backend
                     |
                     v
              Atomic Red Team
                     |
                     v
               WIN11-SOC-01
                     |
                     v
             Windows Telemetry
                     |
                     v
                Wazuh Agent
                     |
                     v
                 WAZUH-01
                     |
                     v
             Security Investigation
```

The testing environment supports practical experience with:

- MITRE ATT&CK techniques
- Process execution monitoring
- Windows event analysis
- Detection validation
- Security alert investigation

Controlled simulations are conducted within the laboratory environment.

---

## Network Security Monitoring

The network architecture supports centralized security visibility through Wazuh.

Monitoring capabilities include:

- Firewall event collection
- Windows endpoint telemetry
- Linux endpoint telemetry
- SSH honeypot activity
- Windows process monitoring
- PowerShell activity
- Custom detection rules
- Security alert investigation

The objective is to improve visibility into suspicious activity across both network and endpoint systems.

---

## Future Expansion

The network architecture is designed to support additional infrastructure and security capabilities.

### Windows Server and Active Directory

A Windows Server virtual machine is planned for deployment within Proxmox.

The server will provide:

- Active Directory Domain Services
- DNS services
- Group Policy management
- Centralized identity management
- Domain authentication

WIN11-SOC-01 will be joined to the Active Directory domain.

Security monitoring will be expanded to include authentication activity and identity-related security events.

### Active Directory Security Monitoring

Future detection engineering activities will include:

- Failed authentication monitoring
- Password spraying detection
- Account enumeration
- Privileged group changes
- Suspicious account creation
- Domain authentication anomalies

These capabilities will expand the laboratory's security monitoring coverage.

### AI-Powered SOC Job Discovery Platform

A lightweight Linux container is planned for deployment using Proxmox LXC.

The container will host a Python-based application for automated cybersecurity job discovery.

Planned capabilities include:

- Job board API integration
- Permitted web scraping
- AI-assisted job classification
- Automated job filtering
- Duplicate detection
- Scheduled searches
- Job notifications

The application will operate as a separate infrastructure service within the laboratory.

### Additional Security Automation

Future improvements may include:

- Expanded AtomicSOC functionality
- Automated detection testing
- Security event enrichment
- Additional endpoint monitoring
- Python-based investigation utilities
- PowerShell security automation

---

## Documentation References

Additional configuration details are maintained in the following documents:

- `04_VLAN_Design.md`
- `05_IP_Addressing_Plan.md`
- `06_Proxmox_Configuration.md`
- `07_FortiGate_Configuration.md`
- `08_Ubiquiti_Switch_Configuration.md`
- `10_DShield_Honeypot.md`

Detailed Wazuh, Windows 11 SOC, and AtomicSOC documentation will be maintained in dedicated project documents.

---

## Notes

This document provides a high-level representation of the current network architecture.

Detailed VLAN assignments, IP addresses, firewall policies, and device configurations are maintained in separate documentation files.

Network architecture diagrams will be updated after the infrastructure documentation refresh is completed.

The architecture will continue evolving as new infrastructure components and security capabilities are introduced.
