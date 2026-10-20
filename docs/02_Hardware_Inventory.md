# 02 — Hardware Inventory

## 1. Overview

This document provides a comprehensive inventory of the physical hardware and virtual infrastructure supporting the CyberSecurity-HomeLab project.

The HomeLAB is designed to provide hands-on experience with enterprise networking, virtualization, cybersecurity monitoring, detection engineering, system administration, and infrastructure management.

Maintaining an accurate hardware inventory supports:

- Infrastructure administration
- Capacity planning
- Troubleshooting
- Security monitoring
- Resource allocation
- Backup and recovery
- Infrastructure lifecycle management
- Future expansion

The inventory includes networking equipment, virtualization hardware, storage devices, dedicated servers, and virtual machines.

**Last Updated:** October 20, 2026

> **Security Note:** This document is a sanitized representation of the operational HomeLAB infrastructure. Public IP addresses, internal management addresses, exact virtual machine identifiers, storage identifiers, credentials, device serial numbers, and sensitive configuration details are intentionally excluded.

---

## 2. Internet Connectivity

The HomeLAB uses a residential fiber internet connection.

Internet connectivity is delivered through ISP-provided equipment to the FortiGate firewall.

The FortiGate serves as the primary network gateway and security enforcement point.

### Connection Architecture

```text
                 INTERNET
                     |
                     |
              ISP EQUIPMENT
                     |
                     |
             FORTIGATE FIREWALL
                     |
                     |
             MANAGED SWITCHING
                     |
                     |
              HOMELAB DEVICES
```

### Internet Connection

| Component | Configuration |
|---|---|
| Connection Type | Residential Fiber |
| Network Handoff | ISP-Provided Equipment |
| Primary Gateway | FortiGate Firewall |
| Internal Distribution | Managed Ethernet Switch |
| Security Enforcement | FortiGate Firewall |

The firewall provides routing, network segmentation, Network Address Translation (NAT), and traffic filtering for the internal environment.

---

## 3. Firewall Infrastructure

The HomeLAB uses a Fortinet FortiGate firewall as its primary security appliance.

### Hardware Inventory

| Component | Configuration |
|---|---|
| Manufacturer | Fortinet |
| Model | FortiGate 90D |
| Device Type | Network Security Appliance |
| Primary Role | Network Gateway and Security Enforcement |
| Network Segmentation | VLAN-Based |
| Routing | Inter-VLAN and WAN Routing |
| Network Address Translation | Configured |
| Firewall Policies | Configured |
| Security Logging | Forwarded to Wazuh |
| Management | Restricted Administrative Access |

### Responsibilities

The FortiGate provides centralized network security and traffic management.

Primary responsibilities include:

- Internet gateway functionality
- VLAN routing
- Inter-VLAN access control
- Stateful firewall inspection
- Network Address Translation (NAT)
- DHCP services for applicable network segments
- Network traffic logging
- Syslog forwarding
- Administrative access restrictions
- Controlled exposure of selected services

### Security Design

The FortiGate acts as the primary Layer 3 routing and security enforcement device.

The environment uses explicit firewall policies to permit required communication between network segments.

Traffic that does not match an applicable allow policy is subject to the firewall's implicit deny behavior.

### Security Monitoring Integration

FortiGate security logs are forwarded to the Wazuh SIEM using syslog.

This provides centralized visibility into network activity and supports security investigations.

### Public Documentation Considerations

Exact firewall policy identifiers, administrative endpoints, external service mappings, and public IP addresses are excluded from the public repository.

Detailed firewall architecture is documented separately in:

`07_FortiGate_Configuration.md`

---

## 4. Switching Infrastructure

The HomeLAB uses a Ubiquiti managed Ethernet switch to provide Layer 2 network connectivity.

### Hardware Inventory

| Component | Configuration |
|---|---|
| Manufacturer | Ubiquiti |
| Model | UniFi Switch Lite 8 PoE |
| Device Type | Managed Layer 2 Switch |
| Ethernet Ports | 8 Gigabit Ethernet |
| PoE Support | PoE/PoE+ |
| PoE-Capable Ports | 4 |
| VLAN Support | IEEE 802.1Q |
| Primary Role | Layer 2 Switching |
| Management Platform | UniFi Network |

### Responsibilities

The managed switch provides connectivity between:

- FortiGate firewall
- Proxmox virtualization host
- Wireless access points
- Dedicated gaming server
- Additional wired infrastructure

Primary responsibilities include:

- VLAN-aware Ethernet switching
- Tagged VLAN transport
- Network segmentation support
- Power over Ethernet delivery
- Physical device connectivity
- Network traffic monitoring
- Centralized switch management

### Network Integration

The switch transports VLAN-tagged traffic between connected infrastructure.

The FortiGate provides Layer 3 routing and firewall policy enforcement.

This separation allows multiple logical networks to share physical switching infrastructure.

### Management

The UniFi Network application provides centralized management capabilities, including:

- Switch port configuration
- VLAN assignments
- Link speed monitoring
- PoE management
- Connected device visibility
- Firmware status
- Traffic statistics

Detailed switching architecture is documented in:

`08_Ubiquiti_Switch_Configuration.md`

---

## 5. Wireless Infrastructure

The HomeLAB uses two Ubiquiti wireless access points.

### Hardware Inventory

| Component | Configuration |
|---|---|
| Manufacturer | Ubiquiti |
| Model | UAP-AC-Pro |
| Quantity | 2 |
| Power Delivery | Power over Ethernet |
| Network Connectivity | Wired Ethernet |
| Management Platform | UniFi Network |
| Wireless Bands | 2.4 GHz and 5 GHz |
| Wireless Segmentation | Private and Public Networks |

### Responsibilities

The wireless infrastructure provides connectivity for trusted and less-trusted wireless devices.

Both access points connect directly to the managed switch and receive power through PoE.

### Physical Deployment

The access points are installed in separate physical locations.

One provides wireless coverage for the primary building.

The second extends wireless coverage to another building on the property.

Both access points use wired Ethernet backhaul connections.

### Wireless Network Design

The environment provides separate wireless networks for:

- Private wireless clients
- Public or guest wireless clients

Wireless traffic is associated with the appropriate VLAN.

The FortiGate controls routed communication between wireless and wired network segments.

### Operational Monitoring

The UniFi Network application provides visibility into:

- Access point connectivity
- Client associations
- Ethernet link speeds
- Device status
- PoE utilization
- Firmware information

### Planned Maintenance

During infrastructure verification, both access points were observed negotiating Ethernet connections at Fast Ethernet speeds.

The access point ports use automatic link negotiation.

Further cable and link-negotiation troubleshooting is planned.

---

## 6. Primary Virtualization Host

The HomeLAB uses a dedicated mini PC running Proxmox Virtual Environment.

### Hardware Specifications

| Component | Configuration |
|---|---|
| Platform | Mini PC |
| Processor | Intel Core i7-11370H |
| Processor Generation | 11th Generation Intel |
| Physical CPU Cores | 4 |
| Logical CPU Threads | 8 |
| Installed Memory | 32 GB |
| Primary Storage | 512 GB-Class NVMe SSD |
| Hypervisor | Proxmox VE 9.2 |
| Boot Mode | UEFI |
| Primary Role | Virtualization Infrastructure |

### Responsibilities

The Proxmox host provides the primary computing platform for the HomeLAB.

It hosts virtual machines supporting:

- Security monitoring
- Endpoint telemetry collection
- Adversary simulation
- Honeypot operations
- Detection engineering
- Infrastructure administration

Virtualization allows these services to operate as independent workloads while sharing physical computing resources.

### Virtualization Capabilities

Proxmox provides:

- Virtual machine management
- Virtual networking
- VLAN-aware network connectivity
- Storage management
- Virtual machine snapshots
- Backup scheduling
- Resource allocation
- Remote administration
- QEMU guest agent integration

### Resource Allocation

The host currently supports three active security virtual machines.

| Resource | Configuration |
|---|---|
| Physical CPU Cores | 4 |
| Logical CPU Threads | 8 |
| Installed RAM | 32 GB |
| Active Virtual Machines | 3 |
| Configured VM Memory | 20 GB |
| Provisioned VM Boot Disk Capacity | 200 GB |
| Active LXC Containers | 0 |

Configured virtual machine memory represents allocated guest memory rather than total physical host memory utilization.

Provisioned virtual disk capacity represents configured disk sizes rather than actual physical storage consumption.

### Capacity Planning

Additional infrastructure is planned, including a Windows Server domain controller.

Future resource allocation will consider:

- Available memory
- CPU utilization
- Storage capacity
- Existing VM requirements
- Expected application workloads
- Backup requirements

---

## 7. Proxmox Storage Architecture

The Proxmox host uses internal NVMe storage for its operating system and active virtual machine disks.

### Primary Storage

| Component | Configuration |
|---|---|
| Storage Type | NVMe SSD |
| Capacity | Approximately 500 GB |
| Primary Purpose | Hypervisor and VM Storage |
| Virtual Disk Storage | LVM-Thin |
| Additional Storage | Local Directory Storage |

### Storage Responsibilities

The internal storage supports:

- Proxmox operating system
- Virtual machine disks
- VM configuration metadata
- Installation media
- Local storage operations

### Storage Design

The environment separates active virtual machine storage from the dedicated external backup destination.

This provides distinct storage locations for operational workloads and recovery archives.

### Limitations

The primary virtualization host uses a single internal physical storage device.

A failure of this device could affect multiple virtual machines simultaneously.

External backup storage provides an additional recovery option.

---

## 8. Backup Storage

The HomeLAB uses dedicated external storage for virtual machine backup and recovery.

### Hardware Inventory

| Component | Configuration |
|---|---|
| Manufacturer | Seagate |
| Device Type | External USB Hard Drive |
| Capacity | 5 TB |
| Filesystem | ext4 |
| Hypervisor Integration | Proxmox Directory Storage |
| Primary Purpose | Virtual Machine Backups |
| Additional Purpose | Recovery and Configuration Preservation |

### Backup Strategy

The environment uses:

- Proxmox virtual machine backups
- Virtual machine snapshots
- Configuration baselines
- Recovery points before major infrastructure changes
- Automated backup scheduling
- Defined backup retention policies

### Backup Configuration

| Setting | Configuration |
|---|---|
| Backup Mode | Snapshot |
| Compression | Zstandard |
| Schedule | Daily, Early Morning |
| Daily Retention | 7 |
| Weekly Retention | 4 |
| Monthly Retention | 3 |
| Protected Workloads | Three Active Security VMs |

### Current Backup Status

Manual backups have been successfully created for all active security virtual machines.

The scheduled backup job is enabled.

Automated execution under the updated schedule and controlled restore testing remain outstanding validation tasks.

### Storage Recovery Experience

During infrastructure maintenance, the external backup drive experienced a filesystem issue following an unexpected USB disconnection.

The filesystem was investigated and repaired using Linux administration tools.

Backup storage access was restored, and new backup archives were successfully created.

### Limitations

The external drive is physically connected to the Proxmox host.

An additional independent backup destination would improve resilience against shared hardware and physical failures.

Detailed backup procedures are documented in:

`11_Backup_and_Recovery.md`

---

## 9. Dedicated Physical Server Infrastructure

### Palworld Dedicated Server

The HomeLAB includes a dedicated physical Windows computer hosting a Palworld game server.

Although the application is gaming-related, the infrastructure provides additional experience with Windows administration, networking, application hosting, and security monitoring.

### Server Inventory

| Component | Configuration |
|---|---|
| Platform | Dedicated Physical Computer |
| Operating System | Windows |
| Primary Application | Palworld Dedicated Server |
| Installation Method | SteamCMD |
| Network Segment | Dedicated Gaming VLAN |
| IP Assignment | DHCP Reservation |
| Network Security | FortiGate Firewall |
| Security Monitoring | Wazuh Agent |
| Application Management | Windows Scheduled Task |
| API Integration | REST API Enabled |

### Implemented Capabilities

- Dedicated game server deployment
- SteamCMD installation and administration
- Windows Scheduled Task automation
- DHCP reservation management
- VLAN-based network segmentation
- Firewall policy configuration
- Application-specific UDP traffic management
- REST API configuration
- Wazuh endpoint monitoring

### Security Integration

The server operates within a dedicated network segment.

This separates gaming infrastructure from the security testing and trusted internal environments.

The Wazuh agent provides endpoint monitoring capabilities.

### Hardware Verification

Detailed physical hardware specifications have not yet been verified.

They will be added when available.

---

## 10. Virtual Machine Inventory

The Proxmox host currently operates three virtual machines supporting security monitoring, endpoint telemetry collection, and adversary simulation.

### Active Virtual Machines

| Workload | Operating System | Virtual CPUs | Memory | Boot Disk | Status |
|---|---|---|---|---|---|
| DShield Honeypot | Ubuntu Server 26.04 LTS | 2 | 4 GB | 40 GB | Running |
| Windows SOC Endpoint | Windows 11 | 4 | 8 GB | 80 GB | Running |
| Wazuh SIEM | Ubuntu Server 26.04.1 LTS | 4 | 8 GB | 80 GB | Running |

### Resource Allocation

| Resource | Total |
|---|---|
| Active Virtual Machines | 3 |
| Configured Virtual CPUs | 10 |
| Configured VM Memory | 20 GB |
| Provisioned Boot Disk Capacity | 200 GB |
| LXC Containers | 0 |
| Available Logical Host CPUs | 8 |

The total number of configured virtual CPUs exceeds the host's physical core count.

This is possible because Proxmox schedules virtual CPUs across the available host processing resources.

The environment does not require every virtual CPU to be fully utilized simultaneously.

### Virtualization Architecture

```text
                  PROXMOX HYPERVISOR
                          |
             +------------+------------+
             |            |            |
             v            v            v
          DSHIELD       WINDOWS       WAZUH
          HONEYPOT      SOC VM        SIEM
             |            |            |
             +------------+------------+
                          |
                          v
                   SECURITY VLAN
                          |
                          v
                   FORTIGATE
```

The virtual machines operate within a dedicated security network.

Inter-VLAN access is controlled by the FortiGate.

---

## 11. DShield Honeypot Virtual Machine

### Configuration

| Component | Configuration |
|---|---|
| Operating System | Ubuntu Server 26.04 LTS |
| Virtual CPUs | 2 |
| Memory | 4 GB |
| Boot Disk | 40 GB |
| Firmware | UEFI |
| Primary Application | DShield/Cowrie Honeypot |
| Security Integration | Wazuh SIEM |
| Guest Agent | Enabled |

### Responsibilities

The honeypot provides an isolated environment for observing unsolicited internet activity.

Collected telemetry may include:

- Unauthorized connection attempts
- Credential guessing
- Authentication failures
- Commands submitted during emulated sessions
- Automated attack behavior
- Suspicious network activity

### Security Architecture

The honeypot operates within a dedicated Security VLAN.

Selected external traffic is directed to the environment through controlled firewall policies.

The honeypot is separated from trusted internal infrastructure.

### Wazuh Integration

Honeypot telemetry is integrated into Wazuh for centralized monitoring and custom detection development.

This supports:

- Log analysis
- Alert investigation
- Detection engineering
- Threat monitoring
- SOC training

---

## 12. Windows SOC Endpoint Virtual Machine

### Configuration

| Component | Configuration |
|---|---|
| Operating System | Windows 11 |
| Virtual CPUs | 4 |
| Memory | 8 GB |
| Boot Disk | 80 GB |
| Firmware | UEFI |
| Virtualization Platform | Proxmox |
| Primary Role | SOC Endpoint Testing |
| Security Monitoring | Wazuh Agent |
| Guest Agent | Enabled |

### Installed Security Components

- Sysmon
- Windows Defender
- PowerShell Logging
- Wazuh Agent
- OpenSSH Server
- Atomic Red Team
- AtomicSOC

### Responsibilities

The Windows SOC endpoint provides a dedicated environment for:

- Windows security event collection
- Process execution monitoring
- PowerShell activity logging
- Endpoint security monitoring
- Controlled adversary simulation
- MITRE ATT&CK technique testing
- Detection validation

### AtomicSOC Integration

The endpoint hosts AtomicSOC, a custom Python and FastAPI application.

AtomicSOC provides a web-based interface for executing selected Atomic Red Team tests.

The application supports controlled security event generation and detection validation.

### Planned Active Directory Integration

The Windows endpoint is planned for integration into a future Active Directory domain.

This will support additional identity-focused security testing and Windows domain monitoring.

---

## 13. Wazuh SIEM Virtual Machine

### Configuration

| Component | Configuration |
|---|---|
| Operating System | Ubuntu Server 26.04.1 LTS |
| Virtual CPUs | 4 |
| Memory | 8 GB |
| Boot Disk | 80 GB |
| Virtualization Platform | Proxmox |
| Primary Application | Wazuh SIEM |
| Primary Role | Centralized Security Monitoring |
| Guest Agent | Enabled |

### Responsibilities

The Wazuh virtual machine provides centralized security event collection and analysis.

The environment collects telemetry from:

- Windows SOC endpoint
- FortiGate firewall
- DShield/Cowrie honeypot

Additional security monitoring has also been implemented for the dedicated gaming server.

### Security Monitoring Capabilities

- Centralized security event collection
- Log analysis
- Custom detection rule processing
- Security alert generation
- Alert investigation
- Detection engineering
- Security event correlation

### Detection Engineering

Custom Wazuh detection rules have been developed and tuned.

This work has included:

- Reviewing security events
- Identifying relevant log fields
- Creating custom detection conditions
- Testing rule behavior
- Investigating unexpected alerts
- Reducing unnecessary alert noise

The Wazuh environment serves as the primary SIEM platform for HomeLAB security investigations.

---

## 14. Retired Infrastructure

### Ubuntu Management Virtual Machine

A general-purpose Ubuntu management virtual machine was previously deployed.

The VM was retired during infrastructure consolidation in October 2026.

### Retirement Reason

The virtual machine was no longer required for current operations.

Removing it simplified the virtual infrastructure and reclaimed provisioned storage resources.

### Lessons Learned

Infrastructure should be periodically reviewed for unused resources.

Retiring unnecessary systems can:

- Reduce resource consumption
- Simplify administration
- Reduce maintenance requirements
- Reduce the number of systems requiring security updates
- Improve infrastructure organization

A historical backup archive was retained following retirement.

---

## 15. Planned Virtual Infrastructure

### Windows Server — Active Directory Domain Controller

A Windows Server virtual machine is planned for deployment on Proxmox.

The server will introduce centralized identity management and authentication services into the HomeLAB.

#### Planned Roles

- Active Directory Domain Services (AD DS)
- DNS Server
- Group Policy Management
- Domain Authentication
- Security Auditing

#### Planned Integration

The existing Windows SOC endpoint will be joined to the Active Directory domain.

This expansion will support:

- Domain authentication monitoring
- Windows security event collection
- Group Policy administration
- Account and group management
- Identity-based attack simulations
- Active Directory detection engineering

Resource allocation will be determined according to available Proxmox capacity.

---

### AI-Powered SOC Job Discovery Container

A lightweight Linux container is planned for hosting an automated cybersecurity job discovery application.

The application will use Python and AI-assisted analysis.

#### Planned Capabilities

- Automated cybersecurity job searches
- Job board API integration
- Permitted web scraping
- Job description analysis
- AI-assisted job classification
- Duplicate detection
- Scheduled execution
- Job tracking
- Automated notifications

#### Infrastructure

Proxmox LXC is the preferred deployment platform.

Containerization will allow the application to operate without requiring a dedicated full virtual machine.

Resource allocation will be determined during development.

---

## 16. Future Hardware Considerations

Potential infrastructure improvements include:

- Additional virtualization resources
- Dedicated network-attached storage
- Expanded backup storage
- Additional Windows endpoints
- Additional security monitoring systems
- Increased memory capacity
- Additional networking equipment

Future expansion will be evaluated according to:

- Resource utilization
- Storage requirements
- Application performance
- Security requirements
- Project objectives

---

## 17. Design Decisions

### Centralized Virtualization

Proxmox provides a single platform for hosting the primary security workloads.

### Network Segmentation

The FortiGate and UniFi switching infrastructure provide separation between network segments.

### Dedicated Security Infrastructure

Security monitoring and testing workloads operate independently of personal and work devices.

### Centralized Security Monitoring

Wazuh collects and analyzes security telemetry from multiple sources.

### External Backup Storage

Dedicated storage provides recovery points separate from the primary VM disks.

### Resource Efficiency

Virtualization allows multiple security workloads to share physical computing resources.

### Infrastructure Consolidation

Unused virtual machines are retired when they no longer serve an operational purpose.

### Public Documentation Security

The public repository focuses on infrastructure architecture, technologies, and demonstrated technical capabilities.

Sensitive operational details are maintained separately.

---

## 18. Lessons Learned

Building and maintaining the HomeLAB infrastructure has provided practical experience with:

- Hardware inventory management
- Virtualization
- Capacity planning
- VLAN-based network segmentation
- Managed switching
- Firewall administration
- Power over Ethernet
- Linux administration
- Windows administration
- Security monitoring
- Backup administration
- Storage troubleshooting
- Infrastructure lifecycle management

### Key Technical Lessons

**Accurate inventories improve infrastructure management.**

Documenting hardware and virtual resources simplifies troubleshooting and planning.

**Virtualization improves resource utilization.**

Multiple workloads can share a single physical host while remaining logically separated.

**Network segmentation reduces unnecessary communication.**

Separating systems by purpose provides a foundation for access control.

**Backup infrastructure requires maintenance.**

Storage failures and outdated backup archives can affect recovery readiness.

**Hardware capacity influences future projects.**

CPU, memory, and storage availability must be considered before deploying additional services.

**Retiring unused infrastructure reduces complexity.**

Removing unnecessary systems helps simplify administration and maintenance.

---

## 19. Summary

The CyberSecurity-HomeLab combines physical networking infrastructure, Proxmox virtualization, dedicated security workloads, and centralized monitoring.

The environment includes:

- FortiGate firewall
- Ubiquiti managed switch
- Two UniFi wireless access points
- Proxmox virtualization host
- Dedicated external backup storage
- Physical gaming server
- DShield honeypot
- Windows SOC endpoint
- Wazuh SIEM

The infrastructure supports practical experience with network administration, virtualization, endpoint security, SIEM operations, detection engineering, and backup management.

Future expansion will introduce Windows Server, Active Directory, and containerized automation workloads.

This document intentionally excludes sensitive operational details to support safe publication in a public cybersecurity portfolio.
