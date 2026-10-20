# 05 — IP Addressing Plan

## 1. Overview

This document defines the IPv4 addressing scheme, VLAN subnets, default gateways, DHCP scopes, DNS configurations, and infrastructure IP assignments used throughout the CyberSecurity HomeLAB.

The environment uses a FortiGate 90D firewall for network segmentation, inter-VLAN routing, DHCP services, and internet connectivity.

The addressing design separates home devices, work devices, cybersecurity infrastructure, gaming services, and wireless clients into dedicated network segments.

### Primary Infrastructure

| Component | Hostname / Device | IP Address |
|---|---|---|
| Firewall | FortiGate 90D | 192.168.1.1 |
| Managed Switch | Ubiquiti Switch Lite 8 PoE | 192.168.1.2 |
| Hypervisor | Proxmox VE — pve | 192.168.1.10 |
| Administrative Workstation | TIC-PC | 10.1.10.50 |
| Honeypot | DSHIELD-01 | 10.1.69.70 |
| SOC Endpoint | WIN11-SOC-01 | 10.1.69.71 |
| SIEM Server | WAZUH-01 | 10.1.69.72 |
| Gaming Server | GAME-SERVER-01 | 10.1.90.100 |

**Verification:** Infrastructure addresses were established through FortiGate configuration, Proxmox management information, endpoint configuration, and Wazuh agent inventory.

---

## 2. Network Addressing Architecture

The HomeLAB uses the following network structure:

```text
Internet
   |
Breezeline Fiber ONT
   |
FortiGate 90D
   |
   +-- Internal Management Network
   |      192.168.1.0/24
   |
   +-- VLAN 10  | Home
   |      10.1.10.0/24
   |
   +-- VLAN 50  | Work
   |      10.1.50.0/24
   |
   +-- VLAN 69  | Security
   |      10.1.69.0/24
   |
   +-- VLAN 90  | Gaming
   |      10.1.90.0/24
   |
   +-- VLAN 220 | Private Wi-Fi
   |      10.1.220.0/24
   |
   +-- VLAN 221 | Public Wi-Fi
          10.1.221.0/24
```

All six VLAN interfaces are configured on the FortiGate with `internal` as their parent interface.

The internal management network uses the FortiGate's hardware-switch interface and is not itself a tagged VLAN.

Inter-VLAN communication is governed by FortiGate firewall policies.

---

## 3. VLAN and Subnet Allocation

### Verified VLAN Configuration

| VLAN ID | FortiGate Interface | Network | Subnet Mask | Default Gateway | Purpose |
|---|---|---|---|---|---|
| N/A | internal | 192.168.1.0/24 | 255.255.255.0 | 192.168.1.1 | Infrastructure management |
| 10 | home_vlan | 10.1.10.0/24 | 255.255.255.0 | 10.1.10.1 | Home devices and administration |
| 50 | work_vlan | 10.1.50.0/24 | 255.255.255.0 | 10.1.50.1 | Work devices |
| 69 | security_vlan | 10.1.69.0/24 | 255.255.255.0 | 10.1.69.1 | Cybersecurity VMs and services |
| 90 | gaming_vlan | 10.1.90.0/24 | 255.255.255.0 | 10.1.90.1 | Gaming infrastructure |
| 220 | priv_wifi | 10.1.220.0/24 | 255.255.255.0 | 10.1.220.1 | Private wireless clients |
| 221 | public_wifi | 10.1.221.0/24 | 255.255.255.0 | 10.1.221.1 | Public wireless clients |

### Addressing Standards

Each network uses a `/24` subnet.

A `/24` network provides:

- 256 total IPv4 addresses.
- 254 usable host addresses.
- One network address.
- One broadcast address.

The FortiGate uses the `.1` address as the default gateway on every network.

Where DHCP is configured, the current allocation range is generally `.100` through `.199`.

Addresses outside the DHCP pools may be used for infrastructure or manually assigned devices, subject to avoiding conflicts.

**Important:** These are descriptions of the current addressing design, not automatic reservations of every address outside a DHCP pool.

---

## 4. FortiGate DHCP Configuration

The FortiGate 90D provides DHCP services for six network segments.

The Security VLAN does not have a DHCP server configured on the FortiGate.

### Verified DHCP Scopes

| Network | DHCP Server ID | DHCP Start | DHCP End | Gateway | Lease Time |
|---|---|---|---|---|---|
| Internal Management | 1 | 192.168.1.100 | 192.168.1.199 | 192.168.1.1 | 86,400 seconds |
| Home — VLAN 10 | 6 | 10.1.10.100 | 10.1.10.199 | 10.1.10.1 | 86,400 seconds |
| Work — VLAN 50 | 4 | 10.1.50.100 | 10.1.50.199 | 10.1.50.1 | 86,400 seconds |
| Security — VLAN 69 | N/A | Not configured | Not configured | 10.1.69.1 | N/A |
| Gaming — VLAN 90 | 7 | 10.1.90.100 | 10.1.90.199 | 10.1.90.1 | Default / not explicitly set |
| Private Wi-Fi — VLAN 220 | 2 | 10.1.220.100 | 10.1.220.199 | 10.1.220.1 | 86,400 seconds |
| Public Wi-Fi — VLAN 221 | 3 | 10.1.221.100 | 10.1.221.199 | 10.1.221.1 | 86,400 seconds |

### DHCP Lease Duration

Five DHCP scopes explicitly specify:

```text
lease-time 86400
```

This corresponds to a 24-hour lease.

The Gaming VLAN does not explicitly define a lease duration in the reviewed configuration.

Its effective lease duration is determined by the FortiOS default and has not been independently verified.

### DHCP Services

The FortiGate configuration also specifies:

```text
ntp-service local
```

for each configured DHCP scope.

The FortiGate therefore advertises its local NTP service through DHCP.

The reviewed DHCP configuration uses the default timezone option.

---

## 5. Security VLAN Addressing

### VLAN 69 — Cybersecurity Infrastructure

**Network:** `10.1.69.0/24`

**Gateway:** `10.1.69.1`

**FortiGate Interface:** `security_vlan`

**DHCP Server:** None configured on the FortiGate

This network hosts the primary cybersecurity monitoring and testing infrastructure.

### Security VM Inventory

| Hostname | IPv4 Address | Assignment Method | Operating System | Function |
|---|---|---|---|---|
| DSHIELD-01 | 10.1.69.70 | Static — user confirmed | Ubuntu 26.04 LTS | DShield/Cowrie honeypot |
| WIN11-SOC-01 | 10.1.69.71 | Static — PowerShell verified | Windows 11 Pro | SOC testing endpoint |
| WAZUH-01 | 10.1.69.72 | Static — Netplan verified | Ubuntu Server | Wazuh SIEM |

### Security VLAN Address Allocation

```text
10.1.69.0       Network address
10.1.69.1       FortiGate gateway

10.1.69.70      DSHIELD-01
10.1.69.71      WIN11-SOC-01
10.1.69.72      WAZUH-01

10.1.69.255     Broadcast address
```

Additional security infrastructure may be assigned addresses within this subnet as the lab expands.

Future addresses must be checked against existing assignments before deployment.

### Security VLAN Design Considerations

The Security VLAN is isolated from other internal networks through explicit FortiGate firewall policies.

Administrative access is provided through specific policies originating from the authorized home administrative workstation.

The FortiGate also contains policies supporting selected inbound honeypot traffic and gaming-server telemetry.

The existence of these policies does not imply unrestricted communication between VLANs.

---

## 6. WAZUH-01 — Verified Static Configuration

The Wazuh server uses a statically configured IPv4 address through Ubuntu Netplan.

### Network Configuration

| Setting | Value |
|---|---|
| Hostname | WAZUH-01 |
| Interface | ens18 |
| Address | 10.1.69.72/24 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 10.1.69.1 |
| Primary DNS | 1.1.1.1 |
| Secondary DNS | 8.8.8.8 |
| Assignment | Static |
| VLAN | 69 |

### Verified Netplan Configuration

The relevant configuration is:

```yaml
network:
  ethernets:
    ens18:
      addresses:
        - 10.1.69.72/24
      nameservers:
        addresses:
          - 1.1.1.1
          - 8.8.8.8
        search: []
      routes:
        - to: default
          via: 10.1.69.1
      set-name: ens18
  version: 2
```

The live configuration also contains a MAC-address match for the interface, omitted here because it is unnecessary for the public addressing plan.

### Verification Commands

```bash
ip -4 addr show
ip route
sudo cat /etc/netplan/*.yaml
```

The routing table confirms:

```text
default via 10.1.69.1 dev ens18 proto static
```

**Status:** Static IP, gateway, and DNS configuration verified.

---

## 7. WIN11-SOC-01 — Verified Static Configuration

The Windows 11 SOC endpoint uses a manually configured IPv4 address.

### Network Configuration

| Setting | Value |
|---|---|
| Hostname | WIN11-SOC-01 |
| Operating System | Windows 11 Pro |
| Network Adapter | Red Hat VirtIO Ethernet Adapter |
| Interface | Ethernet |
| IPv4 Address | 10.1.69.71 |
| Subnet | 10.1.69.0/24 |
| Default Gateway | 10.1.69.1 |
| Primary DNS | 1.1.1.1 |
| Secondary DNS | 8.8.8.8 |
| DHCP | Disabled |
| VLAN | 69 |

### Verification Commands

Executed through PowerShell:

```powershell
Get-NetIPInterface -AddressFamily IPv4 |
Select-Object InterfaceAlias, Dhcp
```

Result:

```text
InterfaceAlias                  Dhcp
--------------                  ----
Ethernet                    Disabled
Loopback Pseudo-Interface 1 Disabled
```

Additional commands:

```powershell
Get-NetIPConfiguration
Get-DnsClientServerAddress -AddressFamily IPv4
```

These confirmed the IPv4 address, gateway, and DNS servers.

**Status:** Static IP, gateway, and DNS configuration verified.

### Future Active Directory Integration

WIN11-SOC-01 is planned to become a domain-joined endpoint.

Before joining the Active Directory domain, its DNS configuration will need to be updated to use a DNS server capable of resolving the Active Directory domain and its service records.

The planned Windows Server domain controller is expected to provide this service.

The domain controller should be configured to resolve external names through appropriate DNS forwarding or recursion.

No Active Directory DNS changes have been implemented at the time of this documentation.

---

## 8. DSHIELD-01 — Static Configuration

### Network Configuration

| Setting | Value |
|---|---|
| Hostname | DSHIELD-01 |
| IPv4 Address | 10.1.69.70 |
| Subnet | 10.1.69.0/24 |
| Default Gateway | 10.1.69.1 |
| Assignment | Static — user confirmed |
| DNS | Not independently verified |
| VLAN | 69 |

DSHIELD-01 hosts the DShield/Cowrie honeypot environment.

The system receives security-related traffic through FortiGate policies and associated inbound publishing configuration.

Management access is controlled separately from externally exposed honeypot services.

### Security Considerations

The honeypot is intentionally exposed to selected external traffic.

It must remain isolated from sensitive internal infrastructure.

**The honeypot is not planned to join the future Active Directory domain.**

Joining an internet-exposed honeypot to the lab domain would introduce unnecessary risk to the domain environment.

---

## 9. Gaming VLAN Addressing

### VLAN 90 — Gaming Infrastructure

**Network:** `10.1.90.0/24`

**Gateway:** `10.1.90.1`

**DHCP Range:** `10.1.90.100–10.1.90.199`

### GAME-SERVER-01

| Setting | Value |
|---|---|
| Hostname | GAME-SERVER-01 |
| Operating System | Windows 11 Pro |
| IPv4 Address | 10.1.90.100 |
| Default Gateway | 10.1.90.1 |
| VLAN | 90 |
| IP Assignment | DHCP reservation |
| FortiGate DHCP Server ID | 7 |
| Primary Function | Palworld dedicated server |

The FortiGate DHCP configuration contains a reserved-address entry with:

```text
set ip 10.1.90.100
set description "GAME-SERVER-01"
```

The reservation is associated with the server's network adapter MAC address.

The MAC address is intentionally omitted from this public document.

### Gaming Server Security Integration

GAME-SERVER-01 has a Wazuh agent installed.

The FortiGate includes a policy permitting the gaming server to communicate with WAZUH-01 using the configured Wazuh TCP service.

The gaming server also has a specific inbound policy associated with its Palworld UDP service.

---

## 10. Infrastructure Management Network

### Internal Network

**Network:** `192.168.1.0/24`

**Gateway:** `192.168.1.1`

**FortiGate Interface:** `internal`

This network is used for infrastructure management.

### Infrastructure Inventory

| Device | IP Address | Purpose |
|---|---|---|
| FortiGate 90D | 192.168.1.1 | Firewall and network gateway |
| Ubiquiti Switch Lite 8 PoE | 192.168.1.2 | Managed network switching |
| Proxmox VE — pve | 192.168.1.10 | Virtualization host |

### DHCP Scope

```text
192.168.1.100 - 192.168.1.199
```

The current infrastructure addresses are outside this DHCP allocation range.

### Administrative Access

The primary administrative workstation is:

```text
TIC-PC
10.1.10.50
```

It resides on Home VLAN 10.

FortiGate firewall policies permit selected management access from this workstation to infrastructure systems and security VMs.

Administrative access includes SSH and web-based management services where specifically permitted.

Access to the FortiGate's own management interfaces is also subject to interface administrative-access and local-in controls, which are distinct from ordinary forwarded firewall policies.

---

## 11. DNS Configuration

DNS is configured at multiple levels throughout the HomeLAB.

The FortiGate system DNS settings differ from the DNS servers distributed to DHCP clients.

### 11.1 FortiGate System DNS

Verified using:

```text
show system dns
```

| Priority | DNS Server |
|---|---|
| Primary | 208.91.112.53 |
| Secondary | 208.91.112.52 |

These are the DNS resolvers configured for the FortiGate itself.

### 11.2 DNS Distributed Through DHCP

All six configured FortiGate DHCP scopes specify:

| Priority | DNS Server | Provider |
|---|---|---|
| Primary | 208.67.222.222 | OpenDNS |
| Secondary | 208.67.220.220 | OpenDNS |

These addresses are advertised to clients through DHCP.

Individual devices may use different DNS servers if their operating systems have manually configured DNS settings.

### 11.3 WAZUH-01 DNS

| Priority | DNS Server | Provider |
|---|---|---|
| Primary | 1.1.1.1 | Cloudflare |
| Secondary | 8.8.8.8 | Google |

Configured directly through Ubuntu Netplan.

### 11.4 WIN11-SOC-01 DNS

| Priority | DNS Server | Provider |
|---|---|---|
| Primary | 1.1.1.1 | Cloudflare |
| Secondary | 8.8.8.8 | Google |

Configured directly in Windows.

### 11.5 DSHIELD-01 DNS

The DNS configuration has not yet been independently verified.

Its static IP assignment and address are user-confirmed.

### DNS Summary

| Device / Network | DNS Configuration |
|---|---|
| FortiGate system | 208.91.112.53 / 208.91.112.52 |
| FortiGate DHCP clients | 208.67.222.222 / 208.67.220.220 |
| WAZUH-01 | 1.1.1.1 / 8.8.8.8 |
| WIN11-SOC-01 | 1.1.1.1 / 8.8.8.8 |
| DSHIELD-01 | Not yet verified |
| Future AD domain clients | Planned internal AD DNS |

---

## 12. IP Address Assignment Methods

The HomeLAB currently uses both static IPv4 assignments and DHCP.

### Verified or Confirmed Assignments

| Device | IPv4 Address | Assignment Method | Verification |
|---|---|---|---|
| FortiGate internal | 192.168.1.1 | Configured interface address | FortiGate CLI |
| FortiGate VLAN gateways | Various `.1` addresses | Configured interface addresses | FortiGate CLI |
| DSHIELD-01 | 10.1.69.70 | Static | User confirmed |
| WIN11-SOC-01 | 10.1.69.71 | Static | PowerShell |
| WAZUH-01 | 10.1.69.72 | Static | Ubuntu Netplan |
| GAME-SERVER-01 | 10.1.90.100 | DHCP reservation | FortiGate CLI |

The assignment methods for the Ubiquiti switch, Proxmox host, and TIC-PC have not been independently established in this document.

Their addresses are documented, but no unsupported assumption is made about whether they use manually configured addresses or DHCP reservations.

---

## 13. FortiGate Address Objects

The FortiGate contains address objects corresponding to major HomeLAB devices.

### Host Address Objects

| Address Object | IPv4 Address |
|---|---|
| Fortigate_mgmt | 192.168.1.1/32 |
| Ubiquity_Switch | 192.168.1.2/32 |
| PVE_Host | 192.168.1.10/32 |
| Tic_PC | 10.1.10.50/32 |
| DSHIELD_Honeypot | 10.1.69.70/32 |
| WIN11-SOC-01 | 10.1.69.71/32 |
| WAZUH-01 | 10.1.69.72/32 |
| GAME-SERVER-01 | 10.1.90.100/32 |

These objects are used to reference devices in firewall policies.

The object names reflect the actual FortiGate naming conventions, including their existing capitalization and spelling.

### Network Address Objects

| Address Object | Network |
|---|---|
| internal_lan | 192.168.1.0/24 |
| home_lan | 10.1.10.0/24 |
| work_lan | 10.1.50.0/24 |
| security_lan | 10.1.69.0/24 |
| gaming_lan | 10.1.90.0/24 |
| priv_wifi | 10.1.220.0/24 |
| public_wifi | 10.1.221.0/24 |

### Legacy Address Object

The FortiGate also contains:

```text
Ubuntu_Server_VM
192.168.1.20/32
```

This object had zero references when previously reviewed.

It may be associated with retired infrastructure.

Its original purpose has not been conclusively verified, so it should be reviewed before removal.

---

## 14. Planned Windows Server and Active Directory

A future phase of the HomeLAB will introduce a Windows Server virtual machine running Active Directory Domain Services.

### Planned Capabilities

- Active Directory Domain Services (AD DS)
- Internal DNS
- Group Policy
- Domain user and computer management
- Domain joining WIN11-SOC-01
- Windows authentication and security event generation
- Additional identity-related SOC testing

### Addressing Requirements

The future domain controller will require:

1. A stable static IPv4 address.
2. A defined subnet and default gateway.
3. Internal DNS configuration.
4. Appropriate firewall access from domain clients.
5. An IP address that does not conflict with existing infrastructure.

**No IP address has been assigned to the future domain controller.**

The final placement and addressing will be determined during the Windows Server deployment.

### DNS Migration Considerations

Once Active Directory DNS is deployed:

- WIN11-SOC-01 will use the internal domain DNS server.
- The domain controller will host the required Active Directory DNS records.
- External DNS resolution will be provided through configured forwarding or recursion.
- Other network segments will not automatically be migrated to AD DNS unless required.

The internet-exposed DSHIELD honeypot will remain outside the Active Directory domain.

---

## 15. Planned Linux Container

A future Proxmox LXC container is being considered for an AI-assisted cybersecurity job discovery application.

The application is intended to help identify SOC Analyst and Junior Security Engineer opportunities.

### Addressing Status

| Item | Status |
|---|---|
| Deployment platform | Proxmox LXC — planned |
| Container hostname | Not assigned |
| VLAN | Not assigned |
| IPv4 address | Not assigned |
| DHCP/static decision | Pending |
| DNS configuration | Pending |

The container will receive an address appropriate to its selected network and access requirements.

No dedicated virtual machine is required solely for this planned application.

---

## 16. Addressing and Security Principles

The HomeLAB follows these general addressing principles:

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

### Public Documentation

This document includes private RFC 1918 addresses because they describe the lab's internal architecture.

Passwords, API keys, public WAN addresses, and unnecessary hardware identifiers are excluded.

---

## 17. Verification Commands

### FortiGate — DHCP

```shell
show system dhcp server
```

### FortiGate — DNS

```shell
show system dns
```

### FortiGate — Interfaces and VLANs

```shell
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

---

## 18. Validation Status

| Component | Status |
|---|---|
| FortiGate internal IP | Verified |
| VLAN IDs | Verified |
| VLAN interface addresses | Verified |
| Default gateways | Verified |
| DHCP scopes | Verified |
| DHCP-configured DNS | Verified |
| FortiGate system DNS | Verified |
| WAZUH-01 static IP | Verified |
| WAZUH-01 gateway and DNS | Verified |
| WIN11-SOC-01 static IP | Verified |
| WIN11-SOC-01 gateway and DNS | Verified |
| DSHIELD-01 static IP | User confirmed |
| DSHIELD-01 DNS | Not independently verified |
| GAME-SERVER-01 DHCP reservation | Verified |
| Gaming DHCP effective lease duration | Not independently verified |
| Future Active Directory addressing | Planned |
| Future LXC addressing | Planned |

---

## 19. Future Improvements

Potential future improvements include:

- Documenting the effective DNS configuration of DSHIELD-01.
- Confirming the assignment methods of the Proxmox host and Ubiquiti switch.
- Recording the effective Gaming VLAN DHCP lease duration.
- Implementing internal Active Directory DNS.
- Assigning a static IP address to the future domain controller.
- Updating WIN11-SOC-01 DNS before joining the domain.
- Maintaining an inventory of additional infrastructure IP addresses.
- Reviewing unused legacy FortiGate address objects.
- Creating updated network diagrams reflecting the finalized addressing scheme.

---

## 20. Summary

The CyberSecurity HomeLAB uses seven IPv4 network segments:

- One internal infrastructure management network.
- Six dedicated VLANs.
- Six FortiGate DHCP scopes.
- One Security VLAN without FortiGate DHCP.
- Static IPv4 assignments for the primary security VMs.
- A DHCP reservation for the Palworld gaming server.
- Separate DNS configurations for FortiGate services, DHCP clients, and selected security endpoints.

This addressing structure supports network segmentation, controlled administrative access, centralized security monitoring, and future Active Directory integration.

**Document Status:** Current addressing configuration documented and validated, with remaining exceptions explicitly identified.

**Last Reviewed:** October 20, 2026
