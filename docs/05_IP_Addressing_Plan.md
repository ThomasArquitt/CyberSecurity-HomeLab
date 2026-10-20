# 05 — IP Addressing Plan

## 1. Overview

This document describes the IPv4 addressing architecture implemented within the CyberSecurity-HomeLab environment.

The laboratory uses a structured addressing scheme to support network segmentation, infrastructure management, centralized security monitoring, and controlled communication between systems.

A FortiGate 90D firewall provides:

- Network gateway services
- Inter-VLAN routing
- DHCP services
- Network Address Translation (NAT)
- Firewall policy enforcement
- Network traffic logging

The addressing architecture separates trusted personal devices, work-related systems, cybersecurity infrastructure, gaming services, and wireless clients.

**Last Updated:** October 20, 2026

> **Security Note:** This document is a sanitized representation of the operational addressing plan. Actual IP addresses, VLAN IDs, gateway assignments, DHCP allocation ranges, administrative endpoints, operational hostnames, and firewall address objects are intentionally excluded from the public repository.

---

## 2. Addressing Architecture

The HomeLAB utilizes private IPv4 addressing across seven logical network segments.

Six segments are implemented using VLANs, while the infrastructure management network operates through an untagged FortiGate hardware switch interface.

### Network Segment Inventory

| Network Segment | Purpose | Addressing Method |
|---|---|---|
| Infrastructure Management | Firewall, switching, and virtualization management | Static/Managed Infrastructure |
| Home Network | Trusted personal devices | DHCP and designated infrastructure assignments |
| Work Network | Work-related devices | DHCP |
| Security Laboratory | Security monitoring and testing systems | Static IPv4 |
| Gaming Network | Dedicated gaming infrastructure | DHCP with reservations |
| Private Wireless | Trusted wireless clients | DHCP |
| Public Wireless | Guest and less-trusted wireless clients | DHCP |

### Logical Architecture

```text
                         INTERNET
                            |
                            |
                      ISP EQUIPMENT
                            |
                            |
                      FORTIGATE 90D
                     FIREWALL / ROUTER
                            |
             +--------------+--------------+
             |              |              |
             |              |              |
       INFRASTRUCTURE    VLAN-BASED     INTERNET
         MANAGEMENT      NETWORKS      CONNECTIVITY
                            |
          +-----------------+-----------------+
          |                 |                 |
          |                 |                 |
        HOME              WORK             SECURITY
          |                 |                 |
          |                 |          +------+------+
          |                 |          |      |      |
          |                 |       DSHIELD WINDOWS WAZUH
          |                 |       HONEYPOT SOC VM   SIEM
          |
          +-----------------------------------------+
                            |
                      OTHER VLANs
                            |
               +------------+------------+
               |            |            |
               |            |            |
             GAMING       PRIVATE      PUBLIC
                          WIRELESS     WIRELESS
```

This diagram represents the logical network organization.

It does not disclose actual network addresses, VLAN identifiers, or physical interface assignments.

---

## 3. IPv4 Addressing Standards

The laboratory uses private IPv4 addresses consistent with RFC 1918.

### Private IPv4 Addressing

Private IPv4 addressing provides internal network connectivity without requiring globally routable addresses for individual devices.

The laboratory uses private address space to organize systems according to network function.

### Subnet Design

Each of the seven network segments currently uses a `/24` IPv4 subnet.

A `/24` subnet provides:

| Property | Value |
|---|---|
| Prefix Length | /24 |
| Subnet Mask | 255.255.255.0 |
| Total IPv4 Addresses | 256 |
| Usable Host Addresses | 254 |
| Network Address | 1 |
| Broadcast Address | 1 |

### Addressing Benefits

Using consistent subnet sizes provides:

- Predictable network organization
- Simplified troubleshooting
- Consistent gateway configuration
- Straightforward DHCP administration
- Easier firewall policy management
- Room for additional laboratory devices

### Gateway Design

The FortiGate provides the default gateway for each network segment.

This centralizes routing and allows firewall policies to control communication between different networks.

Exact gateway addresses are excluded from public documentation.

---

## 4. VLAN and Subnet Allocation

The laboratory uses six VLANs and one untagged infrastructure management network.

### Network Allocation

| Network | VLAN-Based | Subnet Size | Primary Function |
|---|---|---|---|
| Infrastructure Management | No | /24 | Infrastructure Administration |
| Home | Yes | /24 | Trusted Personal Devices |
| Work | Yes | /24 | Work-Related Devices |
| Security | Yes | /24 | SOC Laboratory |
| Gaming | Yes | /24 | Gaming Infrastructure |
| Private Wireless | Yes | /24 | Trusted Wireless Devices |
| Public Wireless | Yes | /24 | Guest and Less-Trusted Devices |

### VLAN Architecture

The FortiGate maintains the VLAN interfaces used for Layer 3 routing.

The Ubiquiti managed switch transports tagged traffic between infrastructure devices.

Proxmox provides VLAN-aware virtual networking for security workloads.

### Inter-VLAN Communication

Traffic between different VLANs is routed through the FortiGate.

Firewall policies determine which connections are permitted.

The presence of a route between networks does not automatically authorize communication.

Detailed VLAN architecture is documented in:

`04_VLAN_Design.md`

---

## 5. DHCP Architecture

The FortiGate provides DHCP services for six of the seven network segments.

The Security VLAN does not currently use a FortiGate DHCP scope.

### DHCP Scope Inventory

| Network Segment | FortiGate DHCP | Address Assignment |
|---|---|---|
| Infrastructure Management | Enabled | Dynamic DHCP |
| Home | Enabled | Dynamic DHCP |
| Work | Enabled | Dynamic DHCP |
| Security | Disabled | Static IPv4 |
| Gaming | Enabled | DHCP and Reservation |
| Private Wireless | Enabled | Dynamic DHCP |
| Public Wireless | Enabled | Dynamic DHCP |

### DHCP Responsibilities

The FortiGate DHCP service provides client configuration information, including:

- IPv4 address
- Subnet mask
- Default gateway
- DNS server information
- Lease duration
- NTP service information

### DHCP Allocation Strategy

DHCP pools are configured within their respective network segments.

Infrastructure systems requiring predictable addresses use static assignments or reservations where appropriate.

Address assignments must be checked to prevent conflicts.

### DHCP Lease Duration

Five DHCP scopes were verified with explicitly configured 24-hour leases.

The Gaming network uses the FortiOS default lease duration because no explicit value was identified in the reviewed configuration.

The effective Gaming DHCP lease duration has not been independently verified.

### DHCP Time Synchronization

The reviewed DHCP configuration advertises the FortiGate's local NTP service.

Time synchronization is important for:

- Accurate security event timestamps
- Log correlation
- Authentication troubleshooting
- Incident investigation
- Consistent system operations

### Security Considerations

DHCP configuration is reviewed to ensure:

- Correct network assignment
- Appropriate gateway distribution
- Consistent DNS configuration
- Address reservation accuracy
- Avoidance of IP conflicts

---

## 6. Static IPv4 Addressing

Static IPv4 addressing is used for critical security infrastructure.

### Static Addressing Objectives

Stable addresses simplify:

- Firewall policy administration
- Wazuh agent communication
- Syslog forwarding
- SSH administration
- Security monitoring
- Service configuration
- Troubleshooting

### Security Infrastructure Inventory

| System | Operating System | Addressing Method | Purpose |
|---|---|---|---|
| DShield Honeypot | Ubuntu Server | Static IPv4 | Honeypot Operations |
| Windows SOC Endpoint | Windows 11 Pro | Static IPv4 | Endpoint Security Testing |
| Wazuh SIEM | Ubuntu Server | Static IPv4 | Centralized Security Monitoring |

All three virtual machines operate within the dedicated Security VLAN.

### Address Management

Static addresses are assigned within the appropriate network segment.

Before assigning additional addresses, existing assignments should be checked to prevent duplication.

### Security Considerations

Static addressing provides predictable connectivity but does not independently provide access control.

Network access is governed by firewall policies, authentication, and applicable endpoint security controls.

---

## 7. Wazuh SIEM Network Configuration

The Wazuh server operates as an Ubuntu Linux virtual machine.

Its IPv4 configuration is managed through Ubuntu Netplan.

### Verified Configuration

| Setting | Configuration |
|---|---|
| Operating System | Ubuntu Server |
| Address Assignment | Static IPv4 |
| Network Interface | VirtIO Virtual Network Adapter |
| Default Gateway | FortiGate Security Network Gateway |
| DNS Configuration | Public DNS Resolvers |
| Network Segment | Security VLAN |
| Configuration Method | Ubuntu Netplan |

### Netplan Configuration

Ubuntu Netplan provides declarative network configuration.

The Wazuh server uses a static IPv4 address, default route, and manually configured DNS resolvers.

### Example Sanitized Netplan Configuration

The following example uses documentation-only addressing and does not represent the operational configuration.

```yaml
network:
  version: 2
  ethernets:
    ens18:
      dhcp4: false
      addresses:
        - 192.0.2.72/24
      routes:
        - to: default
          via: 192.0.2.1
      nameservers:
        addresses:
          - 1.1.1.1
          - 8.8.8.8
```

The example network belongs to the RFC 5737 documentation address space.

### Verification Commands

```bash
ip -4 addr show
```

```bash
ip route
```

```bash
sudo cat /etc/netplan/*.yaml
```

### Validation Status

The Wazuh server's static IPv4 address, default gateway, and DNS configuration were verified during the infrastructure review.

---

## 8. Windows SOC Endpoint Network Configuration

The Windows SOC endpoint operates as a Windows 11 Pro virtual machine.

It uses a manually configured static IPv4 address.

### Verified Configuration

| Setting | Configuration |
|---|---|
| Operating System | Windows 11 Pro |
| Network Adapter | Red Hat VirtIO Ethernet Adapter |
| IPv4 Assignment | Static |
| DHCP | Disabled |
| Default Gateway | FortiGate Security Network Gateway |
| DNS Configuration | Public DNS Resolvers |
| Network Segment | Security VLAN |

### PowerShell Verification

The following commands were used to inspect the endpoint's network configuration.

#### Verify DHCP Status

```powershell
Get-NetIPInterface -AddressFamily IPv4 |
    Select-Object InterfaceAlias, Dhcp
```

#### Review IP Configuration

```powershell
Get-NetIPConfiguration
```

#### Review DNS Servers

```powershell
Get-DnsClientServerAddress -AddressFamily IPv4
```

### Validation Status

The Windows SOC endpoint's static IPv4 address, default gateway, and DNS configuration were verified through PowerShell.

### Future Active Directory Integration

The Windows SOC endpoint is planned to become a domain-joined system.

Before joining the Active Directory domain, its DNS configuration will be updated to use a DNS server capable of resolving the Active Directory domain and its service records.

The planned Windows Server domain controller is expected to provide this service.

No Active Directory DNS changes have been implemented at the time of this documentation.

---

## 9. DShield Honeypot Network Configuration

The DShield/Cowrie honeypot operates as a dedicated Ubuntu Linux virtual machine.

### Addressing Configuration

| Setting | Configuration |
|---|---|
| Operating System | Ubuntu Server |
| IPv4 Assignment | Static |
| Default Gateway | FortiGate Security Network Gateway |
| Network Segment | Security VLAN |
| DNS Configuration | Not Independently Verified |

### Network Purpose

The honeypot collects telemetry associated with unsolicited external activity.

Its network configuration supports controlled inbound connectivity through the FortiGate.

### Security Considerations

The honeypot is intentionally exposed to selected external traffic.

It remains separated from trusted infrastructure through the laboratory's network segmentation and firewall architecture.

Management connectivity is controlled separately from externally accessible honeypot services.

### Active Directory Isolation

The DShield honeypot is not planned to join the future Active Directory domain.

Joining an intentionally exposed honeypot to the domain would introduce unnecessary risk to the identity infrastructure.

### Validation Status

The honeypot's static address assignment was confirmed during the infrastructure review.

Its DNS configuration has not yet been independently verified.

---

## 10. Gaming Network Addressing

The Gaming VLAN provides network connectivity for dedicated gaming infrastructure.

A physical Windows computer hosts the Palworld dedicated server.

### Server Configuration

| Setting | Configuration |
|---|---|
| Operating System | Windows 11 Pro |
| Primary Application | Palworld Dedicated Server |
| Address Assignment | DHCP Reservation |
| DHCP Server | FortiGate |
| Network Segment | Gaming VLAN |
| Security Monitoring | Wazuh Agent |

### DHCP Reservation

The FortiGate maintains a DHCP reservation for the gaming server.

This provides a predictable IPv4 address without requiring a manually configured static address on the Windows endpoint.

### Benefits of DHCP Reservations

- Centralized address management
- Predictable server connectivity
- Reduced risk of accidental address changes
- Simplified firewall policy administration
- Easier endpoint replacement and troubleshooting

### Security Monitoring

The gaming server communicates with the Wazuh SIEM through an authorized cross-VLAN firewall policy.

The server also supports controlled external connectivity for its dedicated gaming application.

Exact destination addresses, service definitions, and external port mappings are excluded from public documentation.

---

## 11. Infrastructure Management Addressing

The infrastructure management network provides connectivity for core networking and virtualization equipment.

### Infrastructure Components

| Component | Purpose |
|---|---|
| FortiGate 90D | Firewall and Network Gateway |
| Ubiquiti Switch Lite 8 PoE | Managed Layer 2 Switching |
| Proxmox VE Host | Virtualization Platform |

### Network Configuration

The management network operates through the FortiGate hardware switch interface.

It is not implemented as a dedicated tagged VLAN.

### Administrative Workstation

A designated workstation within the trusted Home network is used to administer infrastructure systems.

Applicable FortiGate policies permit authorized management traffic.

### Management Technologies

- SSH
- HTTPS-based administration
- PowerShell
- Proxmox management tools
- FortiGate administration
- UniFi Network

### Security Considerations

Management access is controlled through applicable firewall policies, authentication, and device-level access restrictions.

Exact management addresses and administrative access configurations are excluded from this public document.

---

## 12. DNS Architecture

DNS is configured at multiple levels throughout the HomeLAB.

The FortiGate system DNS configuration differs from the DNS settings distributed to DHCP clients.

Selected security endpoints also use manually configured DNS resolvers.

### DNS Configuration Overview

| Component | DNS Configuration |
|---|---|
| FortiGate System | Configured DNS Resolvers |
| FortiGate DHCP Clients | DNS Resolvers Distributed Through DHCP |
| Wazuh SIEM | Manually Configured Public DNS |
| Windows SOC Endpoint | Manually Configured Public DNS |
| DShield Honeypot | Not Independently Verified |
| Future Active Directory Clients | Planned Internal AD DNS |

### FortiGate System DNS

The FortiGate maintains DNS resolver settings for its own operations.

These settings are separate from DNS servers distributed to clients through DHCP.

### DHCP DNS Distribution

FortiGate DHCP scopes advertise configured DNS resolvers to supported client networks.

Clients with manually configured DNS settings may use different resolvers.

### Wazuh DNS

The Wazuh server uses manually configured public DNS resolvers through Ubuntu Netplan.

### Windows SOC DNS

The Windows SOC endpoint uses manually configured public DNS resolvers.

### DShield DNS

The honeypot's DNS configuration has not yet been independently verified.

### Future Active Directory DNS

The planned Windows Server domain controller will provide internal DNS services required for Active Directory.

Domain-joined clients will use DNS servers capable of resolving the Active Directory namespace and required service records.

External DNS resolution will be supported through appropriate forwarding or recursion.

---

## 13. IP Address Assignment Methods

The HomeLAB uses both static IPv4 assignments and DHCP.

### Address Assignment Inventory

| Component | Assignment Method | Verification |
|---|---|---|
| FortiGate Gateway Interfaces | Configured Interface Addresses | FortiGate CLI |
| DShield Honeypot | Static IPv4 | Confirmed |
| Windows SOC Endpoint | Static IPv4 | PowerShell |
| Wazuh SIEM | Static IPv4 | Ubuntu Netplan |
| Gaming Server | DHCP Reservation | FortiGate CLI |
| General Network Clients | DHCP | FortiGate Configuration |
| Proxmox Host | Not Independently Established | Address Verified |
| Ubiquiti Switch | Not Independently Established | Address Verified |
| Administrative Workstation | Not Independently Established | Address Verified |

### Static Addressing

Static addressing is used where predictable network connectivity is important.

### Dynamic Addressing

DHCP is used for supported general-purpose client networks.

### Reservations

DHCP reservations are used for selected systems that require consistent addressing while retaining centralized DHCP management.

### Addressing Validation

Before assigning additional addresses, the administrator should verify that the selected address is not already in use.

---

## 14. FortiGate Address Objects

FortiGate address objects provide reusable references for firewall policies.

### Address Object Categories

The environment uses address objects representing:

- Firewall management infrastructure
- Managed switching infrastructure
- Proxmox virtualization host
- Administrative workstation
- DShield honeypot
- Windows SOC endpoint
- Wazuh SIEM
- Dedicated gaming server
- Network segments

### Address Object Benefits

Address objects simplify firewall policy administration by allowing logical infrastructure references.

Benefits include:

- Improved configuration consistency
- Simplified policy management
- Easier troubleshooting
- Reduced repetitive configuration
- Clearer policy organization

### Legacy Address Object Review

A legacy address object associated with a retired Ubuntu management workload was identified during the infrastructure review.

The object had no observed references at the time of inspection.

It is a candidate for cleanup after confirming that it is no longer required.

### Public Documentation

Actual address object names and their associated IP addresses are intentionally excluded.

---

## 15. Planned Windows Server and Active Directory

A future phase of the HomeLAB will introduce a Windows Server virtual machine running Active Directory Domain Services.

### Planned Capabilities

- Active Directory Domain Services
- Internal DNS
- Group Policy
- Domain user and computer management
- Domain joining the Windows SOC endpoint
- Windows authentication and security event generation
- Identity-related SOC testing

### Addressing Requirements

The future domain controller will require:

1. A stable static IPv4 address.
2. A defined subnet and default gateway.
3. Internal DNS configuration.
4. Appropriate firewall access from domain clients.
5. An address that does not conflict with existing infrastructure.

### Current Status

No IP address has been assigned to the future domain controller.

The final network placement and addressing will be determined during deployment.

### DNS Migration Considerations

Once Active Directory DNS is deployed:

- The Windows SOC endpoint will use internal domain DNS.
- The domain controller will host the required Active Directory DNS records.
- External DNS resolution will be provided through configured forwarding or recursion.
- Other network segments will not automatically migrate to AD DNS unless required.

### Honeypot Isolation

The internet-exposed DShield honeypot will remain outside the Active Directory domain.

---

## 16. Planned Linux Container

A future Proxmox LXC container is being considered for an AI-assisted cybersecurity job discovery application.

The application is intended to help identify SOC Analyst and Junior Security Engineer opportunities.

### Planned Capabilities

- Job board API integration
- Permitted web scraping
- AI-assisted job classification
- Automated job filtering
- Duplicate detection
- Scheduled searches
- Job notifications

### Addressing Status

| Item | Status |
|---|---|
| Deployment Platform | Proxmox LXC — Planned |
| Container Hostname | Not Assigned |
| Network Segment | Not Assigned |
| IPv4 Address | Not Assigned |
| DHCP/Static Decision | Pending |
| DNS Configuration | Pending |

The container will receive an address appropriate to its selected network and access requirements.

No dedicated virtual machine is required solely for this planned application.

---

## 17. Addressing and Security Principles

### Network Separation

Different device categories are placed on separate VLANs.

This allows the FortiGate to apply security policies based on network function.

### Predictable Infrastructure Addresses

Critical security systems use stable IP addresses.

This simplifies:

- SIEM integrations
- Firewall policy management
- SSH administration
- Log forwarding
- Monitoring
- Troubleshooting

### DHCP for General Clients

FortiGate DHCP services provide address assignment for supported client networks.

### Explicit Firewall Rules

Inter-VLAN communication is governed by firewall policies.

The presence of a route between networks does not automatically mean traffic is permitted.

### Dedicated Administrative Access

Management access is provided through the authorized administrative workstation and applicable FortiGate policies.

### Public Documentation Security

This public document intentionally excludes:

- Actual IP assignments
- VLAN identifiers
- Gateway addresses
- DHCP pool boundaries
- Administrative endpoints
- Device MAC addresses
- Firewall address object mappings
- Public WAN addresses
- Passwords and API keys

Detailed operational addressing information is maintained separately from the public repository.

---

## 18. Verification Commands

The following commands can be used to verify addressing and network configuration.

### FortiGate — DHCP

```text
show system dhcp server
```

### FortiGate — DNS

```text
show system dns
```

### FortiGate — Interfaces and VLANs

```text
show system interface
```

### Ubuntu — IPv4 Interfaces

```bash
ip -4 addr show
```

### Ubuntu — Routing

```bash
ip route
```

### Ubuntu — Netplan

```bash
sudo cat /etc/netplan/*.yaml
```

### Windows — DHCP Status

```powershell
Get-NetIPInterface -AddressFamily IPv4 |
    Select-Object InterfaceAlias, Dhcp
```

### Windows — Network Configuration

```powershell
Get-NetIPConfiguration
```

### Windows — DNS

```powershell
Get-DnsClientServerAddress -AddressFamily IPv4
```

These commands provide practical methods for validating the operational configuration without publishing sensitive command output.

---

## 19. Validation Status

The following addressing components were reviewed during the October 20, 2026 infrastructure assessment.

| Component | Status |
|---|---|
| FortiGate Internal Addressing | Verified |
| VLAN Interfaces | Verified |
| VLAN Gateway Addresses | Verified |
| Default Gateways | Verified |
| DHCP Scopes | Verified |
| DHCP-Distributed DNS | Verified |
| FortiGate System DNS | Verified |
| Wazuh Static IPv4 | Verified |
| Wazuh Gateway and DNS | Verified |
| Windows SOC Static IPv4 | Verified |
| Windows SOC Gateway and DNS | Verified |
| DShield Static IPv4 | Confirmed |
| DShield DNS | Not Independently Verified |
| Gaming Server DHCP Reservation | Verified |
| Gaming DHCP Effective Lease Duration | Not Independently Verified |
| Future Active Directory Addressing | Planned |
| Future LXC Addressing | Planned |

### Validation Limitations

The review established the configuration of the listed addressing components.

It does not independently validate every client lease, network communication path, or firewall restriction.

---

## 20. Future Improvements

Potential future improvements include:

- Documenting the effective DNS configuration of the DShield honeypot.
- Confirming the address assignment methods of the Proxmox host and Ubiquiti switch.
- Recording the effective Gaming VLAN DHCP lease duration.
- Implementing internal Active Directory DNS.
- Assigning a static IP address to the future domain controller.
- Updating the Windows SOC endpoint's DNS before joining the domain.
- Maintaining an inventory of additional infrastructure addresses.
- Reviewing unused legacy FortiGate address objects.
- Creating updated network diagrams reflecting the finalized addressing architecture.

---

## 21. Lessons Learned

Implementing and administering the IP addressing architecture has provided practical experience with:

- IPv4 addressing
- Subnetting
- DHCP administration
- Static IP configuration
- DHCP reservations
- DNS configuration
- Default gateway management
- Inter-VLAN routing
- Firewall address objects
- Linux network administration
- Windows PowerShell networking
- Infrastructure troubleshooting

### Key Technical Lessons

**Consistent addressing simplifies administration.**

Using predictable network structures reduces complexity when troubleshooting connectivity.

**Static addresses support critical infrastructure.**

Security monitoring systems benefit from stable network assignments.

**DHCP reservations provide centralized control.**

Reservations allow selected systems to maintain predictable addresses without requiring manual endpoint configuration.

**DNS is essential for Active Directory.**

Domain-joined systems must be able to resolve the Active Directory domain and its required service records.

**Network routing and network authorization are separate functions.**

A valid route does not automatically mean that a firewall policy permits communication.

**Accurate documentation improves infrastructure reliability.**

Maintaining verified addressing records supports troubleshooting, expansion, and recovery.

---

## 22. Documentation References

Related documentation includes:

- `01_Project_Overview.md`
- `02_Hardware_Inventory.md`
- `03_Network_Architecture.md`
- `04_VLAN_Design.md`
- `06_Proxmox_Configuration.md`
- `07_FortiGate_Configuration.md`
- `08_Ubiquiti_Switch_Configuration.md`
- `09_Ubuntu_Server.md`
- `10_DShield_Honeypot.md`
- `11_Backup_and_Recovery.md`

---

## 23. Summary

The CyberSecurity-HomeLab uses seven IPv4 network segments to support logical separation of infrastructure and services.

The environment includes:

- Six VLANs
- One untagged infrastructure management network
- Six FortiGate DHCP scopes
- Static IPv4 assignments for primary security systems
- DHCP reservations for selected infrastructure
- Separate DNS configurations for network and endpoint services
- Centralized routing and firewall enforcement

The addressing architecture supports network segmentation, controlled administrative access, centralized security monitoring, and future Active Directory integration.

**Document Status:** Current addressing architecture documented and validated, with remaining exceptions explicitly identified.

**Last Reviewed:** October 20, 2026
