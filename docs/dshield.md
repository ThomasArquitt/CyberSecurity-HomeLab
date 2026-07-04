# DShield Internet Storm Center Honeypot

## Overview

As part of my CyberSecurity HomeLAB, I deployed an Internet-facing DShield Internet Storm Center (ISC) Honeypot to gain hands-on experience observing real-world attack activity and to contribute threat intelligence to the SANS Internet Storm Center.

The honeypot provides practical experience with network monitoring, attack reconnaissance, firewall configuration, and secure infrastructure management in a controlled environment.

---

## Objectives

The primary goals of this project were to:

- Deploy a production-grade Internet-facing honeypot
- Observe real-world malicious network activity
- Learn common reconnaissance techniques used by threat actors
- Gain experience securing Internet-exposed systems
- Contribute telemetry to the SANS Internet Storm Center
- Expand practical cybersecurity knowledge beyond traditional lab environments

---

## Environment

| Component | Description |
|-----------|-------------|
| Hypervisor | Proxmox VE |
| Guest OS | Ubuntu Server |
| Honeypot | DShield ISC Sensor |
| Firewall | FortiGate 90D |
| Network | Dedicated Security VLAN |
| Management | Ubuntu Management Server |

---

## Network Architecture

The honeypot is deployed on a dedicated security network isolated from the Home, Work, and Management VLANs.

Traffic is controlled through the FortiGate firewall to ensure the honeypot remains isolated while still allowing telemetry collection and secure administrative access.

---

## Deployment Highlights

The deployment included:

- Installing Proxmox VE
- Creating an Ubuntu Server virtual machine
- Installing the DShield ISC sensor
- Configuring Internet connectivity
- Validating firewall policies
- Verifying telemetry submission
- Monitoring attack activity

---

## Observed Activity

After deployment, the honeypot immediately began receiving unsolicited Internet traffic including:

- SSH login attempts
- Automated credential attacks
- Port scanning
- Network reconnaissance
- Bot activity
- Internet-wide vulnerability scanning

These events demonstrate the constant level of automated scanning performed against publicly accessible systems.

---

## Skills Demonstrated

This project provided hands-on experience with:

- Network Segmentation
- VLAN Design
- Firewall Administration
- Network Monitoring
- Linux Administration
- Virtualization
- Threat Intelligence
- Secure System Deployment
- Infrastructure Documentation
- GitHub Documentation

---

## Lessons Learned

Deploying the DShield honeypot reinforced several important cybersecurity concepts:

- Internet-facing systems receive continuous automated attack traffic.
- Proper network segmentation is essential to protect trusted systems.
- Firewalls should follow a least-privilege security model.
- Infrastructure should be documented throughout deployment.
- Practical projects provide valuable experience that complements formal training and certifications.

---

## Screenshots

---

### Sample Observations


---

## References

- https://www.dshield.org
- https://isc.sans.edu
- https://docs.dshield.org
