# Project Status

**Project:** CyberSecurity-HomeLab  
**Last Updated:** October 20, 2026  
**Overall Status:** Operational — Active Development

---

## 1. Current Infrastructure Status

The CyberSecurity-HomeLab has successfully transitioned from an ISP-managed gateway/router deployment to a FortiGate-managed network architecture.

The ISP fiber ONT connects directly to the FortiGate WAN interface.

The FortiGate provides centralized routing, firewall policy enforcement, VLAN segmentation, DHCP services, and network security logging.

The environment currently includes:

- FortiGate 90D firewall
- Ubiquiti Switch Lite 8 PoE
- Two Ubiquiti wireless access points
- Six VLANs and an untagged infrastructure management network
- Proxmox VE virtualization host
- Three active cybersecurity virtual machines
- Dedicated physical gaming server
- Wazuh SIEM
- DShield/Cowrie honeypot
- Windows 11 SOC testing endpoint
- AtomicSOC adversary simulation platform
- External virtual machine backup storage

The environment is operational and actively used for cybersecurity training, security monitoring, detection engineering, and infrastructure administration.

---

## 2. Completed Infrastructure

### Network Infrastructure

- [x] Migrated from ISP gateway routing to FortiGate-managed routing
- [x] Connected fiber ONT directly to FortiGate WAN
- [x] Deployed FortiGate 90D firewall
- [x] Deployed Ubiquiti Switch Lite 8 PoE
- [x] Configured VLAN segmentation
- [x] Configured inter-VLAN routing
- [x] Configured DHCP scopes
- [x] Configured firewall policies
- [x] Configured Network Address Translation
- [x] Deployed two wireless access points
- [x] Configured separate private and public wireless networks
- [x] Established remote SSH administration
- [x] Configured FortiGate syslog forwarding to Wazuh

### Virtualization

- [x] Deployed Proxmox Virtual Environment
- [x] Configured VLAN-aware virtual networking
- [x] Deployed DShield honeypot virtual machine
- [x] Deployed Windows 11 SOC virtual machine
- [x] Deployed Wazuh SIEM virtual machine
- [x] Configured QEMU guest agents
- [x] Implemented virtual machine snapshots
- [x] Configured external backup storage
- [x] Configured automated backup scheduling
- [x] Configured backup retention policies
- [x] Created fresh backups for all active security VMs
- [x] Retired unused Ubuntu management virtual machine

### Security Monitoring

- [x] Deployed Wazuh SIEM
- [x] Installed Wazuh agents on monitored endpoints
- [x] Integrated Windows endpoint telemetry
- [x] Integrated DShield/Cowrie honeypot telemetry
- [x] Integrated FortiGate firewall logs
- [x] Integrated dedicated gaming server monitoring
- [x] Configured Sysmon on Windows SOC endpoint
- [x] Enabled PowerShell logging
- [x] Developed custom Wazuh detection rules
- [x] Performed detection rule testing and tuning
- [x] Validated active endpoint agent connectivity

### Adversary Simulation

- [x] Installed Atomic Red Team
- [x] Developed AtomicSOC web application
- [x] Implemented Python FastAPI backend
- [x] Implemented web-based test execution
- [x] Implemented randomized test selection
- [x] Executed controlled MITRE ATT&CK tests
- [x] Generated Windows endpoint security telemetry

### Dedicated Gaming Infrastructure

- [x] Deployed physical Windows gaming server
- [x] Installed Palworld Dedicated Server using SteamCMD
- [x] Configured dedicated Gaming VLAN
- [x] Configured DHCP reservation
- [x] Configured required firewall policies
- [x] Configured Windows Scheduled Task automation
- [x] Enabled application REST API
- [x] Installed and connected Wazuh agent

### Backup and Recovery

- [x] Integrated external USB backup storage with Proxmox
- [x] Configured persistent filesystem mounting
- [x] Investigated external storage disconnection
- [x] Repaired ext4 filesystem
- [x] Restored backup storage availability
- [x] Removed unnecessary historical backup archives
- [x] Preserved a recovery point for retired infrastructure
- [x] Updated automated backup coverage
- [x] Created backups for all active security VMs
- [x] Configured daily, weekly, and monthly retention

---

## 3. Documentation Status

### Infrastructure Documentation

| Document | Status |
|---|---|
| 01 — Project Overview | Complete |
| 02 — Hardware Inventory | Complete |
| 03 — Network Architecture | Complete |
| 04 — VLAN Design | Complete |
| 05 — IP Addressing Plan | Complete |
| 06 — Proxmox Configuration | Complete |
| 07 — FortiGate Configuration | Complete |
| 08 — Ubiquiti Switch Configuration | Complete |
| 09 — Ubuntu Server | Complete |
| 10 — DShield Honeypot | Complete |
| 11 — Backup and Recovery | Complete |

The infrastructure documentation has undergone a sanitization review for public GitHub publication.

Sensitive operational information has been excluded, including:

- Public IP addresses
- Internal management addresses
- Exact VLAN identifiers
- Credentials and authentication tokens
- Sensitive firewall configurations
- External service mappings
- Infrastructure-specific identifiers

### Remaining Documentation

- [ ] Dedicated Wazuh SIEM documentation
- [ ] Windows 11 SOC endpoint documentation
- [ ] AtomicSOC documentation
- [ ] Final network architecture diagrams
- [ ] Final documentation screenshots
- [ ] Repository-wide consistency review

---

## 4. In Progress

### Documentation Finalization

The current priority is completing the remaining cybersecurity workload documentation.

This includes:

- Wazuh configuration and detection engineering
- Windows SOC endpoint configuration
- AtomicSOC architecture and functionality

Screenshots and diagrams will be added after the written documentation is finalized.

### Infrastructure Validation

Additional operational validation is planned for:

- Automated backup execution
- Backup archive integrity
- Virtual machine restore procedures
- Backup notification delivery
- Administrative access hardening
- Network segmentation testing

---

## 5. Planned Infrastructure

### Windows Server and Active Directory

**Status:** Planned

Deploy a Windows Server virtual machine to introduce enterprise identity infrastructure.

Planned capabilities:

- Active Directory Domain Services
- DNS
- Group Policy
- Domain user and computer management
- Domain authentication
- Windows security auditing

The existing Windows SOC endpoint will be joined to the Active Directory domain.

This expansion will support identity-related SOC investigations and detection engineering.

### Active Directory Security Monitoring

**Status:** Planned

Expand security monitoring to include:

- Failed authentication attempts
- Password spraying
- Account enumeration
- Privileged group modifications
- Suspicious account creation
- Authentication anomalies

### AI-Powered Cybersecurity Job Discovery

**Status:** Planned

Develop a Python-based application hosted in a lightweight Proxmox LXC container.

Planned functionality includes:

- Automated cybersecurity job searches
- Job board API integration
- Permitted web scraping
- AI-assisted job classification
- Duplicate detection
- Scheduled execution
- Job notifications

### Additional Security Automation

**Status:** Planned

Potential improvements include:

- Expanded AtomicSOC functionality
- Additional MITRE ATT&CK testing
- Automated detection validation
- Security event enrichment
- Additional Wazuh detection rules
- Python and PowerShell investigation utilities

---

## 6. Known Issues and Outstanding Validation

| Item | Current Status |
|---|---|
| Proxmox Backup Scheduling | Configured; automated execution pending validation |
| VM Backup Archives | Created successfully |
| Backup Restore Testing | Pending |
| Backup Notification Delivery | Unresolved |
| External USB Storage Reliability | Monitoring |
| Windows 11 VM Guest OS Type | Configuration correction pending |
| Management Service Hardening | Additional review planned |
| Wireless Access Point Link Speeds | Further troubleshooting planned |

These items are documented to distinguish completed implementations from functionality that still requires validation or improvement.

---

## 7. Current Project Priorities

The immediate priorities are:

1. Finalize Wazuh SIEM documentation.
2. Finalize Windows SOC endpoint documentation.
3. Finalize AtomicSOC documentation.
4. Complete the repository-wide documentation review.
5. Deploy Windows Server with Active Directory Domain Services.
6. Join the Windows SOC endpoint to the domain.
7. Expand identity-related security monitoring.
8. Develop the AI-powered cybersecurity job discovery application.
9. Add final screenshots and architecture diagrams.

---

## 8. Project Roadmap

For additional planned infrastructure and development milestones, see:

`ROADMAP.md`

---

## 9. Summary

The CyberSecurity-HomeLab has progressed from a basic home network into an operational, segmented cybersecurity laboratory.

The environment currently provides hands-on experience with:

- Enterprise-style networking
- Firewall administration
- VLAN segmentation
- Proxmox virtualization
- Linux administration
- Windows endpoint security
- Security Information and Event Management
- Detection engineering
- Honeypot monitoring
- Adversary simulation
- Backup administration and recovery

The next major infrastructure milestone is deploying Windows Server and Active Directory to expand identity security monitoring and SOC investigation capabilities.

**Project Status: Operational — Active Development**
