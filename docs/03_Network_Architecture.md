# 03 — Network Architecture

## 1. Overview

This document describes the physical and logical network architecture of the CyberSecurity-HomeLab environment.

The laboratory is designed using enterprise-inspired networking principles, including network segmentation, centralized firewall management, controlled communication between network segments, and centralized security monitoring.

The architecture supports:

- Infrastructure administration
- Security Operations Center (SOC) investigations
- Detection engineering
- Endpoint monitoring
- Network security administration
- Controlled adversary simulation
- Virtualization
- Security telemetry collection
- Future identity security monitoring

The environment combines physical networking equipment, virtualized infrastructure, dedicated security monitoring systems, and a segmented gaming server environment.

**Last Updated:** October 20, 2026

> **Security Note:** This document provides a sanitized representation of the operational network architecture. Public IP addresses, internal IP assignments, exact VLAN identifiers, administrative endpoints, firewall policy identifiers, device serial numbers, and sensitive configuration details are intentionally excluded.

---

## 2. Network Design Goals

The network was designed with the following objectives:

- Separate trusted and untrusted systems.
- Minimize opportunities for lateral movement.
- Centralize routing and firewall policy enforcement.
- Implement logical network segmentation using VLANs.
- Isolate security testing infrastructure from personal and work devices.
- Restrict administrative access to authorized systems.
- Collect network and endpoint security telemetry.
- Support controlled adversary simulation.
- Maintain reliable network connectivity.
- Support future infrastructure expansion.
- Provide practical experience with enterprise networking technologies.

The overall design emphasizes security, maintainability, visibility, and controlled communication between systems.

### Security Principles

The architecture incorporates several foundational security concepts.

**Defense in Depth**

Multiple security controls are used to reduce dependence on any single protective mechanism.

**Least Privilege**

Network access is intended to be limited to communication required for authorized operations.

**Network Segmentation**

Systems are separated according to their purpose and trust requirements.

**Centralized Monitoring**

Security telemetry is collected and analyzed through Wazuh.

**Controlled Administrative Access**

Infrastructure administration is restricted to authorized systems and accounts.

---

## 3. High-Level Network Architecture

The CyberSecurity-HomeLab utilizes a FortiGate 90D firewall as its primary network gateway and security enforcement point.

Internet connectivity is provided through a residential fiber connection.

The FortiGate connects the external network to the internal switching infrastructure.

A Ubiquiti Switch Lite 8 PoE provides managed Layer 2 connectivity between the firewall, virtualization infrastructure, wireless access points, and other network devices.

The primary virtualization platform is a Proxmox VE host running three active security virtual machines.

A dedicated physical Windows computer hosts a Palworld game server within a segmented gaming network.

### Physical and Logical Architecture

```text
                         INTERNET
                            |
                            |
                     ISP EQUIPMENT
                            |
                            |
                     FORTIGATE 90D
                    FIREWALL / GATEWAY
                            |
                            |
                   UBIQUITI MANAGED
                       SWITCH
                            |
             +--------------+--------------+
             |              |              |
             |              |              |
         PROXMOX VE      WIRELESS       OTHER WIRED
         HYPERVISOR    ACCESS POINTS     DEVICES
             |              |
             |              |
       +-----+-----+    +---+---+
       |     |     |    |       |
       |     |     |  PRIVATE  PUBLIC
       |     |     |  WIRELESS WIRELESS
       |     |     |
       v     v     v
    DSHIELD WINDOWS WAZUH
    HONEYPOT SOC VM  SIEM


              SEPARATE GAMING NETWORK
                        |
                        |
                   GAMING VLAN
                        |
                        |
                 PHYSICAL WINDOWS
                    GAME SERVER
                        |
                        |
                  PALWORLD SERVER
                        |
                        |
                    WAZUH AGENT
```

This diagram represents the logical relationships between infrastructure components.

It is not intended to disclose exact physical switch ports, interface assignments, or firewall configurations.

A detailed graphical network diagram will be added after the documentation refresh is completed.

---

## 4. Network Infrastructure Components

### 4.1 FortiGate Firewall

The FortiGate 90D serves as the primary network gateway and firewall.

Its responsibilities include:

- WAN connectivity
- Network routing
- VLAN gateway interfaces
- Inter-VLAN firewall policies
- Network Address Translation
- DHCP services
- Administrative access restrictions
- Traffic logging
- Syslog forwarding
- Controlled external service exposure

The FortiGate provides centralized control over communication between network segments.

Firewall policies permit authorized communication while restricting unnecessary access.

Traffic that does not match an applicable allow policy is subject to the firewall's implicit deny behavior.

### 4.2 Ubiquiti Managed Switch

The HomeLAB uses a UniFi Switch Lite 8 PoE.

The switch provides managed Layer 2 connectivity for the laboratory infrastructure.

Responsibilities include:

- VLAN-aware switching
- IEEE 802.1Q VLAN tagging
- Connectivity between network devices
- Power over Ethernet delivery
- Wireless access point connectivity
- Managed network administration
- Ethernet link monitoring

The switch transports tagged traffic between infrastructure components.

The FortiGate performs Layer 3 routing and firewall policy enforcement between network segments.

### 4.3 Wireless Access Points

Two Ubiquiti UAP-AC-Pro wireless access points provide network coverage across separate physical locations.

Both access points connect directly to the managed switch.

Power is delivered through PoE.

The access points support separate wireless networks for trusted and less-trusted devices.

Wireless connectivity is integrated into the laboratory's network segmentation strategy.

### 4.4 Proxmox Hypervisor

The primary virtualization host runs Proxmox VE 9.2.

The host provides virtualized infrastructure for:

- Security monitoring
- Endpoint testing
- Adversary simulation
- Honeypot operations
- Detection engineering

Proxmox virtual networking provides connectivity between guest systems and the physical network.

### 4.5 Dedicated Gaming Server

A dedicated physical Windows computer hosts the Palworld game server.

The server operates within a separate gaming network.

The environment provides additional experience with:

- Windows administration
- Dedicated application hosting
- Network segmentation
- Firewall policies
- DHCP reservations
- UDP application traffic
- REST API configuration
- Scheduled task automation
- Endpoint security monitoring

A Wazuh agent provides security telemetry from the gaming server.

---

## 5. Logical Network Segmentation

The environment utilizes multiple logical network segments to separate systems based on purpose and security requirements.

### Network Segment Inventory

| Network Segment | Primary Purpose | Trust Classification |
|---|---|---|
| Infrastructure Management | Network and virtualization infrastructure | Restricted |
| Home Network | Trusted personal devices | Trusted |
| Work Network | Work-related systems | Restricted |
| Security Network | SOC laboratory and security testing | Isolated Testing |
| Gaming Network | Dedicated gaming infrastructure | Segmented |
| Private Wireless | Trusted wireless devices | Trusted |
| Public Wireless | Guest and less-trusted devices | Less Trusted |

VLAN segmentation provides logical separation between these environments.

Inter-VLAN communication is controlled through FortiGate firewall policies.

### 5.1 Infrastructure Management

The infrastructure management network supports administration of core network and virtualization equipment.

Managed services include:

- FortiGate administration
- Proxmox administration
- Ubiquiti network management
- SSH administration
- Infrastructure monitoring

Management access is restricted to authorized administrative systems.

The management environment is not represented as a dedicated management VLAN.

Administrative restrictions are implemented through applicable network policies and device-level access controls.

### 5.2 Home Network

The Home network supports trusted personal devices.

It provides connectivity for authorized internal systems and personal computing equipment.

Communication with other network segments is governed by applicable firewall policies.

### 5.3 Work Network

A dedicated Work VLAN provides network separation for work-related systems.

The objective is to reduce unnecessary communication between work devices, personal devices, and security testing infrastructure.

This separation helps maintain distinct network boundaries between device categories.

### 5.4 Security Network

The Security VLAN hosts the primary virtualized cybersecurity workloads.

These include:

- DShield/Cowrie honeypot
- Windows SOC endpoint
- Wazuh SIEM

The Security VLAN provides a dedicated environment for security monitoring, controlled testing, and adversary simulation.

Firewall policies govern permitted communication between the Security VLAN and other network segments.

### 5.5 Gaming Network

The Gaming VLAN provides logical separation for gaming-related infrastructure.

A dedicated physical Windows computer hosts the Palworld server within this network.

The Gaming VLAN supports:

- Dedicated game server hosting
- DHCP reservations
- Controlled firewall access
- UDP application traffic
- Network isolation
- Endpoint security monitoring

The gaming server is monitored through its installed Wazuh agent.

### 5.6 Private Wireless Network

The Private wireless network supports trusted wireless devices.

Wireless traffic is associated with a dedicated VLAN.

The FortiGate controls routed communication between the Private wireless network and other network segments.

### 5.7 Public Wireless Network

The Public wireless network provides connectivity for less-trusted wireless devices.

Separating public wireless clients from trusted infrastructure reduces unnecessary network exposure.

The Public wireless network is integrated into the FortiGate's segmentation and policy enforcement architecture.

---

## 6. VLAN Architecture

The HomeLAB uses IEEE 802.1Q VLAN tagging to transport multiple logical networks across shared physical infrastructure.

### VLAN Responsibilities

VLANs provide:

- Logical separation of network traffic
- Independent broadcast domains
- Separation of device categories
- Simplified network organization
- Support for centralized firewall enforcement

### VLAN Traffic Flow

```text
                   NETWORK DEVICE
                         |
                         |
                   UBIQUITI SWITCH
                         |
                         |
                    VLAN TRAFFIC
                         |
                         |
                    FORTIGATE
                         |
                         |
                 FIREWALL POLICY
                    EVALUATION
                         |
              +----------+----------+
              |                     |
              |                     |
         AUTHORIZED             UNAUTHORIZED
          TRAFFIC                 TRAFFIC
              |                     |
              v                     v
           PERMITTED              DENIED
```

### VLAN Tagging

The managed switch supports tagged VLAN traffic between infrastructure devices.

The FortiGate maintains the applicable VLAN interfaces used for routing and policy enforcement.

Proxmox virtual networking supports VLAN tagging for virtual machine connectivity.

### Segmentation Limitations

VLANs provide logical separation but do not independently guarantee complete security isolation.

Effective segmentation also depends on:

- Firewall policies
- Device configuration
- Administrative access controls
- Endpoint security
- Network monitoring
- Ongoing configuration review

Detailed VLAN design is documented in:

`04_VLAN_Design.md`

---

## 7. Proxmox Virtual Networking

Proxmox provides virtual network connectivity for the security laboratory.

### Virtual Networking Components

The environment uses:

- Linux bridges
- VLAN-aware networking
- Virtual network interfaces
- Guest operating system networking
- FortiGate routing and security policies

### Network Design

The Proxmox host connects to the physical managed switching infrastructure.

Virtual machines use virtual network interfaces connected to the appropriate Proxmox bridge.

VLAN configuration associates the security workloads with the dedicated Security VLAN.

### Virtual Machine Network Architecture

```text
                  PROXMOX VE HOST
                         |
                         |
                 VLAN-AWARE BRIDGE
                         |
             +-----------+-----------+
             |           |           |
             |           |           |
             v           v           v
          DSHIELD      WINDOWS      WAZUH
          HONEYPOT     SOC VM       SIEM
             |           |           |
             +-----------+-----------+
                         |
                         |
                   SECURITY VLAN
                         |
                         |
                     FORTIGATE
```

The three active security virtual machines share the same logical security network.

Communication with other VLANs is subject to applicable FortiGate policies.

### Virtualization Inventory

| Virtual Workload | Operating System | Primary Function |
|---|---|---|
| DShield Honeypot | Ubuntu Server | Honeypot and Attack Telemetry |
| Windows SOC Endpoint | Windows 11 | Endpoint Monitoring and Security Testing |
| Wazuh SIEM | Ubuntu Server | Centralized Security Monitoring |

The retired general-purpose Ubuntu management VM is no longer part of the active environment.

Detailed Proxmox configuration is documented in:

`06_Proxmox_Configuration.md`

---

## 8. Network Routing

The FortiGate serves as the primary routing device for the segmented network.

### Routing Responsibilities

The firewall provides:

- Internal network gateway services
- Inter-VLAN routing
- WAN routing
- Network Address Translation
- Firewall policy enforcement

### Inter-VLAN Routing

Traffic between different VLANs is routed through the FortiGate.

This provides a centralized point for applying security policies.

### Example Traffic Flow

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
                       |
                       |
                DESTINATION VLAN
                       |
                       |
                DESTINATION DEVICE
```

Communication is permitted only when an applicable policy allows the requested traffic.

### Internet Routing

Outbound internet traffic traverses the FortiGate.

Network Address Translation allows internal systems to communicate with external services.

### Static Routing

Static routes may be used where required for specific infrastructure connectivity.

Exact route destinations and next-hop information are excluded from public documentation.

---

## 9. Firewall Security Architecture

The FortiGate provides centralized policy enforcement between network segments.

### Firewall Policy Objectives

Firewall policies are designed to:

- Permit required network communication
- Restrict unnecessary inter-VLAN access
- Control administrative connectivity
- Support security monitoring
- Separate security testing infrastructure
- Support selected externally accessible services

### Policy Evaluation

```text
                  NETWORK TRAFFIC
                         |
                         |
                     FORTIGATE
                         |
                         |
                 POLICY EVALUATION
                         |
             +-----------+-----------+
             |                       |
             |                       |
        MATCHING ALLOW          NO MATCHING
            POLICY              ALLOW POLICY
             |                       |
             v                       v
          PERMITTED                DENIED
```

### Security Enforcement

Firewall policies evaluate traffic using relevant characteristics such as:

- Source network
- Destination network
- Network service
- Protocol
- Applicable policy conditions

### Administrative Access

Management access to infrastructure components is restricted to authorized administrative devices and accounts.

SSH and web-based administration are used where appropriate.

Administrative access is controlled through firewall policies and device-level access restrictions.

### Public Documentation

The following operational details are intentionally excluded:

- Administrative interface addresses
- Exact firewall policy identifiers
- Detailed management access rules
- External service mappings
- Public IP addresses
- Sensitive device configurations

Detailed firewall architecture is documented in:

`07_FortiGate_Configuration.md`

---

## 10. Network Address Translation

The FortiGate provides Network Address Translation for internal and selected externally accessible services.

### Source NAT

Source NAT supports outbound internet connectivity for internal network devices.

### Destination NAT

Destination NAT is used where selected external services require controlled inbound connectivity.

### Security Considerations

Externally accessible services require additional attention to:

- Firewall policy scope
- Service exposure
- Network segmentation
- Logging
- Monitoring
- Patch management
- Access restrictions

### Honeypot Connectivity

The DShield/Cowrie honeypot receives selected external traffic through controlled firewall configurations.

The honeypot is separated from trusted internal infrastructure.

Exact public addresses, service ports, and destination mappings are not included in this document.

---

## 11. Security Monitoring Architecture

The HomeLAB uses Wazuh as its centralized security monitoring platform.

Wazuh collects and analyzes telemetry from multiple infrastructure components.

### Security Telemetry Sources

| Source | Collection Method | Purpose |
|---|---|---|
| Windows SOC Endpoint | Wazuh Agent | Windows Security Monitoring |
| DShield Honeypot | Wazuh Agent / Cowrie Logs | Attack Telemetry |
| Gaming Server | Wazuh Agent | Endpoint Monitoring |
| FortiGate Firewall | Syslog Forwarding | Network Security Events |

### Verified Monitoring Status

During the October 20, 2026 infrastructure review:

- Three endpoint agents were registered.
- All three endpoint agents were active.
- No disconnected endpoint agents were reported.
- No pending endpoint agents were reported.
- Two monitored endpoints were running Windows.
- One monitored endpoint was running Ubuntu Linux.

FortiGate firewall logs are forwarded separately through syslog and do not require an endpoint agent.

### Telemetry Collection Architecture

```text
                SECURITY TELEMETRY SOURCES

                +----------------------+
                |  FORTIGATE FIREWALL  |
                |    Network Syslog    |
                +----------+-----------+
                           |
                           |
                +----------v-----------+
                |                      |
                |      WAZUH SIEM      |
                |                      |
                +----------^-----------+
                           |
             +-------------+-------------+
             |             |             |
             |             |             |
        +----+----+   +----+----+   +----+----+
        | WINDOWS |   | DSHIELD |   | GAMING  |
        | SOC VM  |   | HONEYPOT|   | SERVER  |
        +---------+   +---------+   +---------+
             |             |             |
             |             |             |
          WAZUH         WAZUH         WAZUH
          AGENT         AGENT         AGENT
```

This diagram illustrates logical telemetry relationships rather than exact transport protocols or network routes.

### Wazuh Responsibilities

The Wazuh platform provides:

- Security event collection
- Log analysis
- Detection rule processing
- Alert generation
- Security investigation
- Detection engineering
- Event correlation

### Custom Detection Engineering

Custom detection rules have been developed and tuned to analyze security telemetry.

Detection engineering activities include:

- Reviewing event fields
- Identifying relevant log patterns
- Developing detection conditions
- Testing rule behavior
- Investigating false positives
- Reducing unnecessary alert noise
- Validating security event generation

The Wazuh environment supports practical SOC investigation and security engineering workflows.

---

## 12. Windows Endpoint Monitoring

The Windows SOC endpoint provides a dedicated environment for security telemetry collection and controlled testing.

### Installed Monitoring Components

- Windows Event Logs
- Sysmon
- Windows Defender
- PowerShell Logging
- Wazuh Agent

### Monitoring Capabilities

These components provide visibility into:

- Process execution
- Parent and child processes
- Windows security events
- PowerShell activity
- Authentication events
- Endpoint security activity
- Controlled adversary simulations

### Security Operations Use Cases

The endpoint supports:

- Alert investigation
- Process tree analysis
- Detection validation
- MITRE ATT&CK mapping
- Security event correlation
- Controlled attack simulation

The dedicated gaming server is also monitored through its installed Wazuh agent.

---

## 13. Honeypot Security Architecture

The DShield/Cowrie honeypot provides a controlled environment for observing unsolicited external activity.

### Honeypot Purpose

The honeypot collects telemetry associated with:

- Unauthorized connection attempts
- Credential guessing
- Authentication failures
- Automated scanning
- Commands submitted during emulated sessions
- Suspicious network activity

### Network Placement

The honeypot operates within the dedicated Security VLAN.

Selected external traffic is directed to the honeypot through controlled FortiGate configurations.

The honeypot is separated from trusted personal and work infrastructure.

### Monitoring Integration

Honeypot telemetry is integrated into Wazuh.

This supports:

- Security event analysis
- Alert investigation
- Custom detection development
- Detection rule tuning
- Threat behavior observation
- SOC training

### Security Considerations

The honeypot is intentionally exposed to selected external traffic.

This makes isolation, monitoring, and access control important aspects of its design.

The public repository does not disclose exact external service mappings or management configurations.

Detailed honeypot documentation is maintained in:

`10_DShield_Honeypot.md`

---

## 14. Adversary Simulation Architecture

The laboratory includes a dedicated Windows 11 SOC endpoint for controlled security testing.

AtomicSOC is installed on the Windows SOC endpoint.

AtomicSOC is a custom Python and FastAPI application that provides a web interface for executing selected Atomic Red Team tests.

### Testing Workflow

```text
                    ATOMICSOC
                   WEB INTERFACE
                        |
                        |
                        v
                  FASTAPI BACKEND
                        |
                        |
                        v
                  ATOMIC RED TEAM
                        |
                        |
                        v
                 WINDOWS SOC ENDPOINT
                        |
                        |
                        v
                  WINDOWS TELEMETRY
                        |
                        |
                        v
                    WAZUH AGENT
                        |
                        |
                        v
                     WAZUH SIEM
                        |
                        |
                        v
                 SECURITY INVESTIGATION
```

### Testing Capabilities

The environment supports practical experience with:

- MITRE ATT&CK techniques
- Process execution monitoring
- Windows event analysis
- Detection validation
- Security alert investigation
- Controlled security testing

### Implemented AtomicSOC Features

- Web-based test execution
- Python FastAPI backend
- Atomic Red Team integration
- MITRE ATT&CK technique mapping
- Randomized test selection
- Controlled test execution

### Validated Test Categories

Selected tests have been successfully executed for:

- Process Discovery
- File Deletion
- System Network Configuration Discovery

These tests generate controlled endpoint activity for security analysis.

### Security Boundaries

Adversary simulations are conducted within the laboratory environment.

The objective is to generate observable security events and evaluate monitoring capabilities.

---

## 15. Network Security Design

### Network Segmentation

Network segmentation is implemented through VLANs and firewall policies.

This design helps limit unnecessary communication between systems with different trust requirements.

Benefits include:

- Reduced exposure between network segments
- Improved traffic control
- Separation of trusted and less-trusted devices
- Greater visibility into permitted network communication
- Improved administrative access control

### Firewall Policy Enforcement

The FortiGate controls traffic between network segments.

Policies are configured to permit authorized communication while restricting unnecessary access.

The firewall's implicit deny behavior blocks traffic that does not match an applicable allow policy.

### Administrative Access

Management access to infrastructure components is restricted to authorized administrative devices and accounts.

Administrative access is controlled through applicable network policies and device-level authentication.

### Honeypot Isolation

The DShield/Cowrie honeypot is separated from trusted infrastructure.

Its external exposure is limited to selected services required for honeypot operation.

### Endpoint Monitoring

Windows and Linux endpoints provide security telemetry through Wazuh agents and operating system logging.

### Centralized Security Visibility

Wazuh provides a common platform for analyzing endpoint and network security events.

---

## 16. Network Security Monitoring

The network architecture supports centralized security visibility through Wazuh.

### Monitoring Capabilities

Current monitoring includes:

- Firewall event collection
- Windows endpoint telemetry
- Linux endpoint telemetry
- SSH honeypot activity
- Windows process monitoring
- PowerShell activity
- Custom detection rules
- Security alert investigation

### Investigation Workflow

```text
                 SECURITY EVENT
                       |
                       |
                       v
                 LOG COLLECTION
                       |
                       |
                       v
                  WAZUH ANALYSIS
                       |
                       |
                       v
                DETECTION RULES
                       |
                       |
                       v
                 ALERT REVIEW
                       |
                       |
                       v
                  INVESTIGATION
                       |
                       |
                       v
                DETECTION TUNING
```

The objective is to improve visibility into suspicious activity across both network and endpoint systems.

---

## 17. Infrastructure Management

The HomeLAB infrastructure is administered through a combination of secure remote management tools.

### Management Technologies

- SSH
- PowerShell
- Web-based administration
- Proxmox management tools
- FortiGate administration
- UniFi Network
- Wazuh Dashboard

### Management Objectives

Administrative workflows emphasize:

- Authorized access
- Configuration verification
- Controlled changes
- Troubleshooting
- System maintenance
- Security monitoring
- Documentation

### Change Management

Infrastructure changes are researched, implemented, tested, and documented.

Snapshots and backups are used where appropriate before significant changes.

Configuration baselines support troubleshooting and recovery.

---

## 18. Future Infrastructure Expansion

The network architecture is designed to support additional infrastructure and security capabilities.

### 18.1 Windows Server and Active Directory

A Windows Server virtual machine is planned for deployment within Proxmox.

The server will provide:

- Active Directory Domain Services
- DNS services
- Group Policy management
- Centralized identity management
- Domain authentication

The existing Windows SOC endpoint will be joined to the Active Directory domain.

Security monitoring will be expanded to include authentication activity and identity-related security events.

### 18.2 Active Directory Security Monitoring

Future detection engineering activities will include:

- Failed authentication monitoring
- Password spraying detection
- Account enumeration
- Privileged group changes
- Suspicious account creation
- Domain authentication anomalies

These capabilities will expand the laboratory's security monitoring coverage.

### 18.3 AI-Powered SOC Job Discovery Platform

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

### 18.4 Additional Security Automation

Future improvements may include:

- Expanded AtomicSOC functionality
- Automated detection testing
- Security event enrichment
- Additional endpoint monitoring
- Python-based investigation utilities
- PowerShell security automation

---

## 19. Design Decisions

### Centralized Routing

The FortiGate provides a centralized routing and security enforcement point.

### VLAN-Based Segmentation

Logical network separation allows multiple security zones to operate over shared physical infrastructure.

### Dedicated Security Network

Security testing and monitoring workloads operate within a dedicated network segment.

### Managed Switching

The UniFi switch provides VLAN-aware Layer 2 connectivity and centralized management.

### Dedicated Wireless Networks

Separate wireless networks support devices with different trust requirements.

### Centralized Security Monitoring

Wazuh collects and analyzes security telemetry from multiple infrastructure components.

### Virtualized Security Workloads

Proxmox provides a flexible platform for deploying and managing cybersecurity systems.

### Controlled External Exposure

Selected services are exposed through firewall configurations rather than unrestricted access to internal infrastructure.

### Public Documentation Security

The public repository emphasizes architecture, technical implementation, and demonstrated skills while excluding sensitive operational identifiers.

---

## 20. Lessons Learned

Designing and maintaining the HomeLAB network has provided practical experience with:

- Enterprise-inspired network architecture
- VLAN segmentation
- Managed Layer 2 switching
- Inter-VLAN routing
- Firewall policy administration
- Network Address Translation
- Wireless network segmentation
- Proxmox virtual networking
- Security telemetry collection
- Endpoint monitoring
- Honeypot integration
- Network troubleshooting
- Security testing
- Infrastructure documentation

### Key Technical Lessons

**VLANs provide logical separation, not complete security by themselves.**

Firewall policies and access controls are necessary to govern communication between network segments.

**Centralized routing simplifies security enforcement.**

Using the FortiGate as the primary Layer 3 device provides a consistent point for applying network policies.

**Network visibility is essential for security investigations.**

Collecting firewall and endpoint telemetry provides greater context than relying on a single data source.

**Virtualization supports flexible security testing.**

Proxmox allows independent security workloads to share physical resources while maintaining separate guest environments.

**Controlled adversary simulation improves detection engineering.**

Generating known test activity provides an opportunity to evaluate telemetry collection and detection behavior.

**Accurate documentation supports troubleshooting and future expansion.**

Documenting network architecture makes it easier to understand dependencies, identify configuration issues, and plan infrastructure changes.

---

## 21. Documentation References

Additional configuration details are maintained in the following documents:

- `01_Project_Overview.md`
- `02_Hardware_Inventory.md`
- `04_VLAN_Design.md`
- `05_IP_Addressing_Plan.md`
- `06_Proxmox_Configuration.md`
- `07_FortiGate_Configuration.md`
- `08_Ubiquiti_Switch_Configuration.md`
- `09_Ubuntu_Server.md`
- `10_DShield_Honeypot.md`
- `11_Backup_and_Recovery.md`

Detailed Wazuh, Windows SOC endpoint, and AtomicSOC documentation will be maintained in dedicated project documents.

---

## 22. Summary

The CyberSecurity-HomeLab combines physical networking infrastructure, VLAN-based segmentation, Proxmox virtualization, centralized firewall enforcement, and security monitoring.

The environment includes:

- FortiGate firewall
- Ubiquiti managed switch
- Two wireless access points
- Segmented internal networks
- Proxmox virtualization host
- DShield/Cowrie honeypot
- Windows SOC endpoint
- Wazuh SIEM
- Dedicated gaming server
- Centralized endpoint and network security telemetry

The architecture supports practical experience with network administration, virtualization, security operations, detection engineering, and controlled adversary simulation.

Future expansion will introduce Windows Server, Active Directory, identity-focused security monitoring, and containerized automation workloads.

This document intentionally excludes sensitive operational details to support safe publication in a public cybersecurity portfolio.
