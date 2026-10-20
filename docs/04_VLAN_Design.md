# 04 — VLAN Design

## 1. Overview

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
- Future identity and directory services

**Last Updated:** October 20, 2026

> **Security Note:** This document is a sanitized representation of the operational VLAN architecture. Exact VLAN IDs, subnet assignments, gateway addresses, management endpoints, hostnames, firewall policy identifiers, and sensitive configuration details are intentionally excluded.

---

## 2. VLAN Design Principles

The VLAN architecture follows enterprise-inspired security design principles.

### Least Privilege

Network communication is restricted according to required destinations, services, and protocols.

Dedicated firewall policies provide controlled access between network segments.

### Network Segmentation

Devices and services are separated into logical network segments according to their purpose and security requirements.

### Centralized Firewall Enforcement

The FortiGate provides Layer 3 routing and firewall policy enforcement between VLANs.

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

## 3. Network Segment Inventory

The HomeLAB currently uses six VLANs and a separate untagged infrastructure management network.

### Network Segments

| Network Segment | Primary Purpose | Network Classification |
|---|---|---|
| Home Network | Trusted personal devices | Trusted |
| Work Network | Work-related systems | Restricted |
| Security Laboratory | Cybersecurity testing and monitoring | Isolated Testing |
| Gaming Network | Dedicated gaming infrastructure | Segmented |
| Private Wireless | Trusted wireless clients | Trusted |
| Public Wireless | Guest and less-trusted devices | Less Trusted |
| Infrastructure Management | Network and virtualization administration | Restricted Management |

### Network Architecture

```text
                       INTERNET
                          |
                          |
                    FORTIGATE 90D
                   FIREWALL / ROUTER
                          |
                          |
                   MANAGED SWITCH
                          |
          +---------------+---------------+
          |               |               |
          |               |               |
       PROXMOX         WIRELESS        WIRED
       HOST            ACCESS          DEVICES
          |            POINTS
          |               |
     +----+----+      +---+---+
     |    |    |      |       |
     |    |    |   PRIVATE  PUBLIC
     |    |    |   WIRELESS WIRELESS
     |    |    |
  DSHIELD SOC WAZUH
  HONEYPOT VM  SIEM
```

This diagram represents the logical relationships between infrastructure components.

It does not disclose physical port assignments or operational network addressing.

---

## 4. Home Network

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

It is used to remotely administer laboratory infrastructure.

Dedicated FortiGate policies authorize management traffic from this workstation to selected infrastructure destinations.

### Security Considerations

The Home VLAN is classified as a trusted network.

However, access to security and management infrastructure is still governed by applicable firewall policies.

Trusted network membership does not automatically grant unrestricted administrative access.

---

## 5. Work Network

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

## 6. Security Laboratory Network

### Purpose

The Security VLAN provides a dedicated network segment for cybersecurity testing, endpoint monitoring, and honeypot operations.

This network supports practical SOC investigations, security event generation, detection engineering, and controlled adversary simulation.

### Security Workloads

| Workload | Operating System | Primary Purpose |
|---|---|---|
| DShield Honeypot | Ubuntu Server | SSH Honeypot and Attack Telemetry |
| Windows SOC Endpoint | Windows 11 Pro | SOC Testing and Endpoint Monitoring |
| Wazuh SIEM | Ubuntu Server | Centralized Security Monitoring |

### DShield Honeypot

The DShield/Cowrie honeypot operates as a dedicated Ubuntu Linux virtual machine.

The honeypot collects:

- Unauthorized SSH connection attempts
- Credential guessing activity
- Authentication failures
- Commands submitted during emulated sessions
- Automated attack behavior
- Suspicious network connections

External connectivity is controlled through FortiGate firewall policies and destination NAT configurations.

Collected telemetry is integrated into Wazuh for centralized monitoring and detection engineering.

### Windows SOC Endpoint

The Windows SOC endpoint operates as a dedicated Windows 11 virtual machine for security testing and investigation.

Configured components include:

- Sysmon
- Windows Defender
- PowerShell Logging
- Wazuh Agent
- OpenSSH Server
- Atomic Red Team
- AtomicSOC

The endpoint generates controlled security events that can be investigated through Windows telemetry and Wazuh.

### Wazuh SIEM

The Wazuh virtual machine hosts the centralized security monitoring platform.

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

Communication with trusted network segments is restricted through dedicated FortiGate firewall policies.

Authorized administrative access and required monitoring traffic are permitted through applicable allow policies.

The design helps reduce opportunities for lateral movement from security testing systems into trusted infrastructure.

---

## 7. Gaming Network

### Purpose

The Gaming VLAN provides a dedicated network segment for gaming-related devices and server infrastructure.

The primary system operating within this segment is a dedicated physical Windows computer hosting a Palworld game server.

### Server Configuration

| Component | Configuration |
|---|---|
| Platform | Dedicated Physical Computer |
| Operating System | Windows 11 Pro |
| Primary Application | Palworld Dedicated Server |
| Installation Method | SteamCMD |
| IP Assignment | DHCP Reservation |
| Firewall | FortiGate |
| Security Monitoring | Wazuh Agent |
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

A dedicated policy permits the gaming server to communicate with the Wazuh SIEM for security monitoring.

The Wazuh agent was verified as actively connected during the October 20, 2026 infrastructure review.

---

## 8. Private Wireless Network

### Purpose

The Private Wireless VLAN provides wireless connectivity for trusted devices.

Wireless connectivity is delivered through Ubiquiti access points connected to the managed network infrastructure.

### Design Objectives

- Provide wireless connectivity for trusted devices.
- Maintain separation from public wireless clients.
- Support controlled access to authorized network resources.
- Apply firewall policies to routed traffic.
- Maintain reliable wireless connectivity.

### Security Considerations

Communication between the Private Wireless VLAN and other routed network segments is controlled through FortiGate firewall policies.

Private wireless clients are logically separated from public wireless clients.

---

## 9. Public Wireless Network

### Purpose

The Public Wireless VLAN provides wireless connectivity for less-trusted devices.

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

### Security Benefits

Public wireless segmentation helps reduce unnecessary communication between guest devices and trusted infrastructure.

This provides an additional security boundary for the HomeLAB.

---

## 10. Infrastructure Management Network

### Purpose

The infrastructure management network provides connectivity for administration and management of core infrastructure.

Unlike the six VLAN interfaces, this network operates through the FortiGate hardware switch interface.

It is not configured as a dedicated management VLAN.

### Managed Infrastructure

The management network supports administration of:

- FortiGate firewall
- Ubiquiti managed switch
- Proxmox virtualization host
- Additional infrastructure services

### Administrative Access

Administrative access to HomeLAB infrastructure has been configured, tested, and successfully utilized through SSH.

SSH provides remote command-line access for:

- Infrastructure administration
- Configuration management
- Troubleshooting
- System maintenance
- Network connectivity verification
- Remote service management

### Verified Administrative Capabilities

- Remote SSH administration
- Command-line configuration and troubleshooting
- Remote service management
- Security configuration management
- Network connectivity verification
- System maintenance and updates
- Administration without requiring direct console access

### Security Controls

The management design emphasizes restricting administrative access to authorized systems.

Controls include:

- Device authentication
- Firewall policy enforcement
- Restricted administrative access
- SSH-based remote management
- Device-level management configuration

Successful SSH connectivity confirms operational administrative access.

Verification of unauthorized access restrictions is a separate security validation activity.

---

## 11. VLAN Routing Architecture

The FortiGate serves as the primary Layer 3 routing device for the segmented network.

### Routing Responsibilities

The firewall provides:

- VLAN gateway interfaces
- Inter-VLAN routing
- WAN routing
- Network Address Translation
- Firewall policy enforcement

### Inter-VLAN Traffic Flow

```text
                  SOURCE DEVICE
                        |
                        |
                   SOURCE VLAN
                        |
                        |
                    FORTIGATE
                        |
                        |
                 FIREWALL POLICY
                    EVALUATION
                        |
              +---------+---------+
              |                   |
              |                   |
          PERMITTED             DENIED
              |                   |
              v                   v
       DESTINATION VLAN        DROPPED
              |
              |
       DESTINATION DEVICE
```

### Security Enforcement

Traffic between network segments is evaluated against the applicable FortiGate firewall policies.

Communication is permitted when a matching allow policy authorizes the traffic.

Traffic without an applicable allow policy is subject to the firewall's implicit deny behavior.

---

## 12. FortiGate Firewall Segmentation

The FortiGate provides centralized firewall policy enforcement between network segments.

The firewall utilizes:

- Explicit allow policies
- Explicit deny policies
- Implicit deny behavior
- Stateful traffic inspection

### Security VLAN Restrictions

Dedicated firewall policies restrict traffic originating from the Security VLAN toward trusted network segments.

These restrictions help:

- Prevent unauthorized connections into trusted networks
- Reduce opportunities for lateral movement
- Separate honeypot infrastructure from personal devices
- Restrict access to management infrastructure
- Protect work-related devices
- Maintain separation between security systems and wireless clients

### Stateful Firewall Behavior

The FortiGate operates as a stateful firewall.

Authorized connections initiated from permitted network segments can receive return traffic without requiring a separate reverse-direction allow policy.

For example, an authorized SSH connection from an administrative workstation to a security endpoint can receive return traffic through the established session.

This allows controlled administration while restricting unauthorized connections initiated from the Security VLAN.

### Policy Ordering

FortiGate firewall policies are evaluated according to their configured order.

The first matching policy determines the action applied to traffic.

Explicit deny policies must be positioned appropriately relative to allow policies to enforce the intended security design.

### Security Validation

Configured policies were reviewed during the October 20, 2026 infrastructure assessment.

The presence of a firewall policy confirms configuration, but complete isolation validation requires testing both authorized and unauthorized traffic paths.

---

## 13. Authorized Administrative Access

A designated administrative workstation is used to manage HomeLAB infrastructure.

The workstation operates within the trusted Home network.

Dedicated firewall policies permit administrative access to selected infrastructure systems.

### Managed Systems

Authorized administration includes:

- Proxmox virtualization host
- FortiGate firewall
- Ubiquiti managed switch
- Windows SOC endpoint
- Wazuh SIEM
- DShield honeypot
- Dedicated gaming server

### Management Technologies

- SSH
- HTTPS-based administration
- PowerShell
- Device-specific management interfaces

### Management Access Validation

SSH administrative connectivity has been tested and successfully utilized throughout the laboratory.

Configured firewall policies provide the intended network paths for administrative access.

Access to the FortiGate itself is additionally governed by interface administrative-access settings and applicable local-in controls.

### Security Considerations

Administrative access is restricted through a combination of:

- Network policies
- Device authentication
- Administrative permissions
- Management service configuration

Exact administrative access policies and management endpoints are excluded from public documentation.

---

## 14. Cross-VLAN Security Monitoring

The Gaming VLAN and Security VLAN contain systems that communicate for centralized security monitoring.

### Wazuh Communication

A dedicated firewall policy permits the gaming server to communicate with the Wazuh SIEM.

The policy limits permitted communication according to:

- Source network
- Destination network
- Source system
- Destination system
- Required monitoring service

### Monitoring Architecture

```text
                   GAMING VLAN
                        |
                        |
                  GAME SERVER
                        |
                        |
                    WAZUH AGENT
                        |
                        |
                    FORTIGATE
                        |
                        |
                 FIREWALL POLICY
                        |
                        |
                   SECURITY VLAN
                        |
                        |
                     WAZUH SIEM
```

### Security Benefits

- Limits unnecessary cross-VLAN communication
- Supports centralized security monitoring
- Maintains segmentation between gaming and security infrastructure
- Provides firewall logging for permitted monitoring traffic
- Demonstrates least-privilege network access control

---

## 15. Internet Access and NAT

The FortiGate provides internet access through its WAN interface.

Outbound firewall policies permit internet connectivity from authorized internal networks.

### Outbound Internet Access

The environment provides internet connectivity for:

- Home network
- Work network
- Security laboratory
- Gaming network
- Private wireless network
- Public wireless network
- Infrastructure management network

### Source NAT

Outbound Network Address Translation allows internal systems to access external networks through the FortiGate.

### Security Considerations

Outbound internet access is controlled through FortiGate firewall policies.

The current outbound policies permit broad services to external destinations.

Future security-hardening activities may include evaluating more restrictive outbound service requirements for sensitive infrastructure.

Exact outbound policy identifiers and service definitions are excluded from this public document.

---

## 16. External Service Exposure

The laboratory includes intentionally exposed services for security research and game server hosting.

### Honeypot Exposure

The FortiGate contains inbound firewall policies supporting the DShield/Cowrie honeypot.

The honeypot receives selected external traffic through destination NAT configurations.

### Palworld Server Exposure

A dedicated inbound policy supports external connectivity to the Palworld dedicated server.

The associated destination NAT configuration directs permitted application traffic to the gaming server.

### Security Considerations

Externally accessible services increase the laboratory's attack surface.

Security controls include:

- Dedicated firewall policies
- Network segmentation
- Destination NAT
- Endpoint monitoring
- Centralized security telemetry

Exact external ports, destination mappings, public IP addresses, and policy identifiers are excluded from public documentation.

---

## 17. VLAN Switching Infrastructure

The Ubiquiti Switch Lite 8 PoE provides managed Layer 2 switching.

The switch supports VLAN-aware connectivity between:

- FortiGate firewall
- Proxmox virtualization host
- Wireless access points
- Additional network devices

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

## 18. Security Monitoring Integration

The laboratory utilizes Wazuh for centralized security monitoring.

Wazuh collects security telemetry from Windows endpoints, Ubuntu Linux systems, the DShield/Cowrie honeypot, and the FortiGate firewall.

### Verified Wazuh Agents

The following endpoint monitoring status was verified through the Wazuh dashboard on October 20, 2026.

| Endpoint | Operating System | Monitoring Status |
|---|---|---|
| Windows SOC Endpoint | Windows 11 Pro | Active |
| DShield Honeypot | Ubuntu Linux | Active |
| Gaming Server | Windows 11 Pro | Active |

### Monitoring Summary

| Metric | Value |
|---|---|
| Registered Agents | 3 |
| Active Agents | 3 |
| Disconnected Agents | 0 |
| Pending Agents | 0 |
| Windows Endpoints | 2 |
| Ubuntu Endpoints | 1 |

All three registered endpoint agents were confirmed as actively connected.

### Network Security Telemetry

Wazuh collects telemetry from:

- Windows endpoint security events
- Linux endpoint activity
- DShield/Cowrie honeypot logs
- FortiGate firewall syslog

### Cross-VLAN Monitoring

The Security VLAN and Gaming VLAN contain actively monitored endpoints.

Wazuh agents communicate with centralized monitoring infrastructure through the network paths permitted by applicable firewall policies.

This provides centralized security visibility across multiple network segments.

---

## 19. FortiGate Address Objects

FortiGate address objects provide reusable references for firewall policies.

Address objects simplify the management of:

- Source addresses
- Destination addresses
- Infrastructure endpoints
- Security monitoring systems
- Administrative workstations
- Network segments

### Implemented Address Object Categories

The environment uses address objects representing:

- Firewall management infrastructure
- Managed switching infrastructure
- Proxmox virtualization host
- Administrative workstation
- DShield honeypot
- Windows SOC endpoint
- Wazuh SIEM
- Dedicated gaming server

### Configuration Management

Address objects allow firewall policies to reference logical infrastructure components rather than repeatedly entering address values.

This improves consistency and simplifies policy administration.

### Legacy Configuration Review

A legacy address object associated with a retired Ubuntu management workload was identified during infrastructure review.

The object had no observed references at the time of inspection.

It is a candidate for cleanup following confirmation that it is no longer required.

### Public Documentation

Exact address object names and IP assignments are intentionally excluded.

---

## 20. Security Considerations

### Least Privilege

Network communication is controlled through firewall policies that define permitted sources, destinations, and services.

### Network Isolation

Work devices, gaming infrastructure, security testing systems, and wireless clients are separated into dedicated network segments.

### Explicit Deny Policies

Dedicated deny policies restrict traffic originating from the Security VLAN toward trusted network segments.

### Administrative Access

SSH administration has been tested and successfully utilized across the laboratory infrastructure.

Dedicated firewall policies provide authorized access from the administrative workstation to selected infrastructure systems.

### Honeypot Exposure

The DShield/Cowrie honeypot is intentionally exposed to collect SSH-related attack telemetry.

External connectivity is controlled through FortiGate destination NAT and firewall policies.

### Security Logging

Firewall and endpoint telemetry is collected centrally through Wazuh.

This supports security investigations, detection engineering, and monitoring across multiple network segments.

### Management Service Hardening

A management service configuration review identified opportunities for additional administrative access hardening.

Recommended activities include:

- Review administrative access requirements.
- Disable unnecessary management services.
- Prefer encrypted management protocols.
- Verify administrative access restrictions.
- Review applicable management-plane controls.

These activities remain planned security reviews and are not documented as completed changes.

Specific management-plane configurations and potential weaknesses are excluded from public documentation.

---

## 21. Validation

The following network components and capabilities were reviewed during the October 20, 2026 infrastructure assessment.

### Verified Configuration

| Component | Validation | Status |
|---|---|---|
| Home Network | FortiGate interface configured | Verified |
| Work Network | FortiGate interface configured | Verified |
| Security Network | FortiGate interface configured | Verified |
| Gaming Network | FortiGate interface configured | Verified |
| Private Wireless Network | FortiGate interface configured | Verified |
| Public Wireless Network | FortiGate interface configured | Verified |
| Infrastructure Management | FortiGate hardware switch configured | Verified |
| SSH Administration | Successfully tested and utilized | Verified |
| Security VLAN Restrictions | Dedicated deny policies configured | Verified |
| Administrative Access | Dedicated management policies configured | Verified |
| Cross-VLAN Wazuh Monitoring | Gaming-to-Security policy configured | Verified |
| Outbound Internet Access | NAT-enabled policies configured | Verified |
| Windows SOC Endpoint | Wazuh agent connected | Active |
| DShield Honeypot | Wazuh agent connected | Active |
| Gaming Server | Wazuh agent connected | Active |

### Validation Limitations

Configured firewall policies establish intended access controls.

However, verifying that policies exist does not independently prove that every unauthorized communication path is blocked.

Additional traffic testing is required to validate effective segmentation.

### Additional Validation Opportunities

Future configuration reviews will include:

- VLAN parent interface assignments
- Switch port VLAN profiles
- Inter-VLAN firewall policy permissions
- Wireless SSID-to-VLAN mappings
- Administrative access restrictions
- DHCP scope configurations
- Network traffic logging
- Management service hardening
- External service exposure reviews
- Legacy address object cleanup

---

## 22. Future Expansion

### Windows Server and Active Directory

A Windows Server virtual machine is planned for deployment within Proxmox.

The server will provide:

- Active Directory Domain Services
- DNS
- Group Policy
- Centralized Identity Management
- Domain Authentication

The existing Windows SOC endpoint will be joined to the Active Directory domain.

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

## 23. Lessons Learned

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

### Key Technical Lessons

**VLAN segmentation provides logical separation.**

VLANs create separate broadcast domains but do not independently enforce all security requirements.

**Firewall policies govern routed communication.**

The FortiGate provides centralized control over permitted traffic between VLANs.

**Stateful firewalls support controlled administration.**

Authorized sessions can receive return traffic without requiring unrestricted reverse-direction access.

**Administrative access requires multiple controls.**

Firewall policies, authentication, and device-level configuration all contribute to management security.

**Centralized monitoring provides visibility across network segments.**

Wazuh collects telemetry from systems operating in different VLANs.

**Configuration verification is not the same as security validation.**

Reviewing policies confirms configuration, while traffic testing is needed to establish whether intended restrictions are effective.

---

## 24. Documentation References

Related documentation includes:

- `01_Project_Overview.md`
- `02_Hardware_Inventory.md`
- `03_Network_Architecture.md`
- `05_IP_Addressing_Plan.md`
- `06_Proxmox_Configuration.md`
- `07_FortiGate_Configuration.md`
- `08_Ubiquiti_Switch_Configuration.md`
- `09_Ubuntu_Server.md`
- `10_DShield_Honeypot.md`
- `11_Backup_and_Recovery.md`

---

## 25. Summary

The CyberSecurity-HomeLab uses six VLANs and a separate untagged infrastructure management network to provide logical network segmentation.

The FortiGate 90D provides centralized routing, Network Address Translation, and firewall policy enforcement.

The Ubiquiti managed switch transports VLAN traffic between connected infrastructure.

The environment separates:

- Trusted home devices
- Work-related systems
- Security testing infrastructure
- Gaming infrastructure
- Private wireless clients
- Public wireless clients
- Infrastructure management systems

Wazuh provides centralized security monitoring across multiple network segments.

The VLAN architecture supports practical experience with enterprise networking, firewall administration, access control, and security monitoring.

Future expansion will introduce Windows Server, Active Directory, and additional security automation workloads.

This document intentionally excludes sensitive operational details to support safe publication in a public cybersecurity portfolio.
