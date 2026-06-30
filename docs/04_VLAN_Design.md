# VLAN Design

## Purpose

This document describes the VLAN architecture implemented within the CyberSecurity-HomeLab environment.

The network is segmented using IEEE 802.1Q VLANs to isolate systems based on function and security requirements. Inter-VLAN communication is controlled exclusively by the FortiGate firewall.

---

# Design Principles

The VLAN design follows several security principles:

- Least privilege
- Network segmentation
- Centralized firewall enforcement
- Reduced attack surface
- Isolation of Internet-facing services
- Separation of management traffic

---

# VLAN Allocation

| VLAN ID | Name | Subnet | Purpose |
|---------:|------|--------|---------|
| 10 | Home | 10.1.10.0/24 | Trusted wired home devices |
| 50 | Work | 10.1.50.0/24 | Dedicated work computer |
| 69 | Security | 10.1.69.0/24 | DShield Honeypot and future security services |
| Internal | Management | 192.168.1.0/24 | Infrastructure management |
| 220 | Private Wi-Fi | 10.1.220.0/24 | Trusted wireless devices |
| 221 | Guest Wi-Fi | 10.1.221.0/24 | Internet-only guest wireless |

---

# Traffic Flow

All VLAN routing is performed by the FortiGate 90D.

Traffic between VLANs is denied by default unless explicitly permitted through firewall policies.

Internet-bound traffic is NATed through the FortiGate WAN interface.

---

# Security Considerations

## Management VLAN

Only trusted administrative devices are permitted to access infrastructure services.

---

## Security VLAN

The DShield Honeypot resides on a dedicated security VLAN to isolate Internet-facing monitoring infrastructure from trusted systems.

Future security services such as a SIEM and centralized logging platform will also reside on this VLAN.

---

## Guest Wi-Fi

Guest devices are isolated from internal resources and are permitted Internet access only.

---

# Validation

The following networks were successfully validated after production cutover:

- Home VLAN
- Work VLAN
- Management VLAN
- Security VLAN
- Private Wi-Fi
- Guest Wi-Fi

---

# Lessons Learned

Network segmentation significantly simplifies policy management while reducing the risk of lateral movement between systems.
