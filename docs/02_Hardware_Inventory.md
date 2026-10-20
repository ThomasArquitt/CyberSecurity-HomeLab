# Hardware Inventory

## Purpose

This document provides a detailed inventory of the physical hardware and virtual infrastructure supporting the CyberSecurity-HomeLab project.

Maintaining an accurate hardware inventory is essential for infrastructure management, capacity planning, troubleshooting, security monitoring, and future expansion.

The inventory includes physical networking equipment, virtualization hosts, storage devices, dedicated servers, and virtual machines.

**Last Updated:** October 20, 2026

---

## Internet Service

| Component | Details |
|-----------|---------|
| Internet Service Provider | Breezeline |
| Connection Type | Fiber |
| Delivery Method | Aerial Fiber |
| Network Handoff | Optical Network Terminal (ONT) |
| Firewall Connection | FortiGate WAN1 |
| Primary Purpose | Internet connectivity |

The ISP fiber connection terminates at an Optical Network Terminal (ONT), which provides an Ethernet handoff to the FortiGate firewall.

The FortiGate serves as the primary gateway for the laboratory network.

---

## Firewall

| Component | Details |
|-----------|---------|
| Manufacturer | Fortinet |
| Model | FortiGate 90D |
| Device Type | Next-Generation Firewall |
| Role | Network Gateway and Security Enforcement |
| Primary WAN Interface | WAN1 |
| Network Segmentation | VLAN-Based |
| Routing | Inter-VLAN and WAN Routing |
| Firewall Policies | Configured |
| Network Address Translation | Configured |
| Security Logging | Syslog Forwarding to Wazuh |
| Management | Restricted Administrative Access |

### Responsibilities

The FortiGate 90D provides centralized network security and traffic management.

Primary responsibilities include:

- Internet gateway functionality
- VLAN routing
- Inter-VLAN access control
- Firewall policy enforcement
- Network Address Translation (NAT)
- DHCP services
- Network traffic logging
- Syslog forwarding to Wazuh
- Administrative access restrictions

Firewall policies are configured to control communication between network segments and restrict unnecessary access.

Security logs are forwarded to the Wazuh SIEM for centralized monitoring and investigation.

---

## Switching Infrastructure

| Component | Details |
|-----------|---------|
| Manufacturer | Ubiquiti |
| Model | Switch Lite 8 PoE |
| Device Type | Managed Network Switch |
| Ethernet Ports | 8 |
| PoE Support | Yes |
| VLAN Support | Yes |
| Role | Managed Layer 2 Switching |
| Management | Ubiquiti Network Management |

### Responsibilities

The Ubiquiti managed switch provides connectivity between the FortiGate firewall, Proxmox virtualization host, wireless access points, and additional network devices.

Primary responsibilities include:

- VLAN-aware switching
- Network segmentation
- Power over Ethernet delivery
- Physical network connectivity
- Wireless access point connectivity
- Managed network infrastructure

The switch supports multiple network segments while allowing the FortiGate to enforce security policies between VLANs.

---

## Wireless Infrastructure

| Component | Details |
|-----------|---------|
| Manufacturer | Ubiquiti |
| Wireless Access Points | 2 |
| Power Delivery | Power over Ethernet |
| Network Integration | Ubiquiti Managed Switch |
| Wireless Networks | Public and Private |
| Deployment | Multiple Physical Buildings |

### Responsibilities

The wireless infrastructure provides network connectivity across multiple physical locations.

Two Ubiquiti wireless access points provide wireless coverage for the property.

Wireless networks are logically separated to support different connectivity and security requirements.

The access points are managed through the Ubiquiti networking environment.

---

## Primary Virtualization Host

| Component | Details |
|-----------|---------|
| Platform | Mini PC |
| Processor | Intel Core i7-11370H |
| Processor Generation | 11th Gen Intel |
| Physical CPU Cores | 4 |
| Logical CPU Threads | 8 |
| Base Clock Speed | 3.30 GHz |
| Installed Memory | 32 GB RAM |
| Usable Memory | 31.08 GiB |
| Primary Storage | 500 GB SSD |
| Hypervisor | Proxmox VE 9.2 |
| Boot Mode | UEFI |
| Role | Primary Virtualization Infrastructure |

### Responsibilities

The Proxmox VE host provides the primary computing platform for the CyberSecurity-HomeLab.

It hosts virtual machines supporting:

- Security monitoring
- Endpoint telemetry collection
- Adversary simulation
- Honeypot operations
- Detection engineering
- Infrastructure administration

Virtualization allows security services and test environments to operate independently while sharing physical hardware resources.

### Proxmox Resource Utilization

Resource utilization recorded October 20, 2026:

| Resource | Value |
|----------|-------|
| Logical CPUs | 8 |
| CPU Utilization | 1.41% |
| Installed RAM | 32 GB |
| Usable RAM | 31.08 GiB |
| RAM Utilization | 68.19% |
| RAM In Use | 21.20 GiB |
| RAM Available | Approximately 9.88 GiB |
| Root Filesystem Capacity | 93.93 GiB |
| Root Filesystem Used | 7.62 GiB |
| Root Filesystem Utilization | 8.12% |
| Swap Capacity | 8 GiB |
| Swap Utilization | 0% |

These values represent a point-in-time snapshot and will fluctuate based on virtual machine activity.

The root filesystem capacity does not represent the total storage available across all Proxmox storage pools.

---

## Backup Storage

| Component | Details |
|-----------|---------|
| Manufacturer | Seagate |
| Device Type | External USB Hard Drive |
| Capacity | 5 TB |
| Proxmox Storage Name | HOMELAB_5TB |
| Primary Purpose | Virtual Machine Backups |
| Additional Purpose | Recovery and Configuration Preservation |

### Backup Strategy

The external Seagate drive provides dedicated storage for virtual machine backup and recovery operations.

The laboratory utilizes:

- Proxmox virtual machine backups
- Virtual machine snapshots
- Configuration baselines
- Recovery points before major infrastructure changes

Snapshots are used to preserve known working configurations before significant modifications.

Backups provide an additional recovery mechanism for virtual machines and laboratory infrastructure.

---

## Physical Server Infrastructure

### Palworld Dedicated Server

| Component | Details |
|-----------|---------|
| Platform | Dedicated Physical Computer |
| Operating System | Windows |
| Primary Application | Palworld Dedicated Server |
| Installation Method | SteamCMD |
| Network Segment | Gaming VLAN |
| IP Assignment | DHCP Reservation |
| Firewall | FortiGate 90D |
| Security Monitoring | Wazuh Agent |
| Application Management | Windows Scheduled Task |
| API Integration | REST API Enabled |

### Responsibilities

The dedicated Palworld server provides an additional environment for practicing Windows administration, networking, and security monitoring.

Implemented capabilities include:

- Dedicated game server deployment
- SteamCMD installation and administration
- Windows Scheduled Task automation
- DHCP reservations
- Gaming VLAN segmentation
- FortiGate firewall policy configuration
- UDP traffic management
- REST API configuration
- Wazuh endpoint monitoring

The server operates within a dedicated network segment to separate gaming services from other laboratory infrastructure.

**Note:** Physical hardware specifications will be added after verification.

---

## Virtual Machine Inventory

The Proxmox VE host currently operates three virtual machines supporting security monitoring, endpoint telemetry collection, and adversary simulation.

### Active Virtual Machines

| VM ID | Hostname | Operating System | RAM | Boot Disk | Status |
|-------|----------|------------------|-----|-----------|--------|
| 101 | DSHIELD-01 | Ubuntu Linux | 4 GB | 40 GB | Running |
| 102 | WIN11-SOC-01 | Windows 11 | 8 GB | 80 GB | Running |
| 103 | WAZUH-01 | Ubuntu Server | 8 GB | 80 GB | Running |

### Virtualization Resource Allocation

| Resource | Total |
|----------|-------|
| Active Virtual Machines | 3 |
| Configured VM Memory | 20 GB |
| Provisioned Boot Disk Capacity | 200 GB |
| LXC Containers | 0 |
| Available Logical Host CPUs | 8 |

The three virtual machines have a combined configured memory allocation of 20 GB.

Provisioned disk capacity represents configured virtual disk sizes rather than actual physical storage consumption.

---

## Virtual Machine Details

### VM 101 — DSHIELD-01

| Component | Details |
|-----------|---------|
| VM ID | 101 |
| Hostname | DSHIELD-01 |
| Operating System | Ubuntu Linux |
| Memory | 4 GB |
| Boot Disk | 40 GB |
| Primary Application | DShield/Cowrie Honeypot |
| Security Integration | Wazuh SIEM |

#### Responsibilities

DSHIELD-01 operates as a dedicated Ubuntu virtual machine hosting the DShield/Cowrie SSH honeypot.

The honeypot collects SSH-related attack activity, including:

- Unauthorized connection attempts
- Credential guessing
- Authentication failures
- Attacker command execution
- Automated attack behavior
- Suspicious network activity

Collected telemetry is integrated into Wazuh for centralized security monitoring and custom detection development.

The honeypot provides real-world attack telemetry for SOC investigation and detection engineering exercises.

---

### VM 102 — WIN11-SOC-01

| Component | Details |
|-----------|---------|
| VM ID | 102 |
| Hostname | WIN11-SOC-01 |
| Operating System | Windows 11 |
| Memory | 8 GB |
| Boot Disk | 80 GB |
| Primary Role | SOC Endpoint Testing |
| Security Monitoring | Wazuh Agent |
| Security Testing | Atomic Red Team / AtomicSOC |

#### Installed Security Components

- Sysmon
- Windows Defender
- PowerShell Logging
- Wazuh Agent
- OpenSSH Server
- Atomic Red Team
- AtomicSOC

#### Responsibilities

WIN11-SOC-01 provides a dedicated endpoint for security testing, telemetry generation, and investigation.

Primary activities include:

- Windows security event collection
- Process execution monitoring
- PowerShell activity logging
- Endpoint security monitoring
- Controlled adversary simulation
- MITRE ATT&CK technique testing
- Detection validation

The endpoint also hosts AtomicSOC, a custom Python and FastAPI application used to execute selected Atomic Red Team tests.

The Windows 11 endpoint is planned for integration into an Active Directory domain.

---

### VM 103 — WAZUH-01

| Component | Details |
|-----------|---------|
| VM ID | 103 |
| Hostname | WAZUH-01 |
| Operating System | Ubuntu Server |
| Memory | 8 GB |
| Boot Disk | 80 GB |
| Primary Application | Wazuh SIEM |
| Role | Centralized Security Monitoring |

#### Responsibilities

WAZUH-01 hosts the Wazuh security monitoring platform.

The system collects security telemetry from:

- Windows 11 SOC endpoint
- FortiGate firewall
- DShield/Cowrie honeypot

Primary responsibilities include:

- Centralized security event collection
- Log analysis
- Custom detection rule processing
- Security alert generation
- Alert investigation
- Detection engineering
- Security event correlation

Custom detection rules have been developed and tuned to improve alert quality and reduce unnecessary alert noise.

---

## Retired Virtual Machines

| VM ID | Hostname | Retirement Date | Reason |
|-------|----------|-----------------|--------|
| 100 | UBU-MGMT-01 | October 20, 2026 | Unused Ubuntu management VM removed |

### Retirement Notes

UBU-MGMT-01 was previously configured as a general-purpose Ubuntu management virtual machine.

The virtual machine was no longer required and was removed from Proxmox.

The removal simplified the virtual infrastructure and reclaimed its provisioned storage resources.

---

## Planned Virtual Infrastructure

### Windows Server — Active Directory Domain Controller

A Windows Server virtual machine is planned for deployment on Proxmox.

The server will introduce centralized identity management and authentication services into the laboratory.

#### Planned Roles

- Active Directory Domain Services (AD DS)
- DNS Server
- Group Policy Management
- Domain Authentication
- Security Auditing

#### Planned Integration

The existing WIN11-SOC-01 endpoint will be joined to the Active Directory domain.

This expansion will support:

- Domain authentication monitoring
- Windows security event collection
- Group Policy administration
- Account and group management
- Identity-based attack simulations
- Active Directory detection engineering

The Windows Server VM will be allocated resources based on available Proxmox capacity.

---

### AI-Powered SOC Job Discovery Container

A lightweight Linux container is planned for hosting an automated cybersecurity job discovery application.

The application will be developed using Python and AI-assisted analysis.

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

## Future Hardware Considerations

Potential infrastructure improvements include:

- Additional virtualization resources
- Dedicated network-attached storage (NAS)
- Expanded backup storage
- Additional Windows endpoints
- Additional security monitoring systems
- Increased memory capacity
- Additional laboratory networking equipment

Future expansion will be evaluated based on resource utilization, storage requirements, and project objectives.

---

## Notes

This document provides an inventory of the physical and virtual infrastructure supporting the CyberSecurity-HomeLab.

Virtual machine CPU allocations, network interfaces, IP addresses, and VLAN assignments are documented separately within the Proxmox and network architecture documentation.

Hardware specifications will be updated as additional information is verified.

The inventory will continue evolving as new infrastructure components and security capabilities are introduced.
