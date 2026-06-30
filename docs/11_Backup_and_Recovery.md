# Backup and Recovery

## Purpose

This document describes the backup and recovery strategy used throughout the CyberSecurity-HomeLab environment.

A structured backup process helps minimize downtime, reduces deployment risk, and provides recovery options following hardware failure, configuration errors, or unsuccessful infrastructure changes.

---

# Backup Strategy

The CyberSecurity-HomeLab uses multiple layers of protection.

These include:

- Proxmox VM snapshots
- Full VM backups
- External backup storage
- Configuration backups
- Recovery validation

---

# Virtual Machine Protection

Prior to major infrastructure changes, each virtual machine receives:

- A Proxmox snapshot
- A full Proxmox backup

This allows rapid rollback in the event of an unsuccessful deployment while also protecting against storage failure.

---

# Backup Storage

Virtual machine backups are stored on a dedicated external 5 TB USB hard drive connected to the Proxmox host.

This storage is isolated from the primary virtualization storage to provide an additional layer of protection.

---

# Configuration Backups

Configuration backups are maintained for infrastructure components including:

- FortiGate firewall
- Proxmox VE
- Ubuntu Server
- Ubiquiti network configuration

Configuration backups are created before significant infrastructure changes whenever practical.

---

# Recovery Process

The general recovery workflow is:

1. Identify the failed system or configuration.
2. Determine whether a snapshot rollback or full restore is appropriate.
3. Restore the affected virtual machine or configuration.
4. Validate system functionality.
5. Resume normal operations.

---

# Design Decisions

Snapshots were selected for rapid rollback during infrastructure changes.

Full backups provide protection against hardware failure, corruption, or accidental deletion.

Using both methods provides flexibility while reducing operational risk.

---

# Lessons Learned

Creating snapshots and backups before the FortiGate production cutover provided confidence during deployment.

Knowing a complete recovery path existed allowed infrastructure changes to be completed with significantly less operational risk.

---

# Future Improvements

Planned enhancements include:

- Scheduled automated backups
- Backup integrity verification
- Off-site backup replication
- Backup retention policies
- Disaster recovery testing
