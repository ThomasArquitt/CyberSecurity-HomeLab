# 06 — Proxmox Configuration

## 1. Overview

Proxmox Virtual Environment (Proxmox VE) serves as the primary virtualization platform for the CyberSecurity-HomeLab.

The Proxmox host provides the compute, storage, and virtual networking resources required to operate the laboratory's cybersecurity infrastructure.

The environment supports hands-on experience with:

- Virtualization and infrastructure administration
- Network segmentation and VLAN configuration
- Security Information and Event Management (SIEM)
- Endpoint monitoring and threat detection
- Honeypot deployment and attack telemetry collection
- Security testing and incident simulation
- Backup management and disaster recovery
- Linux system administration and troubleshooting

The virtualization environment currently hosts three active virtual machines dedicated to cybersecurity operations.

**Last Updated:** October 20, 2026

> **Security Note:** This document is a sanitized representation of the operational Proxmox environment. Exact management IP addresses, internal VLAN identifiers, endpoint addressing, hardware identifiers, storage UUIDs, backup job identifiers, and sensitive configuration details are intentionally excluded.

---

## 2. Physical Host Hardware

The Proxmox hypervisor runs on a dedicated Mini PC.

### Hardware Inventory

| Component | Configuration |
|---|---|
| Hypervisor | Proxmox Virtual Environment |
| Proxmox Version | 9.2.10 |
| Processor | Intel Core i7-11370H |
| Logical CPUs | 8 |
| System Memory | 32 GB RAM |
| Primary Storage | 512 GB NVMe SSD |
| External Backup Storage | Seagate Portable 5 TB USB HDD |
| Management Network | Dedicated Infrastructure Management Network |
| Timezone | Eastern Time |

The Proxmox host uses an internal NVMe SSD for the hypervisor and virtual machine storage.

An external USB hard drive provides separate storage for virtual machine backup archives.

The external drive is physically connected to the Proxmox host and is not a Network-Attached Storage (NAS) device.

### Hardware Design Considerations

The host was selected to provide sufficient resources for multiple virtual machines while maintaining a small physical footprint and relatively low operational overhead.

The current configuration supports the existing security workloads without requiring a dedicated rack-mounted server.

Resource utilization and storage capacity will continue to be monitored as additional services are introduced.

---

## 3. Proxmox Management

The Proxmox host is connected to the infrastructure management network.

Administrative access is provided through authorized management services.

### Management Configuration

| Setting | Configuration |
|---|---|
| Management Network | Infrastructure Management |
| Management Bridge | Linux Bridge |
| Web Management | HTTPS |
| Remote Administration | SSH |
| CLI Management | Enabled |
| REST API | Available |

### Administrative Access

The primary administrative workstation resides on the trusted Home network.

A dedicated FortiGate firewall policy permits authorized management traffic from this workstation to the Proxmox host.

This design separates administrative access from the security workloads running within the Security VLAN.

### Administrative Access Methods

The host can be administered using:

- Proxmox web management interface
- SSH
- Proxmox command-line utilities
- Proxmox REST API

### Common Administrative Commands

Verify the installed Proxmox version:

```bash
pveversion
```

List configured virtual machines:

```bash
qm list
```

Review storage availability:

```bash
pvesm status
```

Inspect scheduled backup jobs:

```bash
pvesh get /cluster/backup
```

These commands provide information about the installed Proxmox version, virtual machines, storage, and backup configuration.

### Security Considerations

Management access is controlled through applicable firewall policies, authentication, and device-level administrative configuration.

Exact management endpoints and access policy identifiers are excluded from public documentation.

---

## 4. Virtual Networking

Proxmox uses a Linux bridge to provide network connectivity to its virtual machines.

### Bridge Configuration

| Setting | Configuration |
|---|---|
| Bridge Type | Linux Bridge |
| Physical Interface | Dedicated Ethernet Interface |
| VLAN Aware | Yes |
| VLAN Support | IEEE 802.1Q |
| Spanning Tree Protocol | Disabled |
| Forwarding Delay | 0 |
| Host Management Address | Infrastructure Management Network |

The bridge connects virtual machine network interfaces to the physical network.

VLAN tagging allows multiple logical networks to use the same physical interface while maintaining separation through the FortiGate firewall and managed switching infrastructure.

### Security VLAN

All three active cybersecurity virtual machines are assigned to the dedicated Security VLAN.

| Virtual Machine | Operating System | Network |
|---|---|---|
| DShield Honeypot | Ubuntu Server | Security VLAN |
| Windows SOC Endpoint | Windows 11 Pro | Security VLAN |
| Wazuh SIEM | Ubuntu Server | Security VLAN |

The FortiGate provides Layer 3 routing and firewall policy enforcement between the Security VLAN and other networks.

### Virtual Network Adapters

Each active virtual machine uses a VirtIO network adapter connected to the Proxmox Linux bridge.

VLAN tagging is configured through the Proxmox virtual machine network settings.

### Example Sanitized Network Configuration

```text
net0: virtio=<MAC_ADDRESS>,bridge=<BRIDGE_NAME>,firewall=1,tag=<VLAN_ID>
```

This example demonstrates the structure of a VLAN-tagged VirtIO network interface without disclosing the operational configuration.

### Proxmox Firewall Flag

The `firewall=1` parameter enables the Proxmox firewall flag for the virtual network interface.

Actual packet filtering depends on the applicable Proxmox firewall configuration and enabled rules.

The FortiGate remains the primary network security enforcement point for inter-VLAN traffic.

---

## 5. Virtual Machine Inventory

The HomeLAB currently operates three active cybersecurity virtual machines.

### Active Virtual Machines

| Virtual Machine | Operating System | vCPU | RAM | Virtual Disk |
|---|---|---|---|---|
| DShield Honeypot | Ubuntu Linux | 2 | 4 GB | 40 GB |
| Windows SOC Endpoint | Windows 11 Pro | 4 | 8 GB | 80 GB |
| Wazuh SIEM | Ubuntu Server | 4 | 8 GB | 80 GB |

Total allocated virtual machine memory is approximately 20 GB.

Virtual CPUs are scheduled by the hypervisor and are not dedicated physical processor cores.

### Virtual Machine Architecture

```text
                    PROXMOX VE HOST
                  Intel Core i7 / 32 GB
                           |
             +-------------+-------------+
             |             |             |
             |             |             |
        DSHIELD VM     WINDOWS SOC     WAZUH SIEM
             |             |             |
        Ubuntu Linux   Windows 11    Ubuntu Server
             |             |             |
         2 vCPU          4 vCPU         4 vCPU
         4 GB RAM        8 GB RAM       8 GB RAM
         40 GB Disk      80 GB Disk     80 GB Disk
             |             |             |
             +-------------+-------------+
                           |
                     SECURITY VLAN
                           |
                       FORTIGATE
                           |
                  OTHER NETWORK SEGMENTS
```

---

## 6. DShield Honeypot Virtual Machine

### Purpose

The DShield virtual machine provides honeypot services and malicious activity collection.

### Virtual Machine Configuration

| Setting | Configuration |
|---|---|
| Operating System | Ubuntu Linux |
| CPU Cores | 2 |
| CPU Type | Host |
| Memory | 4 GB |
| Virtual Disk | 40 GB |
| Disk Interface | VirtIO |
| Firmware | UEFI (OVMF) |
| Machine Type | Q35 |
| Network | Security VLAN |
| Network Adapter | VirtIO |
| QEMU Guest Agent | Enabled |

### Security Function

The virtual machine hosts the DShield/Cowrie honeypot environment.

The honeypot collects activity from systems interacting with intentionally exposed services.

Collected telemetry supports:

- Unauthorized connection analysis
- Credential attack observation
- Suspicious command collection
- Automated attack behavior analysis
- SIEM ingestion
- Detection engineering

### Network Isolation

The honeypot operates within the dedicated Security VLAN.

FortiGate firewall policies control communication between the honeypot and other network segments.

Externally accessible honeypot services are published through controlled firewall and NAT configurations.

Exact external port mappings and destination addresses are excluded from public documentation.

---

## 7. Windows SOC Endpoint Virtual Machine

### Purpose

The Windows SOC endpoint provides a controlled environment for endpoint telemetry collection, security testing, and SOC investigation exercises.

### Virtual Machine Configuration

| Setting | Configuration |
|---|---|
| Operating System | Windows 11 Pro |
| CPU Cores | 4 |
| Memory | 8 GB |
| Virtual Disk | 80 GB |
| Disk Interface | SCSI |
| Firmware | UEFI (OVMF) |
| Machine Type | Q35 |
| Virtual TPM | TPM 2.0 |
| Network | Security VLAN |
| Network Adapter | VirtIO |
| QEMU Guest Agent | Enabled |

### Installed Security Tooling

The endpoint includes:

- Microsoft Defender
- Sysmon
- PowerShell logging
- Wazuh agent
- Atomic Red Team
- AtomicSOC testing environment

### AtomicSOC

AtomicSOC provides a controlled environment for executing security tests and generating endpoint telemetry.

The application supports practical experience with:

- Security event generation
- Endpoint investigation
- Detection testing
- MITRE ATT&CK techniques
- PowerShell execution monitoring
- SIEM alert analysis

### Configuration Review Finding

During the infrastructure review, the Proxmox guest operating system type was configured as Linux despite the guest running Windows 11.

This configuration should be corrected to the appropriate Windows guest type after confirming compatibility settings.

**Status:** Configuration correction pending.

### Future Active Directory Integration

The Windows SOC endpoint is planned to join a future Active Directory domain.

This will support additional identity-related security monitoring and investigation exercises.

---

## 8. Wazuh SIEM Virtual Machine

### Purpose

The Wazuh virtual machine provides centralized security monitoring and event analysis.

### Virtual Machine Configuration

| Setting | Configuration |
|---|---|
| Operating System | Ubuntu Server |
| CPU Cores | 4 |
| CPU Type | x86-64-v2-AES |
| Memory | 8 GB |
| Virtual Disk | 80 GB |
| Disk Interface | SCSI |
| Network | Security VLAN |
| Network Adapter | VirtIO |
| QEMU Guest Agent | Enabled |

### Wazuh Responsibilities

The Wazuh environment provides:

- Security event collection
- Log analysis
- Alert generation
- Endpoint monitoring
- Custom detection rules
- Security investigations
- Detection engineering

### Current Integrations

The environment collects telemetry from:

- Windows endpoint events
- Sysmon
- DShield/Cowrie honeypot activity
- FortiGate firewall syslog
- Dedicated gaming server monitoring

### Detection Engineering

Custom detection rules have been created and tuned to improve the usefulness of collected security telemetry.

This work supports practical experience with:

- Security alert investigation
- Log analysis
- Rule tuning
- False-positive reduction
- Event correlation
- Detection validation

Detailed Wazuh configuration is documented separately.

---

## 9. Proxmox Storage Configuration

The Proxmox host uses three storage definitions.

### Storage Architecture

| Storage | Type | Purpose |
|---|---|---|
| Local Storage | Directory | ISO Images, Templates, Imports |
| Virtual Disk Storage | LVM-Thin | Virtual Machine Disks |
| External Backup Storage | Directory | VM Backup Archives and ISO Storage |

### Local Storage

Proxmox local directory storage supports:

- ISO images
- Container templates
- Backup archives
- Imports

### LVM-Thin Storage

The primary virtual machine storage uses LVM-thin provisioning.

Thin provisioning allows virtual disk capacity to be allocated independently from immediately consumed physical storage.

Available capacity must still be monitored to avoid exhausting the underlying storage pool.

### External Backup Storage

| Setting | Configuration |
|---|---|
| Device | Seagate Portable 5 TB USB HDD |
| Filesystem | ext4 |
| Connection | Direct USB |
| Mount Configuration | Persistent |
| Mount Identification | Filesystem UUID |
| Proxmox Storage Type | Directory |
| Primary Purpose | Compressed VM Backups |

The external storage provides a separate physical location for virtual machine backup archives.

### Persistent Mount Configuration

The external drive is mounted using its filesystem UUID rather than relying on a device name such as `/dev/sda1`.

### Example Sanitized fstab Entry

```text
UUID=<FILESYSTEM_UUID> /mnt/storage ext4 defaults 0 2
```

Using a filesystem UUID helps ensure that the correct partition is mounted even if Linux assigns a different device name after a reboot or USB reconnection.

It does not prevent USB disconnects or filesystem errors.

### Storage Verification

Check mounted filesystems:

```bash
findmnt
```

Review Proxmox storage status:

```bash
pvesm status
```

Inspect block devices and filesystems:

```bash
lsblk -f
```

These commands verify filesystem mounts, Proxmox storage availability, and block device information.

---

## 10. Backup Strategy

Proxmox provides scheduled virtual machine backups through its integrated `vzdump` functionality.

The HomeLAB uses compressed backup archives stored on the external Seagate drive.

### Scheduled Backup Configuration

| Setting | Configuration |
|---|---|
| Enabled | Yes |
| Schedule | Daily at 3:00 AM |
| Timezone | Eastern Time |
| Backup Mode | Snapshot |
| Compression | Zstandard (`zstd`) |
| Destination | External Backup Storage |
| VM Selection | Three Active Security VMs |
| Missed Runs | Not Automatically Repeated |
| Notification Mode | Proxmox Notification System |

The backup schedule was selected to minimize interference with normal HomeLAB administration and security testing activities.

Snapshot-mode backups allow Proxmox to back up running virtual machines without requiring a routine full shutdown.

A backup may still introduce temporary I/O load or brief pauses.

Application-consistent backups depend on guest integration and workload behavior.

### Backup Retention

The scheduled backup job uses the following retention policy:

| Retention Category | Value |
|---|---|
| Daily | 7 |
| Weekly | 4 |
| Monthly | 3 |

Proxmox retains recovery points according to the configured daily, weekly, and monthly selection rules.

These categories do not necessarily produce a fixed total number of backup archives.

### Initial Backup Coverage

On October 20, 2026, fresh backup archives were created for all three active security virtual machines.

| Virtual Machine | Backup Date | Approximate Archive Size |
|---|---|---|
| DShield Honeypot | October 20, 2026 | 5.09 GiB |
| Windows SOC Endpoint | October 20, 2026 | 22.02 GiB |
| Wazuh SIEM | October 20, 2026 | 7.31 GiB |

The backup archives were verified as present and recognized by Proxmox.

Archive integrity testing and full VM restore testing remain pending.

### Backup Verification Commands

List available storage:

```bash
pvesm status
```

Inspect scheduled backup jobs:

```bash
pvesh get /cluster/backup
```

List backup archives:

```bash
pvesm list <BACKUP_STORAGE_ID> --content backup
```

Manually create a backup:

```bash
vzdump <VM_ID> \
    --storage <BACKUP_STORAGE_ID> \
    --mode snapshot \
    --compress zstd
```

Manual backups can be useful before significant configuration changes.

---

## 11. Snapshots vs. Backups

Proxmox supports both virtual machine snapshots and full VM backup archives.

These mechanisms serve different purposes.

### Virtual Machine Snapshots

Snapshots provide recovery points associated with a VM's underlying storage.

They are useful before activities such as:

- Detection rule changes
- Software installation
- Security testing
- Operating system configuration changes
- Major application updates

Snapshots are not a substitute for independent backups.

If the underlying VM storage fails, snapshots stored on that same storage may also be lost.

### Full VM Backups

Full VM backups create separate recovery archives.

The HomeLAB stores these archives on an external physical drive, independently of the internal NVMe storage used by active VM disks.

This provides an additional recovery option if the primary VM storage becomes unavailable.

However, the external USB drive is currently the only independent backup destination.

An additional backup copy or off-site destination would improve resilience against drive failure, theft, or physical damage.

---

## 12. Backup Storage Incident and Recovery

### Incident Summary

On September 24, 2026, the external Seagate backup drive experienced a USB disconnect.

The event caused the mounted ext4 filesystem to enter a shutdown state following storage I/O errors.

Although the drive subsequently reappeared under a different Linux device name, the existing mount remained associated with the failed device state.

As a result, the Proxmox external backup storage became unavailable.

### Investigation

The investigation identified:

- A USB device disconnect and subsequent reconnection
- Linux device renumbering
- A stale filesystem mount
- An aborted ext4 journal
- Open Windows VM ISO file handles preventing normal unmounting
- Proxmox backup storage reported as inactive

The underlying cause of the USB disconnect was not conclusively identified.

Potential hardware or connection issues require further investigation if the problem recurs.

### Recovery Actions

The following recovery procedure was performed:

1. Identified the external drive using its filesystem UUID.
2. Investigated the stale mount and open file handles.
3. Detached two ISO images from the Windows SOC endpoint.
4. Unmounted the affected filesystem.
5. Performed an initial read-only filesystem inspection.
6. Mounted the filesystem read-only without journal replay to inspect backup files.
7. Unmounted the recovery mount.
8. Ran an ext4 filesystem repair using `e2fsck`.
9. Recovered the filesystem journal.
10. Remounted the drive.
11. Verified that Proxmox recognized the external backup storage as active.
12. Created fresh backup archives for all active security VMs.

The filesystem repair completed successfully, and the drive returned to read/write operation.

### Backup Coverage Findings

The investigation also revealed that the existing automated backup job targeted a retired Ubuntu management virtual machine.

The active security VMs were not included in that scheduled job.

The backup job was subsequently updated to protect all three active security VMs.

### Backup Cleanup

Historical backup archives for the retired Ubuntu management VM had accumulated over several months.

The archive inventory was reviewed, and older backups were removed.

The most recent backup for the retired VM was preserved as a recovery point.

After cleanup, external storage utilization decreased from approximately 8.48% to 1.19%.

This recovered several hundred gigabytes of backup storage capacity.

### Lessons From the Incident

The incident demonstrated the importance of:

- Monitoring external storage reliability
- Verifying backup coverage
- Maintaining accurate backup job inventories
- Using filesystem UUIDs for persistent mounts
- Understanding Linux filesystem recovery
- Testing backup and restore procedures
- Maintaining backup retention policies

---

## 13. Backup Notifications

The scheduled backup job is integrated with the Proxmox notification system.

### Existing Notification Configuration

The original notification configuration included:

- Built-in email notification target
- Default notification matcher
- Routing of notifications to the administrator email address

Investigation showed that outgoing messages were being rejected by the recipient mail provider when sent directly from the HomeLAB's public IP address.

### Authenticated SMTP Configuration

A separate authenticated SMTP endpoint was created.

The endpoint was successfully added to Proxmox.

However, the initial SMTP delivery test failed with an authentication error.

The existing notification matcher has not yet been switched to the new SMTP endpoint.

**Status:** Backup notification delivery remains unresolved and requires additional testing.

The configured backup schedule and retention policy operate independently of this notification issue.

### Future Notification Improvements

Planned improvements include:

- Resolving SMTP authentication
- Validating successful email delivery
- Updating notification matcher routing
- Testing backup failure notifications
- Confirming that backup alerts reach the administrator

---

## 14. Recovery Strategy

The recovery strategy relies on a combination of VM snapshots, compressed backup archives, and configuration documentation.

### Recovery Scenarios

| Scenario | Recovery Approach |
|---|---|
| Failed VM Configuration Change | Revert to an appropriate snapshot |
| Guest Operating System Corruption | Restore from a verified VM backup |
| Virtual Disk Loss | Restore a VM backup to replacement storage |
| Hypervisor Installation Failure | Reinstall Proxmox and restore VM backups |
| External Backup Drive Failure | Existing backups on that drive may be unavailable |

### Current Recovery Readiness

The HomeLAB has completed the following:

- Configured external backup storage
- Created backups for all three active security VMs
- Configured a daily backup schedule
- Configured automatic backup retention
- Recovered an ext4 filesystem following a storage incident

The following activities remain outstanding:

- Validate backup archive integrity
- Perform a test VM restore
- Confirm successful execution of scheduled backups
- Resolve email notification delivery
- Evaluate an additional backup destination
- Investigate recurring USB connectivity issues if observed

A successful backup job does not by itself guarantee that a virtual machine can be restored and booted.

A controlled restore test is necessary to validate end-to-end recovery.

---

## 15. Design Decisions

### Dedicated Security VLAN

Security workloads operate within a dedicated Security VLAN.

This allows firewall policies to control communication between the security environment and other HomeLAB networks.

### Centralized Virtualization

Proxmox consolidates multiple security workloads onto a single physical host.

This reduces hardware requirements and simplifies infrastructure management.

### External Backup Storage

Backup archives are stored on a separate physical drive rather than exclusively on the internal NVMe SSD.

This provides protection against certain primary-storage failures.

### Overnight Backup Window

Backups are scheduled for 3:00 AM Eastern to reduce interference with interactive HomeLAB activities.

### Defined Backup Retention

Daily, weekly, and monthly retention settings provide multiple recovery points while limiting unnecessary storage growth.

### CLI-Based Administration

Proxmox administration and troubleshooting are performed primarily through SSH and command-line utilities.

This supports repeatable procedures and reinforces Linux system administration skills.

---

## 16. Lessons Learned

The Proxmox environment has provided practical experience with virtualization, storage administration, network segmentation, and operational recovery.

### Key Technical Lessons

**1. Backup coverage must be verified.**

An enabled backup job does not guarantee that every active VM is protected.

**2. Snapshots and backups serve different purposes.**

Snapshots are useful for short-term rollback, while independent backup archives support broader recovery scenarios.

**3. External storage introduces additional failure points.**

USB connectivity issues can interrupt backups even when the hypervisor and virtual machines remain operational.

**4. Filesystem UUIDs improve mount consistency.**

Device names may change after a USB disconnect or reboot.

**5. Filesystem repair requires careful preparation.**

Open file handles and mounted filesystems must be handled appropriately before running repair operations.

**6. Backup retention requires deliberate configuration.**

Without suitable retention rules, historical archives can consume unnecessary storage capacity.

**7. Notification routing is not the same as notification delivery.**

SMTP connectivity and successful message delivery must be tested.

**8. Recovery readiness requires restore testing.**

Backup files should not be considered fully validated until their integrity and recoverability have been confirmed.

**9. VLAN-aware virtualization supports network segmentation.**

Proxmox can host multiple virtual machines on segmented networks using VLAN-aware Linux bridges.

**10. Centralized infrastructure simplifies security operations.**

Consolidating security workloads provides a consistent platform for monitoring, testing, and investigation.

---

## 17. Planned Improvements

The following improvements are planned for the Proxmox environment:

- Complete backup notification configuration
- Verify the first scheduled 3:00 AM backup execution
- Perform controlled VM restore testing
- Monitor external backup drive reliability
- Evaluate additional backup redundancy
- Correct the Windows 11 VM guest operating system type
- Review VM resource allocation as workloads expand
- Deploy a Windows Server VM for Active Directory Domain Services
- Configure DNS and Group Policy in the Windows domain environment
- Join the Windows SOC endpoint to the Active Directory domain
- Evaluate lightweight Linux containers for future automation workloads

These improvements will expand the HomeLAB's capabilities while maintaining a documented and recoverable infrastructure baseline.

---

## 18. Documentation References

Related documentation includes:

- `01_Project_Overview.md`
- `02_Hardware_Inventory.md`
- `03_Network_Architecture.md`
- `04_VLAN_Design.md`
- `05_IP_Addressing_Plan.md`
- `07_FortiGate_Configuration.md`
- `08_Ubiquiti_Switch_Configuration.md`
- `09_Ubuntu_Server.md`
- `10_DShield_Honeypot.md`
- `11_Backup_and_Recovery.md`

---

## 19. Summary

Proxmox VE provides the virtualization foundation for the CyberSecurity-HomeLab.

The current environment supports three active security virtual machines, VLAN-aware networking, centralized security monitoring, honeypot operations, and controlled Windows endpoint testing.

The backup configuration was significantly improved following the discovery and recovery of an external storage failure.

All three active virtual machines now have backup archives, and an automated daily backup job is configured with defined retention policies.

Additional work remains to validate restore procedures, complete notification delivery, and improve backup redundancy.

The environment provides a practical platform for continued cybersecurity engineering, infrastructure administration, and SOC-focused training.

**Document Status:** Current Proxmox configuration documented, with remaining validation and improvement activities explicitly identified.

**Last Reviewed:** October 20, 2026
