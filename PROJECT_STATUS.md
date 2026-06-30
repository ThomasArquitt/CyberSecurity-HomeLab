# Project Status

## Current Production Status

The CyberSecurity-HomeLab environment has been successfully cut over from the ISP gateway/router model to a FortiGate-managed network.

The ISP gateway was removed from the routing path, and the ONT is now connected directly to FortiGate WAN1.

---

## Completed

- FortiGate deployed as the primary firewall and router
- ONT connected directly to FortiGate WAN1
- Ubiquiti Switch Lite 8 PoE deployed for managed switching
- Proxmox VE host deployed
- Ubuntu Server VM deployed
- DShield Honeypot VM deployed
- VLAN segmentation implemented
- VM VLAN tagging configured in Proxmox
- Management VLAN validated
- Home VLAN validated
- Work VLAN validated
- Private Wi-Fi validated
- Guest Wi-Fi validated
- Security VLAN validated
- DShield Honeypot safely exposed
- FortiGate VIP traffic observed
- DShield Honeypot attack activity observed
- VM snapshots created
- VM backups stored on external 5 TB backup drive

---

## Current Focus

- Documenting network architecture
- Documenting VLAN design
- Documenting FortiGate configuration
- Documenting Proxmox configuration
- Adding sanitized screenshots
- Preparing repository for resume and job application use

---

## Planned Next Steps

- Deploy SIEM
- Configure centralized log collection
- Develop detection and threat hunting workflows
- Map observed honeypot activity to MITRE ATT&CK
- Add Bash, PowerShell, and Python automation
