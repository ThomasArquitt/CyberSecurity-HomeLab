# Proxmox Virtualization Platform

## Purpose

Proxmox VE serves as the virtualization platform for the CyberSecurity-HomeLab environment.

It hosts all virtual machines, provides snapshot and backup capabilities, and allows services to be isolated while sharing the same physical hardware.

---

# Host Hardware

| Component | Details |
|-----------|---------|
| CPU | Intel Core i7-11370H (11th Gen) |
| Memory | 32 GB RAM |
| Storage | 500 GB SSD |
| Hypervisor | Proxmox VE 9.x |

---

# Virtual Machines

| VM | Purpose | VLAN |
|----|---------|------|
| Ubuntu Server | Infrastructure management | Management VLAN |
| DShield Honeypot | Internet-facing honeypot | Security (69) |

---

# Networking

The Proxmox host is connected to the Ubiquiti Switch Lite 8 PoE using a VLAN-aware bridge.

Virtual machine network adapters are assigned VLAN tags to place each VM directly into its appropriate network segment.

The Proxmox host is managed through the internal infrastructure management network (192.168.1.0/24).

Virtual machines are assigned to their respective VLANs using VLAN-aware networking and Proxmox VM VLAN tagging.

---

# Backup Strategy

Backups are stored on an external 5 TB USB hard drive.

The backup strategy includes:

- Full VM backups
- VM snapshots prior to major changes
- Recovery testing when appropriate

---

# Recovery Strategy

Before major infrastructure changes:

- Create VM snapshots
- Perform full VM backups
- Validate backup completion
- Proceed with infrastructure changes

This process was followed before the FortiGate production cutover.

---

# Design Decisions

Proxmox VE was selected because it provides:

- Enterprise virtualization features
- Snapshot capability
- Integrated backup management
- VLAN-aware networking
- Web-based management
- No licensing cost for home lab use

---

# Lessons Learned

Creating snapshots and backups before major infrastructure changes greatly reduced deployment risk and provided a reliable rollback strategy during network migration.
