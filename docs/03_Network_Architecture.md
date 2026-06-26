# Network Architecture

## Purpose

This document describes the logical architecture of the CyberSecurity-HomeLab network.

The environment is designed using enterprise networking principles, including network segmentation, centralized firewall management, and virtualization.

---

# Design Goals

The network was designed with the following objectives:

- Separate trusted and untrusted systems
- Minimize lateral movement between network segments
- Centralize routing and firewall policy enforcement
- Isolate management traffic
- Provide a scalable platform for future expansion

---

# High-Level Architecture

Internet
↓
Breezeline Fiber
↓
Adtran 854-6 (Bridge Mode - Planned)
↓
FortiGate 90D
↓
Ubiquiti Switch Lite 8 PoE
↓
Proxmox Virtualization Host
↓
Virtual Machines

---

# Network Segments

| Network | Purpose |
|----------|----------|
| Home VLAN | Personal devices |
| Work VLAN | Work computer |
| Management VLAN | Infrastructure management |
| DShield VLAN | Honeypot isolation |

---

# Infrastructure Components

| Component | Role |
|-----------|------|
| FortiGate 90D | Routing, firewall, VLAN gateway |
| Ubiquiti Switch Lite 8 PoE | Layer 2 switching |
| Proxmox VE | Virtualization platform |
| Ubuntu Server | Management server |
| DShield Honeypot | Security monitoring |

---

# Security Design

The environment follows a segmented network design.

Traffic between VLANs is controlled by the FortiGate firewall rather than allowing unrestricted communication across the network.

This approach reduces the attack surface and provides centralized visibility and policy enforcement.

---

# Future Expansion

The network architecture is designed to support future additions including:

- SIEM
- Active Directory
- Windows management workstation
- Centralized logging
- Automation services
