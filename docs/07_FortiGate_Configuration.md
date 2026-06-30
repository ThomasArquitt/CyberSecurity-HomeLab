# FortiGate Firewall Configuration

## Purpose

The FortiGate 90D serves as the primary security appliance for the CyberSecurity-HomeLab environment.

It is responsible for routing, firewall policy enforcement, network segmentation, NAT, and secure publication of Internet-facing services.

---

# Responsibilities

The FortiGate provides:

- Inter-VLAN routing
- Stateful firewall inspection
- Network Address Translation (NAT)
- Virtual IP (VIP) publishing
- Security policy enforcement
- Internet gateway services

---

# Network Architecture

The FortiGate sits between the Internet connection and the internal network.

The ISP ONT is connected directly to the FortiGate WAN1 interface, making the FortiGate the primary edge firewall for the environment.

---

# Security Philosophy

The firewall follows a default-deny approach.

Traffic between network segments is permitted only through explicitly defined firewall policies.

Internet-facing services are published only when required and only after validation.

---

# Internet Exposure

Internet-facing services are published using Virtual IP (VIP) objects.

Only the required ports are exposed.

Firewall policies restrict inbound traffic to only the services intended for external access.

---

# DShield Honeypot

The DShield Honeypot is published through controlled FortiGate VIPs.

Firewall logs are used to validate inbound connection attempts.

Observed traffic is correlated with activity recorded by the DShield Honeypot.

---

# Design Decisions

The FortiGate was selected because it provides enterprise firewall capabilities including:

- VLAN routing
- Stateful firewall inspection
- Policy-based access control
- VPN support
- NAT
- Logging and monitoring

Using the FortiGate as the primary gateway provides centralized control over all network traffic.

---

# Lessons Learned

Performing a staged migration greatly reduced deployment risk.

Firewall policies and VIPs were validated prior to exposing Internet-facing services.

Snapshots and VM backups were completed before the production cutover, allowing infrastructure changes to be made with confidence.
