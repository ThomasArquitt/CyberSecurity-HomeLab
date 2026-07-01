# Proxmox Virtualization Platform

## Overview

The HomeLAB is built on Proxmox Virtual Environment (VE), providing an enterprise-style virtualization platform for hosting management, security, and research workloads.

The virtualization platform allows isolated virtual machines to be deployed, snapshotted, backed up, and managed from a centralized interface.

## Host Specifications

| Component | Specification |
|----------|---------------|
| Hypervisor | Proxmox VE |
| CPU | Intel Core i5 |
| Memory | 32 GB DDR4 |
| Storage | 500 GB SSD |

## Virtual Machines

| VM | Purpose |
|----|---------|
| UBU-MGMT-01 | Ubuntu Server management host |
| DSHIELD-01 | Internet-facing DShield Honeypot |

## Features

- Virtual machine snapshots
- Centralized management
- VM isolation
- SSH administration
- Backup support
- Resource allocation per VM

## Networking

The Proxmox host connects to the Ubiquiti switch through the FortiGate-managed network infrastructure.

Management traffic remains isolated from production and security workloads through VLAN segmentation.

## Current Workloads

- Ubuntu Management Server
- DShield Honeypot

## Future Expansion

Planned virtual machines include:

- Wazuh
- SIEM platform
- Monitoring server
- Additional security research environments

---

## Proxmox Dashboard

![Proxmox Dashboard](../screenshots/proxmox-overview.png)
