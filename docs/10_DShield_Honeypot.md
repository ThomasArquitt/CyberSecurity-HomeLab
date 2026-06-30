# DShield Honeypot

## Purpose

The DShield Honeypot provides Internet-facing threat visibility by collecting and reporting unsolicited network activity observed from external hosts.

The honeypot allows the CyberSecurity-HomeLab environment to safely observe real-world attack traffic while maintaining isolation from trusted systems.

---

# Objectives

The primary objectives of the DShield Honeypot are:

- Observe real-world Internet attack activity
- Gain experience with threat monitoring
- Validate firewall exposure rules
- Study common reconnaissance techniques
- Support future threat hunting exercises

---

# Architecture

The honeypot is deployed as a virtual machine hosted on Proxmox VE.

The system is published to the Internet through carefully controlled FortiGate Virtual IP (VIP) objects.

Only the required services are exposed.

The honeypot remains isolated from trusted internal systems through network segmentation and firewall policy enforcement.

---

# Security Controls

The deployment includes multiple layers of protection:

- FortiGate firewall
- Controlled VIP exposure
- Dedicated security network
- Segmented infrastructure
- Limited inbound access
- Controlled outbound communication

These controls reduce risk while allowing the honeypot to collect useful threat intelligence.

---

# Validation

Following deployment:

- Firewall VIPs were validated.
- Internet connectivity was confirmed.
- External connection attempts were successfully observed.
- DShield telemetry confirmed inbound attack activity.

The deployment is actively receiving unsolicited Internet traffic.

---

# Lessons Learned

Deploying a publicly accessible service reinforced the importance of:

- Network segmentation
- Least privilege
- Firewall validation
- Change management
- Backup and recovery planning

Observing real attack traffic provided valuable insight into the volume and nature of automated Internet reconnaissance.

---

# Future Enhancements

Planned improvements include:

- SIEM integration
- Centralized log collection
- MITRE ATT&CK mapping
- Threat hunting workflows
- Long-term attack trend analysis
- Detection engineering exercises
