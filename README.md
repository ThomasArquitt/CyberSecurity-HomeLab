# CyberSecurity HomeLab

An enterprise-style cybersecurity home lab designed to simulate real-world infrastructure and security operations.

The environment includes enterprise firewalling, network segmentation, virtualization, Linux administration, secure Internet service exposure, and comprehensive infrastructure documentation.

---

# Project Overview

This HomeLab was built to gain practical experience with enterprise networking, cybersecurity, virtualization, and systems administration.

Rather than focusing on individual technologies, the project emphasizes designing, deploying, documenting, securing, and maintaining a complete enterprise-style infrastructure.

Current implementation includes:

- Enterprise firewall deployment
- Internal infrastructure management network
- Network segmentation
- Proxmox virtualization
- Ubuntu Server administration
- Internet-facing DShield Honeypot
- Managed Layer 2 switching
- VM snapshots and backups
- Infrastructure documentation
- Git-based configuration management

---

# Proxmox Virtualization Platform

The CyberSecurity-HomeLab is hosted on a dedicated Proxmox VE virtualization platform running enterprise-style virtual infrastructure for management and security operations.

![Proxmox Overview](screenshots/proxmox-overview.png)

---

# FortiGate Edge Firewall

The CyberSecurity-HomeLab uses a FortiGate 90D as the primary edge firewall. It provides WAN connectivity, inter-VLAN routing, stateful firewall inspection, NAT, Virtual IP (VIP) publishing for Internet-facing services, and policy enforcement across segmented networks.

![FortiGate Dashboard](screenshots/fortigate-dashboard.png)

---

# Network Segmentation

The FortiGate firewall provides inter-VLAN routing and policy enforcement for multiple isolated network segments. The environment separates management, trusted home devices, work systems, wireless networks, guest access, and security infrastructure to reduce attack surface and improve security.

![FortiGate Interfaces](screenshots/fortigate-interfaces.png)

---

# Current Architecture

> **The current high-level network architecture is shown below. Additional diagrams are available in the `/diagrams` directory.**

![High-Level Network Architecture](diagrams/network-architecture.png)

---

## Network Configuration

The HomeLAB network is segmented using IEEE 802.1Q VLANs configured on a FortiGate 90D. Each VLAN has its own Layer 3 gateway, DHCP scope, and security policies to isolate network traffic while allowing controlled inter-VLAN communication where required.

| VLAN | Purpose | Network |
|------|---------|---------|
| VLAN 10 | Home Network | 10.1.10.0/24 |
| VLAN 50 | Work Network | 10.1.50.0/24 |
| VLAN 69 | Security Lab | 10.1.69.0/24 |
| VLAN 220 | Private WiFi | 10.1.220.0/24 |
| VLAN 221 | Public WiFi | 10.1.221.0/24 |
| Management | Infrastructure Management | 192.168.1.0/24 |

---

### FortiGate VLAN Interface Configuration

The following screenshots document the Layer 3 interface configuration for each VLAN on the FortiGate firewall.

#### Home VLAN (VLAN 10)

![Home VLAN](screenshots/fortigate-home-vlan-config.png)

#### Work VLAN (VLAN 50)

![Work VLAN](screenshots/fortigate-work-vlan-config.png)

#### Security VLAN (VLAN 69)

![Security VLAN](screenshots/fortigate-security-vlan-config.png)

#### Private WiFi VLAN (VLAN 220)

![Private WiFi VLAN](screenshots/fortigate-private-wifi-vlan-config.png)

#### Public WiFi VLAN (VLAN 221)

![Public WiFi VLAN](screenshots/fortigate-public-wifi-vlan-config.png)

---

## Documentation

Detailed documentation for each major component of the HomeLAB is available below.

| Documentation | Description |
|--------------|-------------|
| [Network Architecture](diagrams/network-architecture.png) | High-level infrastructure topology |
| [VLAN Design](docs/vlans.md) | VLAN segmentation, addressing, and design rationale |
| [Proxmox Platform](docs/proxmox.md) | Virtualization platform and VM inventory |
| [Firewall Policies](docs/firewall-policies.md) | FortiGate firewall rules and security policy documentation *(In Progress)* |
| [DShield Honeypot](docs/dshield.md) | Honeypot deployment and configuration *(In Progress)* |

---

# Technology Stack

## Networking

- FortiGate 90D
- Ubiquiti Switch Lite 8 PoE
- IEEE 802.1Q VLANs
- VLAN Trunking
- NAT
- Virtual IPs (VIPs)

## Virtualization

- Proxmox VE
- Virtual Machines
- Snapshots
- VM Backups

## Operating Systems

- Ubuntu Server LTS

## Security

- DShield Honeypot
- Network Segmentation
- Least Privilege
- Stateful Firewall Inspection
- Firewall Policy Enforcement

## Administration

- SSH
- Git
- GitHub

---

# Documentation

Detailed technical documentation is available in the **/docs** directory.

| Document | Description |
|----------|-------------|
| Project Overview | Overall project goals and objectives |
| Hardware Inventory | Physical infrastructure and hardware specifications |
| Network Architecture | High-level network design |
| VLAN Design | Segmentation strategy and VLAN implementation |
| IP Addressing Plan | Network addressing and subnet design |
| Proxmox Configuration | Virtualization platform deployment |
| FortiGate Configuration | Firewall implementation and security policies |
| Ubiquiti Switch Configuration | Managed switch configuration |
| Ubuntu Server | Linux server deployment and administration |
| DShield Honeypot | Internet-facing threat monitoring |
| Backup & Recovery | Backup strategy and disaster recovery planning |

---

# Skills Demonstrated

This project demonstrates practical experience with:

- Enterprise Network Design
- VLAN Segmentation
- Firewall Administration
- Linux Administration
- Virtualization
- Secure Remote Administration
- Network Security
- Threat Monitoring
- Infrastructure Documentation
- Backup & Recovery
- Change Management
- Troubleshooting
- Git Version Control

---

# Current Project Status

✓ Production network deployed

✓ Fiber ONT connected directly to FortiGate WAN

✓ Infrastructure management network operational

✓ Network segmentation validated

✓ Home, Work, Security, Private Wi-Fi, and Public Wi-Fi networks operational

✓ Internet-facing DShield Honeypot deployed

✓ Live attack traffic observed and validated

✓ VM snapshot and backup strategy implemented

✓ Proxmox VE virtualization platform deployed

✓ Enterprise VLAN architecture documented

✓ FortiGate interface configurations documented

✓ High-level network architecture documented

✓ GitHub project documentation established

Project progress is tracked in:

- `PROJECT_STATUS.md`
- `ROADMAP.md`

---

## Future Enhancements

Planned future work includes:

- Microsoft Sentinel SIEM
- Centralized Log Collection
- Infrastructure Monitoring
- Active Directory Lab
- Windows Server Infrastructure
- Detection Engineering
- Threat Hunting Workflows
- MITRE ATT&CK Mapping
- PowerShell Automation
- Python Automation

---

# Disclaimer

This repository is intended for educational and professional portfolio purposes.

Sensitive information including credentials, public IP addresses, firewall secrets, VPN configuration, and other security-sensitive information has been removed or sanitized before publication.
