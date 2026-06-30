# IP Addressing Plan

## Purpose

This document defines the IP addressing scheme used throughout the CyberSecurity-HomeLab environment.

The addressing plan is organized by VLAN and subnet to make troubleshooting, documentation, and future expansion easier.

---

# Network Summary

| VLAN ID | Network | Gateway | Purpose |
|--------:|---------|---------|---------|
| 10 | 10.1.10.0/24 | 10.1.10.1 | Home wired devices |
| 50 | 10.1.50.0/24 | 10.1.50.1 | Work network |
| 69 | 10.1.69.0/24 | 10.1.69.1 | Security services |
|Internal| 192.168.1.0/24 | 192.168.1.1 | Management |
| 220 | 10.1.220.0/24 | 10.1.220.1 | Private Wi-Fi |
| 221 | 10.1.221.0/24 | 10.1.221.1 | Guest Wi-Fi |

---

# Static IP Assignments

| Device | IP Address | VLAN | Purpose |
|--------|------------|------|---------|
| Proxmox Host | 10.1.90.10 | 90 | Hypervisor management |
| Ubuntu Server VM | 10.1.90.20 | 90 | Management server |
| DShield Honeypot VM | 10.1.69.70 | 69 | Honeypot sensor |
| Home Host PC | 10.1.10.50 | 10 | PC Set up to access Proxmox, all VMs, Fortigate Firewall, Ubiquiti Switch |
---

# Addressing Strategy

Each VLAN uses a `/24` subnet.

The third octet of each subnet matches the VLAN ID where practical. This makes the environment easier to understand and troubleshoot.

Examples:

- VLAN 90 uses `10.1.90.0/24`
- VLAN 69 uses `10.1.69.0/24`
- VLAN 50 uses `10.1.50.0/24`

---

# Notes

Additional static assignments and DHCP ranges will be documented as the environment expands.
