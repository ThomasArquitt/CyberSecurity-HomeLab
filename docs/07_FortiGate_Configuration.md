# 07 — FortiGate Firewall Configuration

## 1. Overview

A Fortinet FortiGate firewall serves as the primary network security appliance for the CyberSecurity HomeLAB.

The firewall is positioned between the ISP connection and the internal network infrastructure.

Its primary responsibilities include:

- WAN connectivity
- Network segmentation
- Inter-VLAN routing
- Stateful firewall inspection
- Network Address Translation (NAT)
- Controlled inbound service publishing
- Access control between security zones
- Centralized firewall logging
- Isolation of intentionally exposed security workloads

The FortiGate provides centralized Layer 3 routing and security policy enforcement for the HomeLAB.

The environment follows a default-deny approach, with explicit firewall policies permitting required communication.

> **Security Note:** This document is a sanitized representation of the operational firewall configuration. Public IP addresses, sensitive management details, exact inbound port mappings, and selected security policy details have been intentionally omitted.

---

## 2. Firewall Architecture

### Hardware and Deployment

| Component | Configuration |
|---|---|
| Manufacturer | Fortinet |
| Platform | FortiGate |
| Role | Primary firewall and network gateway |
| WAN Connectivity | ISP fiber connection |
| Internal Connectivity | Managed Ethernet switching |
| VLAN Routing | Firewall VLAN interfaces |
| Security Monitoring | Centralized Wazuh SIEM |
| Logging Protocol | Syslog |

### Logical Network Architecture

```text
                INTERNET
                    |
                    |
               ISP Equipment
                    |
                    |
             FORTIGATE FIREWALL
                    |
                    |
             MANAGED SWITCHING
                    |
        +-----------+-----------+
        |           |           |
    HOME ZONE    WORK ZONE   SECURITY ZONE
                                |
                         +------+------+
                         |      |      |
                      Wazuh  Windows  Honeypot
                       SIEM   SOC VM

        Additional Segments:
        - Gaming
        - Private Wireless
        - Public Wireless
```

The firewall provides the routing and policy enforcement necessary to separate devices and services into distinct security zones.

The managed switching infrastructure provides Layer 2 connectivity and VLAN distribution.

---

## 3. Network Segmentation

The HomeLAB uses VLANs to separate systems according to their purpose and security requirements.

### Network Zones

| Security Zone | Purpose |
|---|---|
| Home | Personal devices and authorized administration |
| Work | Work-related devices and services |
| Security | Cybersecurity infrastructure and testing |
| Gaming | Dedicated gaming infrastructure |
| Private Wireless | Trusted wireless clients |
| Public Wireless | Less-trusted wireless clients |
| Infrastructure Management | Hypervisor and network infrastructure management |

The FortiGate maintains the Layer 3 gateway interfaces for the segmented networks.

### Segmentation Objectives

The network segmentation design is intended to:

1. Reduce unnecessary communication between device categories.
2. Isolate intentionally exposed security testing systems.
3. Separate work-related devices from personal infrastructure.
4. Restrict access to administrative services.
5. Control communication between security workloads.
6. Improve visibility into traffic crossing security boundaries.

### Security VLAN

The Security VLAN contains the primary cybersecurity workloads:

- DShield/Cowrie honeypot
- Windows 11 SOC testing endpoint
- Wazuh SIEM server

These systems are separated from personal, work, and infrastructure management networks.

Inter-VLAN communication is controlled through firewall policies rather than unrestricted routing.

---

## 4. Firewall Policy Architecture

The FortiGate uses stateful firewall policies to evaluate traffic between network interfaces.

Policies consider several attributes, including:

- Source interface
- Destination interface
- Source address
- Destination address
- Service or protocol
- Schedule
- Security action
- Logging configuration

### Policy Evaluation

FortiGate firewall policies are evaluated according to their configured sequence.

The first applicable matching policy determines whether the traffic is permitted or denied.

Traffic without a matching allow policy is subject to the firewall's implicit deny behavior.

### Policy Categories

The HomeLAB implements policies for:

| Policy Category | Purpose |
|---|---|
| Administrative access | Permit authorized management of infrastructure |
| Security infrastructure | Allow required SOC and SIEM communication |
| Internet access | Provide controlled outbound connectivity |
| Honeypot exposure | Permit selected external traffic to isolated services |
| Gaming services | Support dedicated gaming infrastructure |
| Network isolation | Restrict traffic between security zones |
| Telemetry forwarding | Support centralized security monitoring |

The public documentation intentionally omits the complete production policy table.

---

## 5. Administrative Access Controls

Administrative access is designed around a designated management workstation.

The workstation resides on a trusted internal network.

### Managed Infrastructure

The administrative workstation is used to manage:

- FortiGate firewall
- Proxmox hypervisor
- Ubiquiti managed switch
- Wazuh SIEM
- Windows SOC testing endpoint
- DShield honeypot
- Gaming server infrastructure

### Management Policy Design

Dedicated firewall policies permit required administrative protocols between the trusted workstation and specific infrastructure destinations.

Examples include:

- SSH for Linux administration
- HTTPS for secure web management
- Approved application-specific management services

The design avoids relying on unrestricted communication between all VLANs.

### Security Considerations

Firewall management-plane access requires separate consideration from ordinary forwarded traffic.

Management access can be influenced by:

- Interface administrative access settings
- Administrator account permissions
- Trusted-host restrictions
- Local-in policies
- Authentication controls

These controls should be reviewed periodically.

Exact management endpoints and administrative access configurations are excluded from the public repository.

---

## 6. Inter-VLAN Security Policies

Inter-VLAN routing is provided by the FortiGate.

However, routing availability does not automatically imply that communication is permitted.

Traffic must satisfy the applicable firewall policies.

### Authorized Communication

Selected policies permit communication required for:

- Infrastructure administration
- Security monitoring
- SIEM agent communication
- Gaming server management
- Internet connectivity
- Application-specific services

### Security Zone Isolation

The Security VLAN is subject to explicit restrictions intended to prevent security testing workloads from initiating unnecessary connections to protected internal networks.

This is especially important for the honeypot environment.

A honeypot is intentionally exposed to untrusted activity and should not have unrestricted access to sensitive systems.

### Default-Deny Design

Traffic without an applicable allow policy is denied by the firewall.

This provides an additional layer of protection when communication between network segments is not explicitly authorized.

### Stateful Traffic Inspection

The firewall maintains session state.

Permitted connections can receive associated return traffic without requiring a separate reverse-direction allow policy for every established session.

---

## 7. Internet Connectivity and NAT

The FortiGate provides outbound internet connectivity using Network Address Translation.

### Source NAT

Source NAT allows internal systems to initiate connections to external services while translating their private source addresses.

The firewall maintains the associated session information to return traffic to the correct internal device.

### Supported Activities

Outbound connectivity supports:

- Operating system updates
- Security tool downloads
- Package management
- Threat intelligence retrieval
- Security research
- Gaming services
- General network access

### Security Considerations

NAT does not replace firewall policy enforcement.

Outbound access should be evaluated according to the requirements of each network segment.

Security testing systems may require internet connectivity, but their access to internal networks should remain restricted.

---

## 8. Controlled Inbound Service Publishing

The FortiGate uses Virtual IP objects to publish selected internal services.

### Virtual IPs

A Virtual IP can translate traffic arriving at a firewall interface to a designated internal destination.

This functionality is commonly used for destination NAT and port forwarding.

### HomeLAB Use Cases

The environment uses controlled inbound service publishing for:

1. DShield/Cowrie honeypot services
2. Dedicated gaming server connectivity

### Honeypot Publishing

The honeypot receives selected unsolicited internet traffic through firewall VIPs and associated security policies.

The purpose is to observe activity such as:

- Automated scanning
- SSH connection attempts
- Credential attacks
- Web service probing
- Opportunistic exploitation attempts

The honeypot operates inside the dedicated Security VLAN.

### Gaming Server Publishing

The gaming server resides in a separate Gaming VLAN.

Application-specific inbound connectivity is permitted through a dedicated VIP and firewall policy.

This separation prevents the gaming workload from sharing the same security zone as the honeypot and SIEM infrastructure.

### Public Documentation Limitations

The following operational details are intentionally excluded:

- Public WAN IP addresses
- Exact external port mappings
- Internal VIP destination addresses
- Complete inbound firewall policies
- Application-specific exposure details

These settings remain part of the private operational configuration.

---

## 9. Centralized Security Logging

The FortiGate forwards security telemetry to the Wazuh SIEM.

### Logging Architecture

```text
             FORTIGATE FIREWALL
                     |
                     |
                SYSLOG EVENTS
                     |
                     v
                 WAZUH SIEM
                     |
                     v
              EVENT PROCESSING
                     |
                     v
             SECURITY ANALYSIS
```

### Syslog Integration

| Setting | Configuration |
|---|---|
| Logging | Enabled |
| Destination | Internal Wazuh SIEM |
| Transport | UDP |
| Format | FortiOS default |
| Facility | Local syslog facility |

The firewall forwards configured log categories to the centralized SIEM.

### Security Monitoring Benefits

Centralized firewall logging supports:

- Network traffic investigations
- Firewall event analysis
- Security incident triage
- Correlation with endpoint telemetry
- Detection engineering
- Analysis of suspicious network activity

### Wazuh Integration

FortiGate events have been observed in the Wazuh dashboard.

This integration allows firewall activity to be investigated alongside:

- Windows security events
- Sysmon telemetry
- Honeypot events
- Custom detection alerts

### Logging Limitations

UDP syslog does not guarantee message delivery.

Potential limitations include:

- Packet loss
- Network interruptions
- Receiver availability
- Incomplete event coverage
- Disabled logging on individual firewall policies

Additional log validation and tuning remain part of ongoing SIEM development.

---

## 10. Security Architecture

The FortiGate provides several complementary security capabilities.

### Network Segmentation

VLANs separate devices and workloads according to operational requirements.

### Access Control

Firewall policies govern communication between security zones.

### Stateful Inspection

Session tracking allows the firewall to distinguish established return traffic from unsolicited new connections.

### Controlled Exposure

Selected services are published using VIPs and matching firewall policies.

### Centralized Monitoring

Firewall logs are forwarded to Wazuh for analysis and correlation.

### Defense in Depth

The firewall operates alongside:

- VLAN segmentation
- Endpoint protection
- Windows Defender
- Sysmon
- Wazuh agents
- Honeypot telemetry
- Host-based security controls
- Administrative access restrictions

These controls provide complementary visibility and protection.

---

## 11. Security Configuration Review

A configuration review was performed using the FortiGate command-line interface.

### Review Scope

The review included:

- Network interfaces
- VLAN configuration
- Firewall policies
- Administrative access settings
- Virtual IP objects
- NAT configuration
- Centralized logging
- Network isolation

### Review Objectives

The review focused on identifying opportunities to:

1. Reduce unnecessary administrative service exposure.
2. Improve least-privilege access controls.
3. Restrict overly broad firewall permissions.
4. Improve security event visibility.
5. Validate isolation between network segments.
6. Review externally accessible services.
7. Maintain appropriate firewall software and security updates.

### Findings Management

Configuration improvement opportunities were identified during the review.

Detailed findings, affected interfaces, exact policy identifiers, and operational remediation procedures are maintained outside the public repository.

This separation allows the GitHub documentation to demonstrate security assessment experience without unnecessarily disclosing the live environment's weaknesses.

---

## 12. Operational Administration

The FortiGate is administered through its supported management interfaces.

Command-line administration provides a repeatable method for inspecting and troubleshooting the environment.

### Common Administrative Tasks

- Review interface status
- Inspect VLAN configuration
- Verify firewall policies
- Examine routing behavior
- Validate NAT configuration
- Review security logs
- Troubleshoot connectivity
- Confirm system health

### Example Verification Commands

Display interface configuration:

```shell
show system interface
```

Display firewall policies:

```shell
show firewall policy
```

Display Virtual IP objects:

```shell
show firewall vip
```

Display routing information:

```shell
get router info routing-table all
```

Display system status:

```shell
get system status
```

### Sensitive Output Handling

Operational command output may contain:

- Public IP addresses
- Internal infrastructure details
- Management endpoints
- Security policy information
- Authentication-related settings

Raw configuration exports should be reviewed and sanitized before publication.

---

## 13. Design Decisions

### Centralized Routing and Security

Using the FortiGate as the primary gateway provides a consistent enforcement point for inter-VLAN traffic.

### Dedicated Security Network

Security testing infrastructure is separated from personal and work devices.

This limits unnecessary communication between potentially exposed workloads and sensitive systems.

### Separate Gaming Infrastructure

Gaming workloads operate within their own network segment.

### Controlled Administrative Access

A designated administrative workstation is used to manage infrastructure resources through explicitly permitted services.

### Centralized Security Telemetry

Firewall logs are forwarded to Wazuh for correlation with endpoint and honeypot events.

### Controlled External Exposure

Only selected services are published through firewall VIPs.

### Public and Private Documentation Separation

Public documentation focuses on architecture, implementation methods, and demonstrated technical skills.

Sensitive operational details are retained separately.

---

## 14. Lessons Learned

Building and administering the FortiGate environment provided practical experience with:

- Configuring VLAN interfaces
- Implementing inter-VLAN routing
- Creating firewall policies
- Understanding policy evaluation order
- Implementing source NAT
- Configuring destination NAT
- Publishing selected internal services
- Separating administrative and security networks
- Forwarding firewall logs to a SIEM
- Reviewing firewall configurations for security improvements

### Key Technical Lessons

**Network segmentation requires policy enforcement.**

Creating VLANs separates broadcast domains, but firewall policies determine which networks can communicate.

**NAT is not a security policy.**

Address translation and traffic authorization are separate functions.

**Management-plane security requires dedicated attention.**

Administrative services should be restricted independently of ordinary network traffic.

**Honeypots require isolation.**

Systems intentionally exposed to untrusted activity should not have unrestricted access to sensitive internal resources.

**Logging requires validation.**

Configuring a syslog destination does not guarantee that every relevant event is received or processed.

**Security documentation must balance transparency and operational security.**

Technical documentation should demonstrate engineering knowledge without exposing unnecessary information about live infrastructure.

---

## 15. Planned Improvements

Future improvements include:

- Continue reviewing administrative access controls
- Evaluate firewall policies against least-privilege principles
- Improve security event logging where appropriate
- Review outbound access requirements
- Validate isolation between security zones
- Continue tuning FortiGate events in Wazuh
- Maintain appropriate firmware and security updates
- Document firewall changes associated with future Active Directory integration
- Review externally published services periodically

---

## 16. Summary

The FortiGate firewall provides the primary routing and network security foundation for the CyberSecurity HomeLAB.

The environment uses multiple VLANs to separate personal, work, security, gaming, and wireless devices.

Firewall policies enforce communication requirements between these networks, while NAT provides outbound connectivity and controlled inbound service publishing.

The Security VLAN hosts the DShield honeypot, Windows SOC endpoint, and Wazuh SIEM.

Centralized syslog forwarding allows firewall activity to be analyzed alongside endpoint and honeypot telemetry.

The resulting architecture provides hands-on experience with enterprise networking concepts, firewall administration, network segmentation, security monitoring, and defense-in-depth design.

This document intentionally omits sensitive operational details to support safe publication in a public cybersecurity portfolio.
