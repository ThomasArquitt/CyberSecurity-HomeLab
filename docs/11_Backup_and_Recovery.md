# 11 — Backup and Recovery

## 1. Overview

The CyberSecurity HomeLAB uses a structured backup and recovery strategy to protect virtual machines, security infrastructure, and important system configurations.

The primary objective is to reduce the risk of data loss, minimize recovery time, and provide reliable recovery options following hardware failures, configuration errors, or unsuccessful infrastructure changes.

The environment uses Proxmox Virtual Environment for virtual machine backup management.

The backup strategy incorporates:

- Virtual machine snapshots
- Full virtual machine backup archives
- Dedicated external backup storage
- Automated backup scheduling
- Backup retention policies
- Manual recovery checkpoints
- Backup monitoring
- Storage troubleshooting
- Recovery validation

The environment also provides practical experience investigating backup failures and restoring access to affected storage.

> **Security Note:** This document is a sanitized representation of the HomeLAB backup infrastructure. Exact storage identifiers, management addresses, credentials, and sensitive configuration details are intentionally excluded.

---

## 2. Backup Architecture

The HomeLAB uses Proxmox VE to manage virtual machine backups.

Backup archives are stored on dedicated external storage connected to the virtualization host.

### Logical Architecture

```text
                  PROXMOX HYPERVISOR
                          |
              +-----------+-----------+
              |           |           |
              v           v           v
           DSHIELD      WINDOWS      WAZUH
              VM         SOC VM      SIEM VM
              |           |           |
              +-----------+-----------+
                          |
                          v
                  PROXMOX BACKUP JOB
                          |
                          v
                  SNAPSHOT BACKUP
                          |
                          v
                  ZSTD COMPRESSION
                          |
                          v
                  EXTERNAL STORAGE
                          |
                          v
                  BACKUP RETENTION
```

The external backup destination provides storage separate from the hypervisor's primary internal virtual machine storage.

### Protected Workloads

The current backup strategy includes three active virtual machines.

| Workload | Purpose | Backup Protection |
|---|---|---|
| DShield Honeypot | Attack telemetry collection | Enabled |
| Windows 11 SOC | Security testing and investigation | Enabled |
| Wazuh SIEM | Centralized security monitoring | Enabled |

These systems support the core cybersecurity functions of the HomeLAB.

---

## 3. Backup Storage

The primary backup destination is a dedicated external USB storage device.

### Storage Configuration

| Component | Configuration |
|---|---|
| Storage Type | External USB hard drive |
| Capacity | 5 TB nominal |
| Filesystem | ext4 |
| Host Platform | Proxmox VE |
| Storage Type in Proxmox | Directory |
| Primary Purpose | Virtual machine backups |
| Additional Content | Installation media |
| Mount Configuration | Persistent filesystem UUID |

### Storage Design

The external drive is mounted on the Proxmox host and registered as a storage destination.

Proxmox uses this destination to store compressed virtual machine backup archives.

### Benefits

This design provides:

- Backup storage separate from primary VM disks
- Centralized backup management
- Capacity for multiple recovery points
- Support for automated retention
- Access to backup archives through Proxmox

### Limitations

The backup drive is physically connected to the hypervisor.

Therefore, it is not an independent off-site backup.

A host-level electrical issue, physical damage, theft, or other shared failure could affect both primary storage and the attached backup device.

The external drive also represents a single backup storage destination.

Additional independent backup storage would improve resilience.

---

## 4. Virtual Machine Snapshots

Proxmox supports virtual machine snapshots for creating rollback points.

Snapshots are particularly useful before significant configuration changes.

### HomeLAB Use Cases

Snapshots have been used before or during:

- Security tool configuration
- Detection rule development
- Honeypot deployment
- Firewall logging integration
- Windows SOC testing
- Application development

### Snapshot Benefits

Snapshots can provide a convenient way to return a virtual machine to an earlier state.

This is useful when testing configuration changes that may introduce instability.

### Snapshot Limitations

Snapshots are not a replacement for independent backups.

Snapshots typically depend on the underlying virtual machine storage.

If the primary storage fails, snapshots stored on that same storage may also be lost.

### Operational Practice

Snapshots are used as short-term recovery checkpoints.

Separate backup archives provide broader recovery capabilities.

---

## 5. Virtual Machine Backup Configuration

Proxmox provides integrated virtual machine backup functionality.

The HomeLAB uses a scheduled backup job to protect its active security workloads.

### Verified Backup Settings

| Setting | Configuration |
|---|---|
| Backup Method | Snapshot |
| Compression | Zstandard |
| Schedule | Daily |
| Execution Time | 3:00 AM Eastern |
| Backup Destination | External storage |
| Protected Workloads | Three active security VMs |
| Automatic Retention | Enabled |
| Missed Run Replay | Disabled |

### Snapshot Backup Mode

Snapshot backup mode allows Proxmox to back up running virtual machines without requiring a planned full shutdown.

The backup process captures the VM disk data while coordinating with the virtualization platform.

### Operational Considerations

Snapshot-mode backups can reduce downtime but may still introduce:

- Additional disk I/O
- Temporary storage activity
- Increased system load
- Backup-related performance impact

Backup timing should account for periods of high system utilization.

### Schedule Selection

The backup schedule was configured for an early-morning execution window.

This reduces the likelihood of backup activity interfering with evening HomeLAB administration, security testing, and development.

### Current Status

The scheduled backup job is enabled.

Manual backups have been completed successfully for all three active virtual machines.

The first automated execution under the updated schedule has not yet been independently verified.

---

## 6. Backup Compression

The HomeLAB uses Zstandard compression for virtual machine backup archives.

### Zstandard

Zstandard, commonly abbreviated as zstd, provides lossless compression.

It is used to reduce backup archive size while maintaining efficient compression and decompression performance.

### Benefits

- Reduced backup storage consumption
- Efficient compression performance
- Support for large virtual machine archives
- Integration with Proxmox backup functionality

### Operational Considerations

Compression ratios depend on the data stored inside each virtual machine.

Operating system files, application data, databases, and previously compressed files may compress differently.

---

## 7. Backup Retention Policy

The backup job uses a defined retention policy.

### Retention Configuration

| Retention Category | Policy |
|---|---|
| Daily | Keep 7 |
| Weekly | Keep 4 |
| Monthly | Keep 3 |

### Retention Objectives

The policy is designed to balance:

- Recovery point availability
- Storage consumption
- Historical backup coverage
- Operational simplicity

### Daily Backups

Recent daily backups provide recovery points for short-term incidents.

### Weekly Backups

Weekly recovery points provide additional historical coverage.

### Monthly Backups

Monthly recovery points provide longer-term recovery options.

### Important Considerations

Retention rules are applied by Proxmox according to its backup pruning logic.

The policy does not guarantee that every category will always contain its maximum number of backups.

Actual retained archives depend on backup history and successful job execution.

Retention settings should be periodically reviewed to ensure they meet recovery requirements.

---

## 8. Backup Validation

A successful backup job does not automatically guarantee that a virtual machine can be restored successfully.

Backup validation is an important part of recovery planning.

### Validation Levels

Backup validation can include:

1. Confirming the backup job completed successfully.
2. Verifying the archive exists on the destination.
3. Reviewing archive metadata.
4. Checking archive integrity.
5. Performing a controlled test restore.
6. Confirming the restored operating system boots.
7. Validating critical application functionality.

### Current Validation Status

The following activities have been completed:

- Confirmed external storage availability
- Verified Proxmox recognizes the backup destination
- Created current backups for all active VMs
- Confirmed the resulting archives are listed by Proxmox

### Outstanding Validation

The following activities remain planned:

- Full archive integrity testing
- Controlled virtual machine restore testing
- Application-level recovery verification
- Validation of automated backup execution

### Recovery Assurance

Backup archive visibility confirms that Proxmox recognizes the backup files.

It does not independently establish that the archives are free of corruption or that the protected applications can be successfully recovered.

---

## 9. Storage Failure Investigation

During backup infrastructure maintenance, an external storage issue was identified.

The external backup drive had previously disconnected unexpectedly.

This resulted in filesystem errors and prevented normal access to the backup destination.

### Symptoms

The investigation identified:

- Unexpected USB storage disconnection
- Filesystem journal errors
- An interrupted filesystem mount
- Backup storage becoming unavailable
- Stale references to the affected mount
- Storage access problems

### Investigation Objectives

The primary objectives were to:

1. Determine whether the storage device was still detected.
2. Identify the active block device.
3. Confirm the filesystem and mount state.
4. Identify processes holding references to the affected mount.
5. Inspect filesystem consistency.
6. Recover access without unnecessarily destroying existing backup data.

### Troubleshooting Tools

Linux command-line utilities were used to inspect the storage environment.

Examples included:

```bash
lsblk
```

```bash
findmnt
```

```bash
df -h
```

```bash
journalctl -k
```

```bash
dmesg
```

These tools provide information about block devices, mounted filesystems, available capacity, and kernel events.

---

## 10. Filesystem Recovery

The affected backup storage used the ext4 filesystem.

The investigation identified a filesystem journal problem following the unexpected device disconnection.

### Recovery Approach

A staged recovery process was used.

```text
            STORAGE FAILURE DETECTED
                       |
                       v
              IDENTIFY DEVICE
                       |
                       v
              REVIEW KERNEL LOGS
                       |
                       v
             RELEASE STALE MOUNTS
                       |
                       v
             READ-ONLY INSPECTION
                       |
                       v
              FILESYSTEM CHECK
                       |
                       v
             REPAIR FILESYSTEM
                       |
                       v
               REMOUNT STORAGE
                       |
                       v
              VERIFY BACKUP ACCESS
```

### Read-Only Inspection

The filesystem was initially inspected without making repair changes.

This reduced the risk of modifying the filesystem before understanding the problem.

### Filesystem Check

The Linux `e2fsck` utility was used to examine the ext4 filesystem.

An initial non-modifying check identified filesystem inconsistencies.

A subsequent repair operation recovered the filesystem journal and completed the filesystem verification passes.

### Recovery Results

Following repair:

- The external filesystem mounted successfully.
- Read/write access was restored.
- Proxmox recognized the backup storage destination.
- Existing backup archives were accessible through Proxmox.
- New virtual machine backups completed successfully.

### Root Cause Status

The unexpected USB disconnection was confirmed through system logs.

The underlying hardware cause was not conclusively established.

Potential contributing factors include:

- USB connection instability
- Cable problems
- Power interruptions
- External enclosure issues
- Storage hardware problems

No specific hardware component was conclusively identified as the cause.

---

## 11. Backup Inventory Cleanup

During recovery, the backup storage was found to contain a large number of historical archives belonging to a retired virtual machine.

### Problem

The retired VM had accumulated numerous backup archives.

These files consumed storage capacity without protecting an active workload.

### Remediation

The backup inventory was reviewed and cleaned up.

The process included:

1. Identifying backup archives associated with the retired VM.
2. Reviewing archive timestamps.
3. Preserving a selected historical recovery point.
4. Removing redundant older archives.
5. Verifying the remaining backup inventory.

### Results

The cleanup removed dozens of unnecessary backup archives.

A historical archive was retained for the retired VM.

Current backup archives were created for all active security workloads.

### Lessons

Backup retention must be reviewed when virtual machines are retired.

Deleting a VM does not necessarily remove its historical backup archives.

Unused backup data can continue consuming storage until it is explicitly reviewed and managed.

---

## 12. Backup Job Remediation

The backup configuration was reviewed after the storage recovery.

### Improvements Implemented

The backup job was updated to:

- Include all active security virtual machines
- Use snapshot backup mode
- Use Zstandard compression
- Execute during an early-morning maintenance window
- Apply daily, weekly, and monthly retention rules
- Avoid replaying missed backup executions

### Operational Outcome

The backup job configuration was successfully updated and verified.

Manual backups confirmed that the recovered storage destination could accept new archives.

### Outstanding Verification

A successful automated execution under the updated schedule remains to be confirmed.

---

## 13. Backup Notifications

Backup notifications provide visibility into successful jobs and failures.

### Notification Objectives

Notifications should help identify:

- Failed backup jobs
- Storage destination problems
- Backup task errors
- Other conditions requiring administrator attention

### Current Implementation

The Proxmox notification system was reviewed during backup remediation.

An SMTP notification endpoint was configured.

### Current Status

Notification delivery is not yet operational.

Testing encountered an external SMTP authentication error.

The issue remains unresolved.

### Planned Improvements

Future work includes:

1. Resolve SMTP authentication.
2. Verify successful test notification delivery.
3. Configure the appropriate notification routing.
4. Validate notifications for failed backup jobs.
5. Confirm that important backup failures are reported.

### Operational Risk

Until notification delivery is validated, backup failures may require manual inspection through the Proxmox interface or task logs.

---

## 14. Recovery Procedures

The HomeLAB uses a structured approach to recovery.

### General Recovery Workflow

```text
                INCIDENT IDENTIFIED
                         |
                         v
                 ASSESS IMPACT
                         |
                         v
                IDENTIFY WORKLOAD
                         |
                         v
              REVIEW RECOVERY OPTIONS
                         |
                +--------+--------+
                |                 |
                v                 v
           VM SNAPSHOT       VM BACKUP
                |                 |
                v                 v
            ROLLBACK          RESTORE
                |                 |
                +--------+--------+
                         |
                         v
                 VALIDATE SYSTEM
                         |
                         v
                VERIFY APPLICATIONS
                         |
                         v
                 DOCUMENT RESULTS
```

### Recovery Option Selection

The appropriate recovery method depends on the failure.

Examples include:

| Failure Scenario | Potential Recovery Method |
|---|---|
| Unsuccessful configuration change | Snapshot rollback |
| Corrupted virtual machine | Restore from backup |
| Deleted virtual machine | Restore from backup |
| Application misconfiguration | Configuration repair or rollback |
| Backup filesystem error | Storage investigation and recovery |
| Primary VM storage failure | Restore from independent backup storage |

### Recovery Validation

After recovery, the administrator should verify:

- Operating system boot
- Network connectivity
- Application availability
- Service status
- Security telemetry
- Relevant configuration settings

Recovery is not complete until the restored workload performs its intended function.

---

## 15. Configuration Backups

Virtual machine backups protect guest disks and included VM configuration data.

However, some infrastructure configuration exists outside those virtual machines.

### Infrastructure Components

Additional configuration protection may be required for:

- FortiGate firewall
- Ubiquiti switch and wireless infrastructure
- Proxmox host
- Network management applications
- Security monitoring configuration

### Configuration Backup Considerations

Important configuration data may include:

- VLAN definitions
- Firewall policies
- Routing settings
- Device management configuration
- Hypervisor networking
- Storage configuration
- Security application settings

### Current Status

Configuration backups and documentation have been used during infrastructure changes.

A fully automated and independently verified configuration backup process for every infrastructure component has not been established.

### Planned Improvements

Future work will focus on improving configuration backup consistency and recovery documentation.

Sensitive configuration exports will remain outside the public repository.

---

## 16. Security Considerations

Backup infrastructure contains potentially sensitive information.

Virtual machine archives may include:

- Operating system data
- User accounts
- Application configuration
- Security logs
- Authentication material
- Internal network information

### Backup Security Objectives

Backup storage should be protected against:

- Unauthorized access
- Accidental deletion
- Data corruption
- Hardware failure
- Ransomware
- Physical damage

### Recommended Controls

Potential improvements include:

- Restricted administrative access
- Independent backup destinations
- Encrypted backup storage
- Off-site backup copies
- Backup integrity validation
- Periodic restore testing
- Storage health monitoring

### Current Limitations

The primary backup destination is directly attached to the Proxmox host.

This means the backup environment does not currently provide the same separation as an independent backup server or off-site storage system.

---

## 17. Design Decisions

### Dedicated Backup Storage

A separate external drive provides backup capacity independent of the primary virtual machine disks.

### Snapshot-Mode Backups

Snapshot mode reduces the need for planned VM shutdowns during backup operations.

### Zstandard Compression

Compression helps reduce storage consumption while maintaining practical backup performance.

### Automated Scheduling

Scheduled backups reduce dependence on manual backup execution.

### Defined Retention

Retention rules balance recent recovery points with longer-term historical coverage.

### Manual Recovery Checkpoints

Snapshots provide convenient rollback options during security infrastructure development.

### Staged Filesystem Recovery

Read-only inspection before repair reduces the risk of unnecessary changes during storage troubleshooting.

### Public Documentation Sanitization

Sensitive operational details are excluded from the public repository.

---

## 18. Lessons Learned

Building and maintaining the backup environment provided practical experience with:

- Proxmox backup administration
- Virtual machine snapshots
- Backup scheduling
- Retention policies
- Zstandard compression
- Linux filesystem management
- USB storage troubleshooting
- ext4 filesystem recovery
- Backup inventory management
- Infrastructure recovery planning

### Key Technical Lessons

**Backups require ongoing verification.**

A configured backup job does not guarantee that successful backups are being created.

**Backup archives are only one part of recovery.**

Restore testing is required to establish confidence in recoverability.

**Storage availability can change unexpectedly.**

External storage may disconnect or encounter filesystem errors without a planned maintenance event.

**Filesystem recovery should be deliberate.**

Inspecting filesystem state before making repairs helps reduce unnecessary risk.

**Retired infrastructure can leave behind operational data.**

Historical backup archives should be reviewed when virtual machines are removed.

**Retention policies require maintenance.**

Without appropriate pruning, backup storage can accumulate unnecessary archives.

**Notifications are part of backup reliability.**

Automated alerting helps administrators identify failures that might otherwise go unnoticed.

**Recovery procedures should be documented before an incident.**

A documented recovery workflow reduces uncertainty during troubleshooting.

---

## 19. Planned Improvements

The following improvements are planned:

### High Priority

- Validate the first automated backup execution
- Resolve backup notification delivery
- Perform controlled VM restore testing
- Implement backup integrity checks
- Improve external storage health monitoring

### Medium Priority

- Review configuration backup procedures
- Evaluate an additional independent backup destination
- Improve backup failure monitoring
- Review backup storage permissions
- Establish documented recovery objectives

### Long-Term Improvements

- Evaluate network-attached backup storage
- Consider off-site backup copies
- Investigate backup encryption options
- Improve automated backup verification
- Develop repeatable disaster recovery exercises

---

## 20. Summary

The CyberSecurity HomeLAB uses Proxmox VE to provide backup and recovery capabilities for its virtualized security infrastructure.

Three active virtual machines are protected through a scheduled backup job using snapshot mode, Zstandard compression, and defined retention policies.

Backup archives are stored on dedicated external storage.

During infrastructure maintenance, an unexpected external storage disconnection was investigated and the affected ext4 filesystem was successfully repaired.

The recovery process restored access to existing backup archives and allowed new backups to complete successfully.

The backup inventory was also cleaned up, and the scheduled backup configuration was updated to protect all active workloads.

Automated backup execution, notification delivery, archive integrity testing, and controlled restore validation remain outstanding operational improvements.

The project provides practical experience with backup administration, Linux storage troubleshooting, filesystem recovery, infrastructure maintenance, and disaster recovery planning.

This document intentionally excludes sensitive operational details to support safe publication in a public cybersecurity portfolio.
