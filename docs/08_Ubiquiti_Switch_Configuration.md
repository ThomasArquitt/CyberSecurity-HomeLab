# 08 — Ubiquiti Switch Configuration

## 1. Overview

The CyberSecurity HomeLAB uses a Ubiquiti UniFi managed switch to provide Layer 2 connectivity between the firewall, virtualization infrastructure, wireless access points, and other network devices.

The switch serves as the central Ethernet distribution point for the HomeLAB.

Its primary responsibilities include:

- Layer 2 Ethernet switching
- IEEE 802.1Q VLAN tagging
- Network segmentation support
- Power over Ethernet (PoE)
- Wireless access point connectivity
- Infrastructure connectivity
- Centralized network management
- Network traffic monitoring

The switch works alongside the FortiGate firewall, which provides inter-VLAN routing and security policy enforcement.

> **Security Note:** This document is a sanitized representation of the operational switch configuration. Exact management addresses, device identifiers, physical port assignments, and sensitive network configuration details have been intentionally omitted.

---

## 2. Hardware

The HomeLAB uses a Ubiquiti UniFi Switch Lite 8 PoE.

### Hardware Specifications

| Component | Configuration |
|---|---|
| Manufacturer | Ubiquiti |
| Model | UniFi Switch Lite 8 PoE |
| Switch Type | Managed Layer 2 |
| Ethernet Ports | 8 Gigabit Ethernet |
| PoE Support | PoE/PoE+ |
| PoE Ports | 4 |
| Switching Standard | IEEE 802.1Q |
| Management Platform | UniFi Network |
| Primary Role | Network distribution and VLAN transport |

The switch provides connectivity for multiple HomeLAB systems while supporting VLAN-aware network segmentation.

### Design Considerations

The switch was selected for its combination of:

- Managed Layer 2 functionality
- VLAN support
- PoE capabilities
- Compact form factor
- Centralized management
- Integration with UniFi wireless access points

This allows the HomeLAB to support several logically separated networks without requiring separate physical switches for each network.

---

## 3. Network Architecture

The UniFi switch connects the FortiGate firewall to internal network infrastructure.

### Logical Topology

```text
                    INTERNET
                        |
                        |
                  ISP EQUIPMENT
                        |
                        |
                 FORTIGATE FIREWALL
                        |
                        |
                 UNIFI MANAGED SWITCH
                        |
          +-------------+-------------+
          |             |             |
       PROXMOX       WIRELESS      OTHER WIRED
       HOST          ACCESS        DEVICES
                     POINTS
                        |
                  +-----+-----+
                  |           |
               PRIVATE      PUBLIC
                WI-FI       WI-FI
```

The FortiGate performs Layer 3 routing and firewall inspection.

The UniFi switch provides Layer 2 connectivity and transports VLAN-tagged traffic between connected devices.

### Separation of Responsibilities

| Component | Responsibility |
|---|---|
| FortiGate Firewall | Routing, NAT, firewall policies |
| UniFi Switch | Layer 2 switching and VLAN transport |
| Proxmox | Virtualization and VM networking |
| UniFi Access Points | Wireless network connectivity |
| Wazuh SIEM | Centralized security monitoring |

This separation simplifies administration and troubleshooting.

---

## 4. UniFi Network Management

The switch is managed through the UniFi Network application.

The application provides centralized visibility and configuration management for supported UniFi devices.

### Managed Devices

The current UniFi environment includes:

- One UniFi Switch Lite 8 PoE
- Two UniFi UAP-AC-Pro wireless access points

### Management Capabilities

UniFi Network provides visibility into:

- Device connectivity
- Switch port status
- Link speeds
- PoE configuration
- VLAN assignments
- Network traffic statistics
- Connected clients
- Device firmware status

### Operational Monitoring

The UniFi Network application is used to monitor connected devices and identify potential connectivity issues.

Examples include:

- Unexpected device disconnections
- Link negotiation problems
- Access point connectivity issues
- Port configuration changes
- Firmware update availability

---

## 5. VLAN Implementation

The HomeLAB uses IEEE 802.1Q VLAN tagging to transport multiple logical networks across shared switching infrastructure.

### VLAN Purpose

VLANs separate network traffic into distinct Layer 2 broadcast domains.

The HomeLAB uses separate network segments for:

| Network Segment | Purpose |
|---|---|
| Home | Personal devices and administration |
| Work | Work-related devices |
| Security | Security testing and monitoring |
| Gaming | Dedicated gaming infrastructure |
| Private Wireless | Trusted wireless clients |
| Public Wireless | Less-trusted wireless clients |
| Infrastructure Management | Network and virtualization management |

The FortiGate provides the gateway interfaces and enforces communication policies between these segments.

### VLAN Tagging

Tagged Ethernet frames contain an IEEE 802.1Q VLAN identifier.

This allows multiple VLANs to share the same physical Ethernet connection.

### Native VLAN

The switch uses a native VLAN for untagged traffic on selected ports.

The native network also supports infrastructure management connectivity.

### Tagged VLAN Transport

Selected switch ports permit multiple tagged VLANs.

This supports devices such as:

- The FortiGate firewall
- Proxmox virtualization host
- Wireless access points

Exact VLAN identifiers and production port assignments are omitted from the public documentation.

---

## 6. Switch Port Configuration

The switch uses different port configurations depending on the connected device.

### VLAN-Aware Uplink

The firewall-facing uplink is configured to support both native and tagged VLAN traffic.

The verified configuration includes:

| Setting | Configuration |
|---|---|
| Port Status | Active |
| Native Network | Default management network |
| Tagged VLAN Management | Allow All |
| Link Negotiation | Automatic |
| Connection Speed | Gigabit Ethernet |

This configuration allows multiple VLANs to traverse the uplink.

### Access Point Connectivity

The access point ports support:

- Native management network traffic
- Tagged wireless VLAN traffic
- PoE power delivery
- Automatic link negotiation

A verified access point port uses the following configuration:

| Setting | Configuration |
|---|---|
| Port Status | Active |
| Native Network | Default management network |
| Tagged VLAN Management | Allow All |
| PoE | Enabled |
| Link Negotiation | Automatic |

### Other Wired Devices

Additional switch ports provide connectivity to wired HomeLAB systems.

The appropriate VLAN configuration depends on the connected device and its network requirements.

### Port Configuration Principles

The switch configuration follows several principles:

1. Use tagged VLANs where multiple networks must share a connection.
2. Use an appropriate native VLAN for untagged traffic.
3. Provide PoE only where required.
4. Maintain clear separation between network segments.
5. Use centralized management to verify port settings.
6. Review unused ports and unnecessary VLAN permissions periodically.

---

## 7. Wireless Access Points

Two UniFi UAP-AC-Pro wireless access points provide wireless connectivity.

Both access points connect directly to the UniFi switch.

### Access Point Hardware

| Component | Configuration |
|---|---|
| Manufacturer | Ubiquiti |
| Model | UAP-AC-Pro |
| Quantity | 2 |
| Connectivity | Wired Ethernet |
| Power | PoE |
| Management | UniFi Network |
| Wireless Bands | 2.4 GHz and 5 GHz |

### Physical Deployment

The access points are deployed in separate physical locations.

One provides wireless coverage for the primary building.

The second extends wireless connectivity to another building on the property.

Both access points use wired backhaul connections to the managed switch.

### Wireless Networks

The wireless environment provides separate networks for:

- Private wireless clients
- Public or guest wireless clients

These networks use VLAN segmentation to separate their traffic.

### Wireless Traffic Flow

```text
               WIRELESS CLIENT
                      |
                      |
                UNIFI ACCESS POINT
                      |
                      |
                TAGGED ETHERNET
                      |
                      |
                 UNIFI SWITCH
                      |
                      |
               FORTIGATE FIREWALL
                      |
                      |
              ROUTING / FIREWALL
```

Wireless clients are assigned to the appropriate network segment based on their wireless network configuration.

The FortiGate controls routed communication between wireless and wired networks.

---

## 8. Power over Ethernet

The UniFi switch supplies power to both wireless access points.

### PoE Benefits

Power over Ethernet provides several advantages:

- Eliminates separate power adapters
- Simplifies access point installation
- Reduces cabling requirements
- Supports centralized power management
- Allows access points to be installed away from electrical outlets

### PoE Configuration

The access point ports have PoE enabled.

The UniFi Network application provides visibility into PoE configuration and power consumption.

### Operational Considerations

PoE availability depends on:

- Switch power budget
- Connected device requirements
- Cable condition
- Port configuration

PoE utilization should be reviewed when additional powered devices are introduced.

---

## 9. Link Speed and Connectivity Monitoring

The UniFi Network application reports negotiated Ethernet link speeds.

### Current Observations

During configuration verification:

- The primary switch uplink negotiated at Gigabit Ethernet speed.
- Multiple wired devices negotiated at Gigabit Ethernet speed.
- Both wireless access points negotiated at Fast Ethernet speed.

The access point ports are configured for automatic link negotiation.

### Investigation Considerations

Potential causes of reduced Ethernet link speed include:

- Cable quality
- Damaged conductors
- Improper cable termination
- Incomplete twisted-pair connectivity
- Connector issues
- Network interface negotiation problems

### Planned Verification

Future troubleshooting may include:

1. Inspecting Ethernet cable connections.
2. Testing cable continuity and wire pairs.
3. Verifying connector termination.
4. Testing with a known-good cable.
5. Reviewing negotiated link speed after corrective action.

The access points remain operational.

The link speed observation is documented as an item for future performance investigation rather than a confirmed hardware failure.

---

## 10. Network Segmentation and Security

The UniFi switch provides Layer 2 VLAN separation.

The FortiGate provides Layer 3 routing and security enforcement.

### Security Responsibilities

| Security Function | Primary Component |
|---|---|
| VLAN tagging | UniFi switch |
| Layer 2 segmentation | UniFi switch |
| Inter-VLAN routing | FortiGate |
| Firewall policy enforcement | FortiGate |
| Internet NAT | FortiGate |
| Wireless network assignment | UniFi access points |
| Centralized security monitoring | Wazuh |

### Security Design

The network is designed to prevent unnecessary communication between unrelated device categories.

Security testing workloads operate in a dedicated network segment.

Public wireless clients are separated from trusted devices.

Administrative access is controlled through firewall policies and infrastructure management settings.

### Additional Considerations

Switch configuration should be periodically reviewed for:

- Unnecessary VLAN access
- Unused active ports
- Firmware updates
- Unexpected connected devices
- Unauthorized configuration changes
- Management access restrictions

---

## 11. Operational Administration

The UniFi Network application provides the primary interface for switch administration.

### Common Administrative Tasks

- Review connected devices
- Inspect switch port status
- Verify VLAN assignments
- Configure PoE
- Monitor link speeds
- Review traffic statistics
- Identify disconnected devices
- Apply firmware updates

### Troubleshooting Workflow

When investigating a connectivity issue:

1. Verify that the device is connected to the expected switch port.
2. Confirm the port is active.
3. Check the negotiated link speed.
4. Verify the native VLAN.
5. Review tagged VLAN permissions.
6. Confirm the connected device's network configuration.
7. Verify the FortiGate gateway and firewall policies.
8. Test connectivity to the intended destination.

This workflow helps distinguish Layer 1, Layer 2, and Layer 3 problems.

---

## 12. Design Decisions

### Managed Switching

A managed switch provides VLAN support and centralized administration.

### PoE Access Points

PoE simplifies wireless deployment and reduces the need for separate electrical connections.

### VLAN-Aware Uplinks

Tagged VLAN transport allows multiple networks to share physical connections.

### Centralized Wireless Management

UniFi Network provides a consistent interface for managing the switch and access points.

### Separate Routing and Switching Responsibilities

The FortiGate handles routing and firewall enforcement while the UniFi switch handles Layer 2 connectivity.

### Wired Wireless Backhaul

Both access points use Ethernet backhaul connections.

This avoids relying on wireless mesh connectivity between the access points.

---

## 13. Lessons Learned

Deploying and managing the UniFi switching infrastructure provided practical experience with:

- Managed Layer 2 switching
- IEEE 802.1Q VLAN tagging
- Native and tagged VLAN configuration
- VLAN-aware uplinks
- Power over Ethernet
- Wireless access point deployment
- Ethernet link negotiation
- Network troubleshooting
- Centralized device management

### Key Technical Lessons

**VLAN tagging enables multiple networks to share infrastructure.**

A single physical connection can transport traffic from several logical networks.

**Switching and routing serve different purposes.**

The switch forwards Ethernet frames, while the firewall routes traffic between networks.

**PoE simplifies network deployment.**

Power and network connectivity can be delivered through the same Ethernet cable.

**Link negotiation affects performance.**

A device may remain connected while operating below its supported Ethernet speed.

**Physical connectivity matters.**

Cable quality and termination can affect network performance even when VLAN configuration is correct.

**Documentation improves troubleshooting.**

Maintaining an accurate record of network roles and configuration principles makes future infrastructure changes easier to manage.

---

## 14. Planned Improvements

Future improvements include:

- Investigate reduced access point Ethernet link speeds
- Review VLAN permissions on switch ports
- Verify unused port configuration
- Continue monitoring PoE utilization
- Maintain appropriate switch and access point firmware
- Review network management access controls
- Evaluate additional switching capacity as the HomeLAB expands
- Document network changes required for future infrastructure projects

---

## 15. Summary

The Ubiquiti UniFi Switch Lite 8 PoE provides the managed Layer 2 switching foundation for the CyberSecurity HomeLAB.

The switch connects the FortiGate firewall, Proxmox virtualization infrastructure, wireless access points, and additional wired systems.

IEEE 802.1Q VLAN tagging allows multiple logically separated networks to share the same physical switching infrastructure.

Two PoE-powered UniFi access points provide wireless connectivity across separate physical locations.

The UniFi Network application provides centralized management, port configuration, connectivity monitoring, and operational visibility.

The resulting environment provides hands-on experience with enterprise networking concepts, VLAN configuration, managed switching, wireless infrastructure, and network troubleshooting.

This document intentionally excludes sensitive operational details to support safe publication in a public cybersecurity portfolio.
