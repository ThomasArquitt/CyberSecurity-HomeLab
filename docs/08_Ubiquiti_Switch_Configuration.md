# Ubiquiti Switch Configuration

## Purpose

The Ubiquiti Switch Lite 8 PoE provides Layer 2 switching for the CyberSecurity-HomeLab environment.

The switch is responsible for transporting VLAN traffic between the FortiGate firewall, Proxmox virtualization host, access points, and endpoint devices.

---

# Responsibilities

The switch provides:

- IEEE 802.1Q VLAN tagging
- Access ports
- Trunk ports
- PoE for wireless access points
- Layer 2 connectivity

---

# Network Design

The switch connects all infrastructure devices to the FortiGate firewall.

A trunk connection carries multiple VLANs between the FortiGate and the switch.

Access ports are assigned to the appropriate VLAN based on the connected device.

---

# Access Points

Two PoE-powered wireless access points are connected to the switch.

Wireless networks include:

- Private Wi-Fi
- Guest Wi-Fi

Each SSID is mapped to its appropriate VLAN to maintain network segmentation.

---

# VLAN Implementation

The switch transports VLAN traffic without performing routing.

Inter-VLAN communication is handled exclusively by the FortiGate firewall.

This design centralizes security policy enforcement and simplifies network management.

---

# Design Decisions

The Ubiquiti Switch Lite 8 PoE was selected because it provides:

- Managed Layer 2 switching
- VLAN support
- Power over Ethernet (PoE)
- Centralized management through UniFi
- Sufficient port density for future expansion

---

# Lessons Learned

Planning VLAN assignments before production deployment simplified the network cutover.

Separating switching and routing responsibilities reduced complexity and made troubleshooting more straightforward.
