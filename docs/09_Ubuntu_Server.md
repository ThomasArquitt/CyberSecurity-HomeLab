# 09 — Linux Server Administration & Security

## 1. Overview

Ubuntu Server provides the Linux operating system foundation for several critical components of the CyberSecurity HomeLAB.

The environment uses Linux-based virtual machines to support security monitoring, honeypot operations, log collection, and cybersecurity research.

Linux administration is performed primarily through SSH and command-line utilities.

The implementation provides practical experience with:

- Linux server administration
- Remote management using SSH
- Operating system maintenance
- Package management
- System service administration
- Log analysis and troubleshooting
- Network configuration
- File and directory permissions
- Security infrastructure deployment
- Virtual machine administration
- Backup and recovery

> **Security Note:** This document describes the Linux administration architecture and operational practices used in the HomeLAB. Sensitive management details, credentials, authentication material, and internal service configurations are intentionally excluded.

---

## 2. Linux Infrastructure

The HomeLAB currently operates two Ubuntu-based virtual machines.

### Active Linux Servers

| Server | Operating System | Primary Purpose |
|---|---|---|
| DSHIELD-01 | Ubuntu 26.04 LTS | Honeypot and attack telemetry |
| WAZUH-01 | Ubuntu 26.04 LTS | Security monitoring and SIEM |

Both systems run Ubuntu 26.04 LTS, codenamed Resolute Raccoon.

WAZUH-01 has been updated to the 26.04.1 LTS point release.

### Infrastructure Architecture

```text
                  PROXMOX HYPERVISOR
                          |
              +-----------+-----------+
              |                       |
              v                       v
         DSHIELD-01               WAZUH-01
         Ubuntu Server            Ubuntu Server
              |                       |
              v                       v
         Honeypot Services        Wazuh SIEM
              |                       ^
              |                       |
              +---- Security Logs ----+
```

Both virtual machines operate within the dedicated security network.

The FortiGate firewall controls communication between this network and other HomeLAB segments.

### Infrastructure Changes

An earlier Ubuntu management virtual machine was retired during infrastructure consolidation.

Its removal reduced unnecessary resource consumption and eliminated an unused server from the active environment.

Linux administration activities are now performed directly on the remaining operational systems.

---

## 3. Virtual Machine Configuration

The Linux servers are hosted on Proxmox Virtual Environment.

### DSHIELD-01

| Resource | Configuration |
|---|---|
| Operating System | Ubuntu 26.04 LTS |
| Virtual CPUs | 2 |
| Memory | 4 GB |
| Virtual Disk | 40 GB |
| Firmware | UEFI |
| Network | Dedicated Security VLAN |
| QEMU Guest Agent | Enabled |

DShield provides a controlled environment for observing unsolicited network activity.

### WAZUH-01

| Resource | Configuration |
|---|---|
| Operating System | Ubuntu 26.04.1 LTS |
| Virtual CPUs | 4 |
| Memory | 8 GB |
| Virtual Disk | 80 GB |
| Network | Dedicated Security VLAN |
| QEMU Guest Agent | Enabled |

WAZUH-01 hosts the centralized security monitoring infrastructure.

### Virtualization Benefits

Hosting Linux services on Proxmox provides:

- Centralized resource management
- Virtual machine isolation
- Backup capabilities
- Snapshot support
- Flexible resource allocation
- Remote administration
- Simplified infrastructure recovery

---

## 4. Remote Administration Using SSH

Secure Shell (SSH) provides the primary remote administration method for the Linux servers.

### SSH Administration

SSH allows administrators to:

- Execute commands remotely
- Manage system services
- Review log files
- Transfer files
- Modify configuration files
- Install software
- Troubleshoot operating system issues

### Example Connection

```bash
ssh administrator@linux-server
```

The example uses a placeholder hostname and username.

Actual management addresses and account details are excluded from public documentation.

### Security Considerations

SSH access should be protected through:

- Restricted administrative access
- Strong authentication
- Appropriate account permissions
- Network segmentation
- Firewall policies
- Regular security updates

Administrative access to the Linux security infrastructure is controlled through the HomeLAB's network security architecture.

---

## 5. Linux Package Management

Ubuntu uses the Advanced Package Tool (APT) for software package management.

APT provides a consistent method for installing, updating, and removing software.

### Refresh Package Information

```bash
sudo apt update
```

### Upgrade Installed Packages

```bash
sudo apt upgrade
```

### Install a Package

```bash
sudo apt install <package-name>
```

### Remove a Package

```bash
sudo apt remove <package-name>
```

### Review Upgradable Packages

```bash
apt list --upgradable
```

### Operational Considerations

Package management is used to maintain:

- Operating system components
- Security updates
- System utilities
- Application dependencies
- Monitoring tools

Updates should be evaluated for compatibility with running security services.

For significant changes, an appropriate VM backup or snapshot can provide a recovery option.

---

## 6. System Service Management

Ubuntu uses systemd to manage system services.

The `systemctl` utility provides control over service execution and startup behavior.

### Check Service Status

```bash
sudo systemctl status <service-name>
```

### Start a Service

```bash
sudo systemctl start <service-name>
```

### Stop a Service

```bash
sudo systemctl stop <service-name>
```

### Restart a Service

```bash
sudo systemctl restart <service-name>
```

### Enable a Service at Boot

```bash
sudo systemctl enable <service-name>
```

### Check Whether a Service Is Enabled

```bash
systemctl is-enabled <service-name>
```

### Practical HomeLAB Applications

System service administration has been used while working with:

- Wazuh services
- Honeypot components
- SSH services
- Security monitoring infrastructure

Understanding systemd is important for maintaining application availability and troubleshooting service failures.

---

## 7. Log Analysis and Troubleshooting

Linux system logs provide visibility into operating system and application behavior.

The HomeLAB uses command-line log analysis to investigate service issues and validate security operations.

### journalctl

The `journalctl` utility displays logs collected by systemd-journald.

### View Recent Logs

```bash
sudo journalctl -n 100
```

### Follow Logs in Real Time

```bash
sudo journalctl -f
```

### View Logs for a Specific Service

```bash
sudo journalctl -u <service-name>
```

### Follow a Specific Service

```bash
sudo journalctl -u <service-name> -f
```

### View Logs from the Current Boot

```bash
sudo journalctl -b
```

### Practical Applications

Log analysis has supported:

- Honeypot activity monitoring
- Service startup troubleshooting
- Application error investigation
- Security event verification
- Operating system diagnostics

### Operational Value

Real-time log monitoring helps determine whether:

- Services are running correctly
- Applications are generating expected events
- Configuration changes are successful
- Errors require investigation

---

## 8. Linux Networking

The Ubuntu servers use network interfaces connected to the Proxmox virtual networking infrastructure.

### Network Architecture

```text
                 UBUNTU SERVER
                       |
                       v
               VIRTUAL NETWORK ADAPTER
                       |
                       v
                 PROXMOX BRIDGE
                       |
                       v
                  SECURITY VLAN
                       |
                       v
                 FORTIGATE FIREWALL
```

The FortiGate provides the gateway and firewall enforcement for the security network.

### Common Networking Commands

Display IP addresses:

```bash
ip addr
```

Display routing information:

```bash
ip route
```

Test connectivity:

```bash
ping <destination>
```

Display listening TCP and UDP sockets:

```bash
sudo ss -tuln
```

Test DNS resolution:

```bash
getent hosts <hostname>
```

### Troubleshooting Process

When investigating Linux connectivity issues:

1. Verify the network interface is active.
2. Confirm the assigned IP address.
3. Review the routing table.
4. Test connectivity to the local gateway.
5. Verify DNS resolution.
6. Review applicable firewall policies.
7. Check whether the destination service is listening.

This process helps isolate network configuration problems from application failures.

---

## 9. File and Directory Management

Linux administration requires familiarity with the filesystem hierarchy and command-line file operations.

### Common Commands

Display the current directory:

```bash
pwd
```

List directory contents:

```bash
ls -lah
```

Change directories:

```bash
cd /path/to/directory
```

Create a directory:

```bash
mkdir <directory-name>
```

Copy a file:

```bash
cp <source> <destination>
```

Move a file:

```bash
mv <source> <destination>
```

Display file contents:

```bash
cat <filename>
```

Search file contents:

```bash
grep "search-term" <filename>
```

### Practical Applications

Filesystem administration has been used for:

- Reviewing application logs
- Managing configuration files
- Organizing security telemetry
- Preparing log archives
- Troubleshooting service configuration
- Maintaining security applications

---

## 10. Linux Permissions and Administrative Privileges

Linux uses ownership and permission controls to restrict access to files and directories.

### Permission Categories

| Category | Description |
|---|---|
| Owner | User that owns the file |
| Group | Group associated with the file |
| Others | All other users |
| Read | Permission to view file contents |
| Write | Permission to modify a file |
| Execute | Permission to execute a file or traverse a directory |

### Inspect Permissions

```bash
ls -l
```

### Change Ownership

```bash
sudo chown <user>:<group> <filename>
```

### Change Permissions

```bash
sudo chmod <mode> <filename>
```

### Administrative Privileges

The `sudo` command allows authorized users to execute commands with elevated privileges.

Example:

```bash
sudo systemctl status ssh
```

### Security Principles

Linux administrative access should follow the principle of least privilege.

Important practices include:

- Avoiding unnecessary root sessions
- Restricting administrative accounts
- Applying appropriate file permissions
- Protecting sensitive configuration files
- Reviewing elevated command execution

---

## 11. Security Infrastructure Integration

Ubuntu supports two important cybersecurity workloads in the HomeLAB.

### DShield/Cowrie Honeypot

The DShield environment collects telemetry associated with unsolicited internet activity.

Its Linux foundation supports:

- Honeypot service execution
- Log collection
- Remote administration
- Application troubleshooting
- Security event forwarding

The honeypot is isolated from trusted internal networks.

### Wazuh SIEM

The Wazuh environment provides centralized security monitoring.

Its Linux foundation supports:

- Wazuh service management
- Log ingestion
- Event processing
- Detection rule configuration
- Security event investigation
- Dashboard access

### Security Telemetry Flow

```text
              SECURITY DATA SOURCES
                       |
           +-----------+-----------+
           |           |           |
        Windows      Honeypot    Firewall
        Endpoint      Events      Logs
           |           |           |
           +-----------+-----------+
                       |
                       v
                   WAZUH SIEM
                       |
                       v
                SECURITY ANALYSIS
```

These integrations provide hands-on experience administering Linux systems that support security operations.

---

## 12. Backup and Recovery

The Linux virtual machines are included in the Proxmox backup strategy.

### Backup Architecture

```text
                PROXMOX HYPERVISOR
                        |
              +---------+---------+
              |                   |
          DSHIELD-01           WAZUH-01
              |                   |
              +---------+---------+
                        |
                        v
                 VM BACKUP PROCESS
                        |
                        v
                EXTERNAL STORAGE
```

### Backup Capabilities

The environment uses:

- Proxmox VM backups
- Compressed backup archives
- Scheduled backup execution
- Defined retention policies
- VM snapshots for selected configuration changes

### Recovery Considerations

VM snapshots provide convenient rollback points for certain configuration changes.

Full VM backup archives provide a separate recovery mechanism.

Backup archives should be periodically validated through integrity checks and controlled restore testing.

### Current Status

Backup archives have been created for both active Linux servers.

Automated backup scheduling is configured.

End-to-end restore testing remains a planned improvement.

---

## 13. Security Practices

Linux security administration is an important component of the HomeLAB.

### Network Isolation

Linux security workloads reside in a dedicated VLAN.

Firewall policies restrict unnecessary communication with other internal networks.

### Remote Access

Administrative access is performed using SSH.

### Software Maintenance

APT provides operating system and package update capabilities.

### Service Management

systemd allows administrators to inspect and manage running services.

### Log Monitoring

System and application logs provide information for troubleshooting and security analysis.

### Backup Protection

Proxmox backups and snapshots provide recovery options for Linux workloads.

### Configuration Management

Configuration changes should be documented and validated.

Sensitive information should not be committed to public repositories.

---

## 14. Operational Troubleshooting

Linux troubleshooting requires a structured approach.

### Example Investigation Workflow

When a service stops functioning:

1. Check the service status.
2. Review recent journal entries.
3. Verify relevant configuration files.
4. Confirm required network connectivity.
5. Check listening ports.
6. Review file permissions.
7. Restart the service if appropriate.
8. Validate that the service has recovered.

### Example Commands

```bash
sudo systemctl status <service-name>
```

```bash
sudo journalctl -u <service-name> -n 100
```

```bash
sudo ss -tuln
```

```bash
ip route
```

### Practical Experience

This workflow has been applied while troubleshooting HomeLAB security services and infrastructure.

The objective is to identify the underlying cause of an issue rather than relying solely on service restarts.

---

## 15. Design Decisions

### Ubuntu Server

Ubuntu provides a stable Linux platform with broad compatibility across security tools.

### Virtualized Deployment

Proxmox allows Linux services to run as isolated virtual machines.

### SSH Administration

Remote command-line administration reduces dependence on graphical interfaces.

### Dedicated Security Network

Linux security workloads are separated from trusted personal and work devices.

### Centralized Monitoring

Wazuh provides a platform for collecting and analyzing security events.

### Backup Integration

Linux servers are included in the broader Proxmox backup strategy.

### Infrastructure Consolidation

Unused infrastructure is retired when it no longer serves an operational purpose.

---

## 16. Lessons Learned

Administering Ubuntu servers in the HomeLAB has provided practical experience with:

- Linux command-line administration
- SSH connectivity
- Package management
- systemd service management
- journalctl log analysis
- Network troubleshooting
- Filesystem navigation
- File permissions
- Security application deployment
- Virtualization
- Backup administration
- Infrastructure maintenance

### Key Technical Lessons

**Linux administration is essential for security engineering.**

Many security platforms rely on Linux-based infrastructure.

**Logs are a primary troubleshooting resource.**

Service logs frequently reveal configuration problems and application errors.

**Network troubleshooting requires multiple layers of verification.**

An application may appear unavailable because of routing, firewall, DNS, or service configuration issues.

**Service availability requires ongoing monitoring.**

A service that starts successfully may still encounter problems later.

**Backups and snapshots are complementary.**

Snapshots support rollback, while separate backup archives support broader recovery scenarios.

**Removing unused infrastructure reduces complexity.**

Retiring unnecessary virtual machines frees resources and reduces maintenance requirements.

---

## 17. Planned Improvements

Future Linux infrastructure improvements include:

- Continue improving Linux administration skills
- Expand shell scripting and automation
- Review SSH security configurations
- Improve service monitoring
- Perform controlled backup restore testing
- Evaluate lightweight Linux containers
- Explore infrastructure automation
- Continue Wazuh detection engineering
- Improve configuration documentation
- Maintain operating system and security updates

---

## 18. Summary

Ubuntu Server provides the Linux operating system foundation for the HomeLAB's honeypot and SIEM infrastructure.

The environment currently operates two Ubuntu-based virtual machines supporting security monitoring and attack telemetry collection.

Administration is performed primarily through SSH and Linux command-line utilities.

The HomeLAB provides hands-on experience with Linux networking, service management, package administration, log analysis, security tooling, and backup operations.

The infrastructure also demonstrates the ability to maintain, troubleshoot, and consolidate Linux systems as operational requirements change.

This document intentionally excludes sensitive operational details to support safe publication in a public cybersecurity portfolio.
