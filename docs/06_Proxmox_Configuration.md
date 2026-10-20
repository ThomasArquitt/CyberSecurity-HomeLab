# 06 — Proxmox Configuration

## 1. Overview

Proxmox Virtual Environment (Proxmox VE) serves as the primary virtualization platform for the HomeLAB.

The Proxmox host provides the compute, storage, and virtual networking resources required to operate the cybersecurity lab's virtual machines.

The environment is designed to support hands-on experience with:

- Virtualization and infrastructure administration
- Network segmentation and VLAN configuration
- Security information and event management (SIEM)
- Endpoint monitoring and threat detection
- Honeypot deployment and attack telemetry collection
- Security testing and incident simulation
- Backup management and disaster recovery

The virtualization environment currently hosts three active virtual machines dedicated to cybersecurity operations.

---

## 2. Physical Host Hardware

The Proxmox hypervisor runs on a dedicated Mini PC.

| Component | Configuration |
|---|---|
| Hypervisor | Proxmox Virtual Environment |
| Proxmox Version | 9.2.10 |
| Hostname | `pve` |
| Processor | Intel Core i7-11370H |
| Logical CPUs | 8 |
| System Memory | 32 GB RAM |
| Primary Storage | 512 GB NVMe SSD |
| External Backup Storage | Seagate Portable 5 TB USB HDD |
| Management IP | `192.168.1.10/24` |
| Default Gateway | `192.168.1.1` |
| Timezone | America/New_York |

The Proxmox host uses a dedicated internal NVMe SSD for the hypervisor and virtual machine storage.

An external USB hard drive provides separate storage for virtual machine backup archives.

The external drive is physically connected to the Proxmox host and is not a network-attached storage device.

### Hardware Design Considerations

The host was selected to provide sufficient resources for multiple virtual machines while maintaining a relatively small physical footprint and low operational overhead.

The current configuration supports the existing security workloads without requiring a dedicated rack-mounted server.

Resource utilization and storage capacity will be monitored as additional services are introduced.

---

## 3. Proxmox Management

The Proxmox management interface is accessible at:

`https://192.168.1.10:8006`

The host is connected to the internal management network.

| Setting | Value |
|---|---|
| Hostname | `pve` |
| Management Address | `192.168.1.10/24` |
| Default Gateway | `192.168.1.1` |
| Management Bridge | `vmbr0` |
| Management Protocols | HTTPS and SSH |

Administrative access is restricted through the FortiGate firewall.

The primary administrative workstation, TIC-PC, resides on VLAN 10 and uses IP address `10.1.10.50`.

A FortiGate firewall policy permits authorized management traffic from TIC-PC to the Proxmox host.

This design separates administrative access from the security workloads running on VLAN 69.

### Administrative Access Methods

The host can be administered using:

- Proxmox web management interface
- SSH
- Proxmox command-line utilities
- Proxmox REST API

Common administrative commands include:

```bash
pveversion
```

```bash
qm list
```

```bash
pvesm status
```

```bash
pvesh get /cluster/backup
```

These commands provide information about the installed Proxmox version, virtual machines, storage, and backup configuration.

---

## 4. Virtual Networking

Proxmox uses a Linux bridge named `vmbr0` to provide network connectivity to its virtual machines.

### Bridge Configuration

| Setting | Configuration |
|---|---|
| Bridge | `vmbr0` |
| Physical Interface | `nic0` |
| VLAN Aware | Yes |
| VLAN Range | 2–4094 |
| Spanning Tree Protocol | Disabled |
| Forwarding Delay | 0 |
| Host IP | `192.168.1.10/24` |

The bridge connects virtual machine interfaces to the physical network.

VLAN tagging allows multiple logical networks to use the same physical network interface while maintaining separation through the FortiGate firewall and managed switching infrastructure.

### Security VLAN

All three active security virtual machines are assigned to VLAN 69.

| VM | Name | IP Address | VLAN |
|---|---|---|---|
| 101 | DSHIELD-01 | `10.1.69.70` | 69 |
| 102 | WIN11-SOC-01 | `10.1.69.71` | 69 |
| 103 | WAZUH-01 | `10.1.69.72` | 69 |

The default gateway for VLAN 69 is:

`10.1.69.1`

The FortiGate firewall provides Layer 3 routing and policy enforcement between the security VLAN and other networks.

### Virtual Network Adapters

Each virtual machine uses a VirtIO network adapter connected to `vmbr0`.

The Proxmox configuration explicitly assigns VLAN tag 69 to each VM interface.

Example configuration:

```text
net0: virtio=<MAC_ADDRESS>,bridge=vmbr0,firewall=1,tag=69
```

The `firewall=1` parameter enables the Proxmox firewall flag for the interface.

Actual packet filtering depends on the applicable Proxmox firewall configuration and enabled rules.

FortiGate remains the primary network security enforcement point for inter-VLAN traffic.

---

## 5. Virtual Machine Inventory

The HomeLAB currently operates three active virtual machines.

| VM ID | Hostname | Operating System | vCPU | RAM | Virtual Disk |
|---|---|---|---:|---:|---:|
| 101 | DSHIELD-01 | Ubuntu Linux | 2 | 4 GB | 40 GB |
| 102 | WIN11-SOC-01 | Windows 11 Pro | 4 | 8 GB | 80 GB |
| 103 | WAZUH-01 | Ubuntu Server | 4 | 8 GB | 80 GB |

Total allocated virtual machine memory is approximately 20 GB.

Virtual CPUs are scheduled by the hypervisor and are not dedicated physical processor cores.

### VM 101 — DSHIELD-01

**Purpose:** Honeypot deployment and malicious activity collection.

| Setting | Configuration |
|---|---|
| VM ID | 101 |
| Hostname | DSHIELD-01 |
| Operating System | Ubuntu Linux |
| CPU Cores | 2 |
| CPU Type | Host |
| Memory | 4096 MB |
| Disk | 40 GB |
| Disk Interface | VirtIO |
| Firmware | UEFI (OVMF) |
| Machine Type | Q35 |
| Network Bridge | `vmbr0` |
| VLAN | 69 |
| IP Address | `10.1.69.70` |
| QEMU Guest Agent | Enabled |

DSHIELD-01 hosts the DShield/Cowrie honeypot environment.

The VM collects activity from systems interacting with exposed honeypot services.

Collected events are used for security analysis and SIEM ingestion.

The honeypot operates within the dedicated security VLAN to reduce exposure to the remainder of the HomeLAB.

### VM 102 — WIN11-SOC-01

**Purpose:** Windows endpoint telemetry, detection testing, and SOC investigation exercises.

| Setting | Configuration |
|---|---|
| VM ID | 102 |
| Hostname | WIN11-SOC-01 |
| Operating System | Windows 11 Pro |
| CPU Cores | 4 |
| Memory | 8192 MB |
| Disk | 80 GB |
| Disk Interface | SCSI |
| Firmware | UEFI (OVMF) |
| Machine Type | Q35 |
| Virtual TPM | TPM 2.0 |
| Network Bridge | `vmbr0` |
| VLAN | 69 |
| IP Address | `10.1.69.71` |
| QEMU Guest Agent | Enabled |

WIN11-SOC-01 functions as the primary Windows security testing endpoint.

Its security tooling includes:

- Microsoft Defender
- Sysmon
- PowerShell logging
- Wazuh agent
- AtomicSOC testing environment

AtomicSOC provides a controlled environment for executing security tests and generating endpoint telemetry.

These activities support investigation practice and validation of SIEM detection capabilities.

**Configuration note:** The Proxmox guest OS type is currently set to `l26`, despite the guest running Windows 11. This configuration should be corrected to the appropriate Windows guest type after confirming the relevant Proxmox compatibility settings.

### VM 103 — WAZUH-01

**Purpose:** Centralized security monitoring and event analysis.

| Setting | Configuration |
|---|---|
| VM ID | 103 |
| Hostname | WAZUH-01 |
| Operating System | Ubuntu Server |
| CPU Cores | 4 |
| CPU Type | x86-64-v2-AES |
| Memory | 8192 MB |
| Disk | 80 GB |
| Disk Interface | SCSI |
| Network Bridge | `vmbr0` |
| VLAN | 69 |
| IP Address | `10.1.69.72` |
| QEMU Guest Agent | Enabled |

WAZUH-01 provides the centralized Wazuh security monitoring environment.

The server receives and analyzes security telemetry from multiple HomeLAB sources.

Current integrations include:

- Windows endpoint telemetry
- Sysmon events
- DShield/Cowrie honeypot events
- FortiGate firewall logs

The environment also includes custom detection rules and tuning intended to reduce unnecessary alerts and improve the usefulness of security telemetry.

The Wazuh implementation is documented separately in the SIEM configuration documentation.

---

## 6. Proxmox Storage Configuration

The Proxmox host uses three storage definitions.

| Storage ID | Type | Purpose |
|---|---|---|
| `local` | Directory | ISO images, templates, imports, and backup-capable local storage |
| `local-lvm` | LVM-thin | Virtual machine disks |
| `HOMELAB_5TB` | Directory | External VM backups and ISO storage |

### Local Storage

Storage ID:

`local`

Filesystem path:

`/var/lib/vz`

This storage supports:

- ISO images
- Container templates
- Backup archives
- Imports

### LVM-Thin Storage

Storage ID:

`local-lvm`

Volume group and thin pool:

`pve/data`

This storage provides the virtual disks used by the active virtual machines.

The thin-provisioned storage design allows virtual disk capacity to be allocated independently from immediately consumed physical storage.

Available capacity must still be monitored to avoid exhausting the underlying storage pool.

### External Backup Storage

Storage ID:

`HOMELAB_5TB`

Device:

Seagate Portable 5 TB USB hard drive

Filesystem:

`ext4`

Mount point:

`/mnt/storage`

Proxmox storage path:

`/mnt/storage/Backups/Proxmox`

The external storage is configured as a Proxmox directory storage target.

Its primary role is to hold compressed virtual machine backup archives.

### Persistent Mount Configuration

The drive is mounted using its filesystem UUID rather than relying on a device name such as `/dev/sda1`.

The `/etc/fstab` configuration is:

```text
UUID=288fbe19-449c-4302-8484-9c2b7b78e085 /mnt/storage ext4 defaults 0 2
```

Using a filesystem UUID helps ensure that the correct partition is mounted even if Linux assigns a different device name after a reboot or USB reconnection.

It does not prevent USB disconnects or filesystem errors.

### Storage Verification

Useful commands include:

```bash
findmnt /mnt/storage
```

```bash
pvesm status
```

```bash
lsblk -f
```

These commands verify the filesystem mount, Proxmox storage status, and block device information.

---

## 7. Backup Strategy

Proxmox provides scheduled virtual machine backups through its integrated `vzdump` functionality.

The HomeLAB uses compressed backup archives stored on the external Seagate drive.

### Scheduled Backup Configuration

| Setting | Configuration |
|---|---|
| Job ID | `backup-53762cb1-c62d` |
| Enabled | Yes |
| Schedule | Daily at 3:00 AM |
| Timezone | America/New_York |
| Backup Mode | Snapshot |
| Compression | Zstandard (`zstd`) |
| Destination | `HOMELAB_5TB` |
| VM Selection | 101, 102, 103 |
| Missed Runs | Not automatically repeated |
| Notification Mode | Proxmox notification system |

The backup schedule was selected to minimize interference with normal HomeLAB administration and security testing activities.

Snapshot-mode backups allow Proxmox to back up running virtual machines without requiring a routine full shutdown.

A backup may still introduce temporary I/O load or brief pauses, and application-consistent backups depend on guest integration and workload behavior.

### Backup Retention

The scheduled job uses the following retention policy:

| Retention Category | Value |
|---|---:|
| Daily | 7 |
| Weekly | 4 |
| Monthly | 3 |

Proxmox retains recovery points according to the configured daily, weekly, and monthly selection rules.

These categories do not necessarily produce a fixed total number of backup archives.

### Initial Backup Coverage

On October 20, 2026, fresh backup archives were created for all three active security virtual machines.

| VM ID | Virtual Machine | Backup Date | Approximate Archive Size |
|---|---|---|---:|
| 101 | DSHIELD-01 | October 20, 2026 | 5.09 GiB |
| 102 | WIN11-SOC-01 | October 20, 2026 | 22.02 GiB |
| 103 | WAZUH-01 | October 20, 2026 | 7.31 GiB |

The backup archives were verified as present and recognized by Proxmox.

Archive integrity testing and full VM restore testing remain pending.

### Backup Verification Commands

List backup archives:

```bash
pvesm list HOMELAB_5TB --content backup
```

Check storage availability:

```bash
pvesm status
```

Inspect the scheduled backup job:

```bash
pvesh get /cluster/backup/backup-53762cb1-c62d \
  --output-format json-pretty
```

Manually back up a VM:

```bash
vzdump 103 \
  --storage HOMELAB_5TB \
  --mode snapshot \
  --compress zstd
```

Manual backups can be useful before significant configuration changes.

---

## 8. Snapshots vs. Backups

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

The HomeLAB stores these archives on `HOMELAB_5TB`, independently of the internal NVMe storage used by the active VM disks.

This provides an additional recovery option if the primary VM storage becomes unavailable.

However, the external USB drive is currently the only independent backup destination.

An additional backup copy or off-site destination would improve resilience against drive failure, theft, or physical damage.

---

## 9. Backup Storage Incident and Recovery

### Incident Summary

On September 24, 2026, the external Seagate backup drive experienced a USB disconnect.

The event caused the mounted ext4 filesystem to enter a shutdown state following storage I/O errors.

Although the drive subsequently reappeared under a different Linux device name, the existing mount remained associated with the failed device state.

As a result, the Proxmox external backup storage became unavailable.

### Investigation

The investigation identified:

- A USB device disconnect and subsequent reconnection
- Device renumbering from `/dev/sda1` to `/dev/sdb1`
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
3. Detached two ISO images from WIN11-SOC-01.
4. Unmounted the affected filesystem.
5. Performed an initial read-only filesystem inspection.
6. Mounted the filesystem read-only without journal replay to inspect backup files.
7. Unmounted the recovery mount.
8. Ran an ext4 filesystem repair using `e2fsck`.
9. Recovered the filesystem journal.
10. Remounted the drive at `/mnt/storage`.
11. Verified that Proxmox recognized `HOMELAB_5TB` as active.
12. Created fresh backup archives for all active security VMs.

The filesystem repair completed successfully, and the drive returned to read/write operation.

### Backup Coverage Findings

The investigation also revealed that the existing automated backup job targeted VM 100, an Ubuntu management VM that had been retired.

The active security VMs were not included in that scheduled job.

The backup job was subsequently updated to protect VMs 101, 102, and 103.

### Backup Cleanup

Historical backup archives for retired VM 100 had accumulated over several months.

The archive inventory was reviewed, and older VM 100 backups were removed.

The most recent VM 100 backup, dated September 23, 2026, was preserved as a recovery point.

After cleanup, external storage utilization decreased from approximately 8.48% to 1.19%.

This cleanup recovered several hundred gigabytes of backup storage capacity.

---

## 10. Backup Notifications

The scheduled backup job is integrated with the Proxmox notification system.

### Existing Notification Configuration

The original notification configuration included:

- Built-in `mail-to-root` notification target
- Built-in `default-matcher`
- Routing of notifications to the Proxmox administrator email address

Investigation showed that outgoing messages were being rejected by the recipient mail provider when sent directly from the HomeLAB's public IP address.

### SMTP Configuration

A separate authenticated SMTP endpoint was created.

| Setting | Value |
|---|---|
| Endpoint | `yahoo-smtp` |
| SMTP Server | `smtp.mail.yahoo.com` |
| Port | 465 |
| Encryption | TLS |
| Authentication | Application-specific password |
| Recipient | Administrator email address |

The endpoint was successfully created in Proxmox.

However, the initial SMTP delivery test failed with an authentication error.

The existing notification matcher has not yet been switched to the new SMTP endpoint.

**Status:** Backup notification delivery remains unresolved and requires additional testing.

The configured backup schedule and retention policy operate independently of this notification issue.

---

## 11. Recovery Strategy

The recovery strategy relies on a combination of VM snapshots, compressed backup archives, and configuration documentation.

### Recovery Scenarios

| Scenario | Recovery Approach |
|---|---|
| Failed VM configuration change | Revert to an appropriate snapshot |
| Guest operating system corruption | Restore from a verified VM backup |
| Virtual disk loss | Restore a VM backup to replacement storage |
| Hypervisor installation failure | Reinstall Proxmox and restore VM backups |
| External backup drive failure | Existing backups on that drive may be unavailable |

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

## 12. Design Decisions

### Dedicated Security VLAN

Security workloads are isolated on VLAN 69.

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

## 13. Lessons Learned

The Proxmox environment has provided practical experience with virtualization, storage administration, network segmentation, and operational recovery.

Key lessons include:

1. **Backup coverage must be verified.** An enabled backup job does not guarantee that every active VM is protected.

2. **Snapshots and backups serve different purposes.** Snapshots are useful for short-term rollback, while independent backup archives support broader recovery scenarios.

3. **External storage introduces additional failure points.** USB connectivity issues can interrupt backups even when the hypervisor and virtual machines remain operational.

4. **Filesystem UUIDs improve mount consistency.** Device names may change after a USB disconnect or reboot.

5. **Filesystem repair requires careful preparation.** Open file handles and mounted filesystems must be handled appropriately before running repair operations.

6. **Backup retention requires deliberate configuration.** Without suitable retention rules, historical archives can consume unnecessary storage capacity.

7. **Notification routing is not the same as notification delivery.** SMTP connectivity and successful message delivery must be tested.

8. **Recovery readiness requires restore testing.** Backup files should not be considered fully validated until their integrity and recoverability have been confirmed.

---

## 14. Planned Improvements

The following improvements are planned for the Proxmox environment:

- Complete backup notification configuration
- Verify the first scheduled 3:00 AM backup execution
- Perform controlled VM restore testing
- Monitor external backup drive reliability
- Evaluate additional backup redundancy
- Correct the Windows 11 VM guest OS type
- Review VM resource allocation as workloads expand
- Deploy a Windows Server VM for Active Directory Domain Services
- Configure DNS and Group Policy in the Windows domain environment
- Join WIN11-SOC-01 to the Active Directory domain
- Evaluate lightweight Linux containers for future automation workloads

These improvements will expand the HomeLAB's capabilities while maintaining a documented and recoverable infrastructure baseline.

---

## 15. Summary

Proxmox VE provides the virtualization foundation for the cybersecurity HomeLAB.

The current environment supports three active security virtual machines, VLAN-aware networking, centralized security monitoring, honeypot operations, and controlled Windows endpoint testing.

The backup configuration was significantly improved following the discovery and recovery of an external storage failure.

All three active virtual machines now have backup archives, and an automated daily backup job is configured with defined retention policies.

Additional work remains to validate restore procedures, complete notification delivery, and improve backup redundancy.

The environment provides a practical platform for continued cybersecurity engineering, infrastructure administration, and SOC-focused training.
