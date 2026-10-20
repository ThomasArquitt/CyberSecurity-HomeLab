# VLAN Design

## Purpose

This document describes the Virtual Local Area Network (VLAN) architecture implemented within the CyberSecurity-HomeLab environment.

The network utilizes IEEE 802.1Q VLAN segmentation to logically separate devices and services based on their purpose, trust level, and security requirements.

Inter-VLAN communication is routed and controlled through a FortiGate 90D firewall.

The architecture supports:

- Network security and segmentation
- Infrastructure administration
- Security Operations Center (SOC) operations
- Endpoint security monitoring
- Honeypot deployment
- Controlled adversary simulation
- Gaming server infrastructure
- Centralized security telemetry collection

**Last Updated:** October 20, 2026

---

## Design Principles

The VLAN architecture follows enterprise-inspired security design principles.

### Least Privilege

Network communication is restricted according to required destinations, services, and protocols.

Dedicated firewall policies provide controlled access between network segments.

### Network Segmentation

Devices and services are separated into logical network segments according to their purpose and security requirements.

### Centralized Firewall Enforcement

The FortiGate 90D provides Layer 3 routing and firewall policy enforcement between VLANs.

### Reduced Lateral Movement

Network segmentation limits unnecessary communication between systems and reduces opportunities for lateral movement across network boundaries.

### Administrative Access Control

Infrastructure administration is restricted through authentication, firewall policies, and device-level access controls.

A designated administrative workstation is used to access infrastructure systems.

### Security Monitoring

Security telemetry is collected from Windows endpoints, Linux systems, network infrastructure, and the DShield/Cowrie honeypot.

### Scalability

The architecture supports future expansion, including Windows Server, Active Directory, additional security monitoring systems, and containerized applications.

---

## VLAN Allocation

The following network interfaces and gateway addresses were verified through the FortiGate 90D management interface on October 20, 2026.

| VLAN ID | Interface Name | Subnet | Gateway | Purpose |
|---------|----------------|--------|---------|---------|
| 10 | home_vlan | 10.1.10.0/24 | 10.1.10.1 | Trusted home devices |
| 50 | work_vlan | 10.1.50.0/24 | 10.1.50.1 | Work-related devices |
| 69 | security_vlan | 10.1.69.0/24 | 10.1.69.1 | Security laboratory |
| 90 | gaming_vlan | 10.1.90.0/24 | 10.1.90.1 | Gaming infrastructure |
| 220 | priv_wifi | 10.1.220.0/24 | 10.1.220.1 | Private wireless devices |
| 221 | public_wifi | 10.1.221.0/24 | 10.1.221.1 | Public wireless devices |
| N/A | internal | 192.168.1.0/24 | 192.168.1.1 | Infrastructure management |

All listed interfaces use a subnet mask of `255.255.255.0`.

The internal management network operates on a FortiGate hardware switch interface rather than a dedicated VLAN interface.

The six VLAN IDs reflect the documented network design. Interface names and gateway addresses were verified through the FortiGate GUI; the underlying VLAN tag configuration can be independently verified through the FortiGate CLI.

---

## VLAN 10 — Home Network

**Interface:** `home_vlan`  
**Subnet:** `10.1.10.0/24`  
**Gateway:** `10.1.10.1`

### Purpose

The Home VLAN provides connectivity for trusted personal devices.

This segment separates personal systems from work-related devices, security testing infrastructure, and public wireless networks.

### Design Objectives

- Provide connectivity for trusted home devices.
- Separate personal devices from work systems.
- Restrict unnecessary access to security infrastructure.
- Control communication with other network segments.
- Maintain reliable internet connectivity.

### Administrative Workstation

The primary administrative workstation operates within the Home VLAN.

| Component | Details |
|-----------|---------|
| Hostname | TIC-PC |
| IP Address | 10.1.10.50 |
| Network | Home VLAN 10 |
| Role | Infrastructure Administration |

TIC-PC is used to remotely administer laboratory infrastructure.

Dedicated FortiGate policies authorize management traffic from this workstation to specific infrastructure destinations.

---

## VLAN 50 — Work Network

**Interface:** `work_vlan`  
**Subnet:** `10.1.50.0/24`  
**Gateway:** `10.1.50.1`

### Purpose

The Work VLAN provides a dedicated network segment for work-related devices.

Separating work systems from personal and laboratory devices reduces unnecessary communication between different device categories.

### Design Objectives

- Isolate work-related devices.
- Maintain required internet connectivity.
- Reduce exposure to personal and laboratory systems.
- Apply firewall-based traffic restrictions.
- Support reliable work-related network operations.

### Security Considerations

Traffic between the Work VLAN and other network segments is controlled through FortiGate firewall policies.

The separation supports a more controlled environment for work-related devices.

---

## VLAN 69 — Security Laboratory

**Interface:** `security_vlan`  
**Subnet:** `10.1.69.0/24`  
**Gateway:** `10.1.69.1`

### Purpose

The Security VLAN provides a dedicated network segment for cybersecurity testing, endpoint monitoring, and honeypot operations.

This network supports practical SOC investigations, security event generation, detection engineering, and controlled adversary simulation.

### Verified Security Endpoints

| Hostname | IP Address | Operating System | Purpose |
|----------|------------|------------------|---------|
| DSHIELD-01 | 10.1.69.70 | Ubuntu 26.04 LTS | SSH Honeypot |
| WIN11-SOC-01 | 10.1.69.71 | Windows 11 Pro | SOC Testing Endpoint |
| WAZUH-01 | 10.1.69.72 | Ubuntu Server | Centralized Security Monitoring |

The IP addresses were verified through FortiGate address objects and Wazuh endpoint information.

### DSHIELD-01

DSHIELD-01 operates as a dedicated Ubuntu Linux virtual machine hosting the DShield/Cowrie SSH honeypot.

The honeypot collects:

- Unauthorized SSH connection attempts
- Credential guessing activity
- Authentication failures
- Attacker command execution
- Automated attack behavior
- Suspicious network connections

External connectivity is controlled through FortiGate firewall policies and destination NAT configurations.

Collected telemetry is integrated into Wazuh for centralized monitoring and detection engineering.

### WIN11-SOC-01

WIN11-SOC-01 operates as a dedicated Windows 11 endpoint for security testing and investigation.

Configured components include:

- Sysmon
- Windows Defender
- PowerShell Logging
- Wazuh Agent
- OpenSSH Server
- Atomic Red Team
- AtomicSOC

The endpoint generates controlled security events that can be investigated through Windows telemetry and Wazuh.

### WAZUH-01

WAZUH-01 hosts the centralized Wazuh security monitoring platform.

The server receives telemetry from monitored endpoints and network infrastructure.

Its responsibilities include:

- Security event collection
- Log analysis
- Custom detection rules
- Alert generation
- Security investigations
- Detection engineering

### Security Considerations

The Security VLAN contains systems used for testing and observing potentially malicious activity.

Communication with trusted network segments is restricted through explicit FortiGate deny policies.

Dedicated allow policies permit authorized administrative access and required monitoring traffic.

---

## VLAN 90 — Gaming Network

**Interface:** `gaming_vlan`  
**Subnet:** `10.1.90.0/24`  
**Gateway:** `10.1.90.1`

### Purpose

The Gaming VLAN provides a dedicated network segment for gaming-related devices and server infrastructure.

The primary system operating within this segment is GAME-SERVER-01.

### Verified Server Configuration

| Component | Details |
|-----------|---------|
| Hostname | GAME-SERVER-01 |
| IP Address | 10.1.90.100 |
| Operating System | Windows 11 Pro |
| Primary Application | Palworld Dedicated Server |
| IP Assignment | DHCP Reservation |
| Firewall | FortiGate 90D |
| Security Monitoring | Wazuh Agent 4.14.7 |
| Wazuh Agent ID | 003 |
| Monitoring Status | Active |

### Server Infrastructure

The Palworld server utilizes:

- SteamCMD
- Windows Scheduled Tasks
- FortiGate firewall policies
- UDP application traffic
- DHCP reservations
- REST API functionality
- Wazuh endpoint monitoring

### Security Considerations

The Gaming VLAN separates gaming infrastructure from trusted personal devices and security testing systems.

Required communication is controlled through FortiGate firewall policies.

A dedicated firewall policy permits GAME-SERVER-01 to communicate with WAZUH-01.

The Wazuh agent was verified as actively connected on October 20, 2026.

---

## VLAN 220 — Private Wi-Fi

**Interface:** `priv_wifi`  
**Subnet:** `10.1.220.0/24`  
**Gateway:** `10.1.220.1`

### Purpose

The Private Wi-Fi VLAN provides wireless connectivity for trusted devices.

Wireless connectivity is delivered through Ubiquiti access points connected to the managed network infrastructure.

### Design Objectives

- Provide wireless connectivity for trusted devices.
- Maintain separation from public wireless clients.
- Support controlled access to authorized network resources.
- Apply firewall policies to routed traffic.
- Maintain reliable wireless connectivity.

### Security Considerations

Communication between the Private Wi-Fi VLAN and other routed network segments is controlled through FortiGate firewall policies.

---

## VLAN 221 — Public Wi-Fi

**Interface:** `public_wifi`  
**Subnet:** `10.1.221.0/24`  
**Gateway:** `10.1.221.1`

### Purpose

The Public Wi-Fi VLAN provides wireless connectivity for less-trusted devices.

This segment is designed to separate public wireless clients from trusted internal infrastructure.

### Design Objectives

- Provide internet connectivity.
- Restrict unnecessary access to internal networks.
- Reduce exposure of trusted infrastructure.
- Maintain separation from private wireless devices.
- Enforce network access restrictions.

### Security Considerations

Internet-only access is the intended security design.

Actual permitted destinations and services are determined by the configured FortiGate firewall policies.

---

## Internal Management Network

**Interface:** `internal`  
**Subnet:** `192.168.1.0/24`  
**Gateway:** `192.168.1.1`  
**Interface Type:** FortiGate Hardware Switch

### Purpose

The internal network provides connectivity for infrastructure administration and management.

Unlike the six VLAN interfaces, this network operates through the FortiGate hardware switch interface.

### Verified Infrastructure Addresses

| Device | IP Address | Purpose |
|--------|------------|---------|
| FortiGate 90D | 192.168.1.1 | Firewall Management |
| Ubiquiti Switch | 192.168.1.2 | Switch Management |
| Proxmox Host | 192.168.1.10 | Virtualization Management |

These addresses were verified through FortiGate interface and address object configurations.

### Administrative Access

Administrative access to the HomeLAB infrastructure has been configured, tested, and successfully utilized through SSH.

SSH provides remote command-line access for infrastructure administration, configuration management, troubleshooting, and system maintenance.

Verified administrative capabilities include:

- Remote SSH administration of infrastructure systems
- Command-line configuration and troubleshooting
- Remote service management
- Security configuration management
- Network connectivity verification
- System maintenance and updates
- Remote administration without requiring direct console access

SSH connectivity has been successfully tested and utilized across the laboratory infrastructure.

Administrative access is controlled through authentication, device configuration, and applicable FortiGate security policies.

### Administrative Security

The management design emphasizes restricting administrative access to authorized systems.

Security controls include:

- Device authentication
- Firewall policy enforcement
- Restricted administrative access
- SSH-based remote management
- Device-level management configuration

Successful SSH connectivity confirms operational administrative access.

Verification of unauthorized access restrictions is a separate security validation activity.

---

## FortiGate Firewall Segmentation

The FortiGate 90D provides centralized firewall policy enforcement between network segments.

The firewall utilizes explicit allow policies, explicit deny policies, and an implicit deny rule.

### Explicit Security VLAN Deny Policies

Five dedicated firewall policies restrict traffic originating from the Security VLAN.

| Policy ID | Source Interface | Destination Interface | Action |
|-----------|------------------|-----------------------|--------|
| 25 | security_vlan | home_vlan | DENY |
| 26 | security_vlan | internal | DENY |
| 27 | security_vlan | work_vlan | DENY |
| 28 | security_vlan | priv_wifi | DENY |
| 29 | security_vlan | public_wifi | DENY |

These policies deny traffic initiated from the Security VLAN toward the specified network segments when the traffic matches the policy conditions.

### Security Objectives

The explicit deny policies help:

- Prevent unauthorized connections into trusted networks.
- Reduce opportunities for lateral movement.
- Separate honeypot infrastructure from personal devices.
- Restrict access to management infrastructure.
- Protect work-related devices.
- Maintain separation between security systems and wireless clients.

### Stateful Firewall Behavior

The FortiGate operates as a stateful firewall.

Authorized connections initiated from permitted network segments can receive return traffic without requiring a separate reverse-direction allow policy.

For example, an authorized SSH connection from TIC-PC to WIN11-SOC-01 can receive return traffic through the established session.

This allows controlled administration while restricting unauthorized connections initiated from the Security VLAN.

### Policy Ordering

FortiGate firewall policies are evaluated according to their configured order.

The first matching policy determines the action applied to traffic.

Explicit deny policies must be positioned appropriately relative to allow policies to enforce the intended security design.

---

## Authorized Administrative Access

TIC-PC is the designated administrative workstation.

**Hostname:** `TIC-PC`  
**IP Address:** `10.1.10.50`  
**Network:** Home VLAN 10

Dedicated firewall policies permit administrative access from TIC-PC to selected infrastructure systems.

### Verified Management Policies

| Policy ID | Destination | Permitted Services |
|-----------|-------------|--------------------|
| 7 | Proxmox Host | HTTP, HTTPS, SSH |
| 22 | FortiGate Management | HTTP, HTTPS, SSH |
| 23 | Ubiquiti Switch | HTTP, HTTPS |
| 32 | WIN11-SOC-01 | SSH |
| 33 | WAZUH-01 | SSH, HTTPS |
| 34 | DSHIELD-01 | DSHIELD-SSH-MGMT |
| 37 | GAME-SERVER-01 | SSH, PALWORLD-UDP-8211 |

These policies provide controlled administrative access to infrastructure systems.

The configuration avoids relying exclusively on unrestricted access between network segments.

### Management Access Validation

SSH administrative connectivity has been tested and successfully utilized throughout the laboratory.

The configured firewall policies provide the intended network paths for administrative access.

Access to the FortiGate itself is additionally governed by interface administrative-access settings, administrator trusted hosts, and applicable local-in controls.

---

## Cross-VLAN Security Monitoring

The Gaming VLAN and Security VLAN contain systems that communicate for centralized security monitoring.

### Wazuh Communication Policy

| Component | Configuration |
|-----------|---------------|
| Policy ID | 39 |
| Source Interface | gaming_vlan |
| Destination Interface | security_vlan |
| Source Address | GAME-SERVER-01 |
| Destination Address | WAZUH-01 |
| Service | WAZUHTCP |
| Action | ACCEPT |
| Logging | All |

This policy permits GAME-SERVER-01 to communicate with WAZUH-01 using the defined WAZUHTCP service.

The policy supports centralized monitoring while limiting permitted communication to the configured source, destination, and service.

### Security Benefits

- Limits unnecessary cross-VLAN communication.
- Supports centralized security monitoring.
- Maintains segmentation between gaming and security infrastructure.
- Provides firewall logging for permitted monitoring traffic.
- Demonstrates least-privilege network access control.

---

## Internet Access and NAT

The FortiGate provides internet access through WAN1.

Outbound firewall policies permit internet connectivity from authorized internal networks.

### Verified Outbound Internet Policies

| Policy ID | Source Interface | Destination Interface | NAT | Action |
|-----------|------------------|-----------------------|-----|--------|
| 1 | home_vlan | wan1 | Enabled | ACCEPT |
| 6 | internal | wan1 | Enabled | ACCEPT |
| 5 | security_vlan | wan1 | Enabled | ACCEPT |
| 4 | work_vlan | wan1 | Enabled | ACCEPT |
| 36 | gaming_vlan | wan1 | Enabled | ACCEPT |
| 3 | priv_wifi | wan1 | Enabled | ACCEPT |
| 30 | public_wifi | wan1 | Enabled | ACCEPT |

Outbound NAT allows internal systems to access external networks through the FortiGate WAN interface.

### Security Considerations

Outbound internet access is controlled through FortiGate firewall policies.

The current outbound policies permit broad services to external destinations.

Future security-hardening activities may include evaluating more restrictive outbound service requirements for sensitive infrastructure.

---

## External Service Exposure

The laboratory includes intentionally exposed services for security research and game server hosting.

### Honeypot Exposure

The FortiGate contains inbound firewall policies supporting the DShield/Cowrie honeypot.

Verified inbound policy IDs include:

- Policy 24 — Honeypot SSH
- Policy 16 — Honeypot HTTP
- Policy 17 — Honeypot HTTPS
- Policy 18 — Cowrie SSH Honeypot

These policies permit traffic from WAN1 toward the Security VLAN using configured destination address objects.

The exact external ports and destination mappings depend on the associated Virtual IP (VIP) configurations.

### Palworld Server Exposure

A dedicated inbound policy supports external connectivity to the Palworld dedicated server.

| Component | Details |
|-----------|---------|
| Policy ID | 38 |
| Source Interface | wan1 |
| Destination Interface | gaming_vlan |
| Destination Object | PALWORLD-UDP-8211 |
| Service | PALWORLD-UDP-8211 |
| Action | ACCEPT |
| Logging | All |

The policy supports external connectivity to the Palworld dedicated server.

The associated VIP configuration determines the external-to-internal destination mapping.

### Security Considerations

Externally accessible services increase the laboratory's attack surface.

Security controls include:

- Dedicated firewall policies
- Network segmentation
- Destination NAT
- Endpoint monitoring
- Centralized security telemetry

The exact VIP mappings and exposed ports will be documented in the FortiGate configuration documentation.

---

## VLAN Switching Infrastructure

The Ubiquiti Switch Lite 8 PoE provides managed Layer 2 switching.

The switch supports VLAN-aware connectivity between the FortiGate firewall, virtualization infrastructure, wireless access points, and additional network devices.

### Responsibilities

- VLAN tagging and forwarding
- Managed Ethernet connectivity
- Power over Ethernet delivery
- Wireless access point connectivity
- Network infrastructure connectivity

The FortiGate provides Layer 3 routing and firewall enforcement between network segments.

### Wireless VLAN Integration

Two Ubiquiti wireless access points provide wireless connectivity across multiple physical locations.

The wireless environment includes:

- Private Wi-Fi
- Public Wi-Fi

Wireless network segmentation is integrated with the broader VLAN architecture.

The FortiGate provides gateway services and firewall enforcement for routed wireless traffic.

---

## Security Monitoring Integration

The laboratory utilizes Wazuh for centralized security monitoring.

Wazuh collects security telemetry from Windows endpoints, Ubuntu Linux systems, the DShield/Cowrie honeypot, and the FortiGate firewall.

### Verified Wazuh Agents

The following agents were verified through the Wazuh dashboard on October 20, 2026.

| Agent ID | Hostname | IP Address | Operating System | Version | Status |
|----------|----------|------------|------------------|---------|--------|
| 001 | WIN11-SOC-01 | 10.1.69.71 | Windows 11 Pro | 4.14.7 | Active |
| 002 | DSHIELD-01 | 10.1.69.70 | Ubuntu 26.04 LTS | 4.14.7 | Active |
| 003 | GAME-SERVER-01 | 10.1.90.100 | Windows 11 Pro | 4.14.7 | Active |

### Monitoring Summary

| Metric | Value |
|--------|-------|
| Registered Agents | 3 |
| Active Agents | 3 |
| Disconnected Agents | 0 |
| Pending Agents | 0 |
| Windows Endpoints | 2 |
| Ubuntu Endpoints | 1 |
| Agent Group | default |

All three registered endpoint agents were confirmed as actively connected.

### Network Security Telemetry

Wazuh collects telemetry from:

- Windows endpoint security events
- Linux endpoint activity
- DShield/Cowrie honeypot logs
- FortiGate firewall syslog

### Cross-VLAN Monitoring

The Security VLAN and Gaming VLAN contain actively monitored endpoints.

Wazuh agents communicate with the centralized monitoring infrastructure through the network paths permitted by the applicable firewall policies.

This provides centralized security visibility across multiple network segments.

---

## FortiGate Address Objects

FortiGate address objects provide reusable references for firewall policies.

The following infrastructure address objects were verified.

| Address Object | IP Address | Purpose |
|----------------|------------|---------|
| Fortigate_mgmt | 192.168.1.1/32 | Firewall Management |
| Ubiquity_Switch | 192.168.1.2/32 | Switch Management |
| PVE_Host | 192.168.1.10/32 | Proxmox Management |
| Tic_PC | 10.1.10.50/32 | Administrative Workstation |
| DSHIELD_Honeypot | 10.1.69.70/32 | Honeypot |
| WIN11-SOC-01 | 10.1.69.71/32 | SOC Endpoint |
| WAZUH-01 | 10.1.69.72/32 | Security Monitoring |
| GAME-SERVER-01 | 10.1.90.100/32 | Palworld Server |

### Legacy Address Object

An additional address object was identified:

| Address Object | IP Address | References |
|----------------|------------|------------|
| Ubuntu_Server_VM | 192.168.1.20/32 | 0 |

This object may reference the retired Ubuntu management virtual machine.

The object should be reviewed before removal to confirm that it is no longer required.

---

## Security Considerations

### Least Privilege

Network communication is controlled through firewall policies that define permitted sources, destinations, and services.

### Network Isolation

Work devices, gaming infrastructure, security testing systems, and wireless clients are separated into dedicated network segments.

### Explicit Deny Policies

Five explicit deny policies restrict traffic originating from the Security VLAN toward trusted network segments.

### Administrative Access

SSH administration has been tested and successfully utilized across the laboratory infrastructure.

Dedicated firewall policies provide authorized access from TIC-PC to selected infrastructure systems.

### Honeypot Exposure

The DShield/Cowrie honeypot is intentionally exposed to collect SSH-related attack telemetry.

External connectivity is controlled through FortiGate destination NAT and firewall policies.

### Security Logging

Firewall and endpoint telemetry is collected centrally through Wazuh.

This supports security investigations, detection engineering, and monitoring across multiple network segments.

### FortiGate Management Services

A review of the FortiGate interface configuration identified administrative services enabled on multiple interfaces.

Observed services include:

- HTTPS
- HTTP
- SSH
- PING
- FortiManager access

The internal hardware switch interface provides SSH and HTTPS administrative access.

Several VLAN interfaces also have HTTP and HTTPS administrative access enabled.

These settings are documented for future security-hardening review.

Recommended validation activities include:

- Review administrative access requirements for each interface.
- Disable unnecessary HTTP management access.
- Prefer encrypted management protocols.
- Verify administrative access restrictions.
- Review FortiGate local-in policies where applicable.

These activities are planned security reviews and are not documented as completed changes.

---

## Validation

The following network components and capabilities have been verified.

| Component | Validation | Status |
|-----------|------------|--------|
| Home VLAN | FortiGate interface configured | Verified |
| Work VLAN | FortiGate interface configured | Verified |
| Security VLAN | FortiGate interface configured | Verified |
| Gaming VLAN | FortiGate interface configured | Verified |
| Private Wi-Fi VLAN | FortiGate interface configured | Verified |
| Public Wi-Fi VLAN | FortiGate interface configured | Verified |
| Internal Management Network | FortiGate hardware switch configured | Verified |
| SSH Administration | Successfully tested and utilized | Verified |
| Security VLAN Deny Policies | Five explicit deny policies configured | Verified |
| Administrative Access Policies | Dedicated TIC-PC policies configured | Verified |
| Wazuh Cross-VLAN Policy | Gaming-to-Security monitoring policy configured | Verified |
| Outbound Internet Policies | NAT-enabled policies configured | Verified |
| WIN11-SOC-01 | Wazuh agent connected | Active |
| DSHIELD-01 | Wazuh agent connected | Active |
| GAME-SERVER-01 | Wazuh agent connected | Active |

### Additional Validation Opportunities

Future configuration reviews will include:

- VLAN parent interface assignments
- Switch port VLAN profiles
- Inter-VLAN firewall policy permissions
- Wireless SSID-to-VLAN mappings
- Administrative access restrictions
- DHCP scope configurations
- Network traffic logging
- FortiGate management service hardening
- Honeypot VIP mappings
- Palworld VIP mappings
- Legacy address object cleanup

---

## Future Expansion

### Windows Server and Active Directory

A Windows Server virtual machine is planned for deployment within Proxmox.

The server will provide:

- Active Directory Domain Services
- DNS
- Group Policy
- Centralized Identity Management
- Domain Authentication

WIN11-SOC-01 will be joined to the Active Directory domain.

The domain controller's network placement will be determined during implementation.

### Active Directory Security Monitoring

Future detection engineering activities will include:

- Failed authentication monitoring
- Password spraying detection
- Account enumeration
- Privileged group changes
- Suspicious account creation
- Domain authentication anomalies

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

Network access for the application will be controlled through the existing security architecture.

---

## Lessons Learned

Implementing and administering the VLAN architecture has provided practical experience with:

- Layer 2 network segmentation
- Layer 3 routing
- Firewall policy enforcement
- Explicit allow and deny policies
- Network access control
- VLAN-aware switching
- Wireless network segmentation
- Inter-VLAN communication
- SSH-based infrastructure administration
- Stateful firewall behavior
- Network troubleshooting
- Security monitoring across network segments

The environment demonstrates how network segmentation, centralized firewall management, and endpoint telemetry collection can support a controlled and observable infrastructure.

---

## Documentation References

Related documentation includes:

- `03_Network_Architecture.md`
- `05_IP_Addressing_Plan.md`
- `06_Proxmox_Configuration.md`
- `07_FortiGate_Configuration.md`
- `08_Ubiquiti_Switch_Configuration.md`

---

## Notes

This document describes the VLAN architecture implemented within the CyberSecurity-HomeLab as of October 20, 2026.

Network interface names and gateway addresses were verified through the FortiGate management interface.

Firewall policies and address objects were reviewed to document network segmentation and authorized communication paths.

SSH administrative connectivity has been successfully tested and utilized across the laboratory infrastructure.

Three Wazuh endpoint agents were verified as actively connected.

Detailed firewall policies, IP address assignments, switch configurations, and DHCP settings are maintained in separate documentation.

Network architecture diagrams and screenshots will be updated after the Markdown documentation refresh is completed.
