# Project Overview

## Purpose

The CyberSecurity-HomeLab project is an enterprise-inspired home laboratory designed to develop practical experience in IT infrastructure, networking, virtualization, system administration, cybersecurity operations, and security engineering.

The primary objective is to bridge the gap between theoretical cybersecurity knowledge and hands-on technical experience by designing, deploying, securing, monitoring, and maintaining a segmented network environment.

The laboratory provides a practical environment for developing skills in:

- Security Operations Center (SOC) analysis
- Security monitoring and incident investigation
- Detection engineering and security event correlation
- Endpoint security and telemetry analysis
- Network security and firewall administration
- Vulnerability management
- Adversary simulation and detection validation
- Infrastructure automation and system administration

All infrastructure is self-managed, with configurations, troubleshooting procedures, architectural decisions, and lessons learned documented throughout the project.

The long-term objective is to develop an enterprise-style security environment capable of supporting realistic SOC investigations, security engineering workflows, and controlled adversary simulations.

---

## Project Objectives

### Infrastructure and Networking

- Build and administer a virtualized infrastructure using Proxmox VE.
- Design and implement segmented networks using VLANs.
- Configure and administer a FortiGate next-generation firewall.
- Configure and manage Ubiquiti switching and wireless infrastructure.
- Deploy and maintain Linux and Windows virtual machines.
- Implement firewall policies, network address translation, and access controls.
- Establish reliable backup, recovery, and virtualization snapshot procedures.

### Security Operations

- Deploy and administer a centralized SIEM platform using Wazuh.
- Collect and analyze security telemetry from Windows endpoints, firewalls, and honeypots.
- Develop and tune custom security detection rules.
- Investigate security alerts using event logs and SIEM queries.
- Analyze suspicious processes, authentication activity, and network connections.
- Reduce false positives and improve detection accuracy.
- Map observed activity to the MITRE ATT&CK framework.

### Security Testing and Automation

- Deploy and operate a DShield/Cowrie SSH honeypot.
- Simulate adversary techniques using Atomic Red Team.
- Develop custom security testing applications using Python and FastAPI.
- Generate controlled security events for detection validation.
- Develop automated security investigation and testing workflows.
- Expand automation capabilities using Python and PowerShell.

### Future Infrastructure Development

- Deploy Windows Server and Active Directory Domain Services.
- Implement centralized identity and domain management.
- Domain-join the Windows 11 SOC endpoint.
- Expand security monitoring to include Active Directory authentication events.
- Develop additional detection rules for identity-based attacks.
- Deploy lightweight Linux containers using Proxmox LXC.
- Develop an AI-assisted cybersecurity job discovery platform.

---

## Technologies

### Virtualization and Infrastructure

- Proxmox Virtual Environment (VE)
- Virtual Machines
- Virtual Machine Snapshots
- Backup and Recovery
- Proxmox LXC Containers (Planned)

### Networking

- FortiGate 90D Firewall
- Ubiquiti Switch Lite 8 PoE
- Ubiquiti Wireless Access Points
- VLAN Segmentation
- DHCP
- DNS
- TCP/IP
- Static IP Addressing
- Network Address Translation (NAT)
- Firewall Policies
- Network Access Controls

### Operating Systems

- Ubuntu Server
- Windows 11
- Windows Server (Planned)

### Security Monitoring and Endpoint Telemetry

- Wazuh SIEM
- Wazuh Agents
- Sysmon
- Windows Event Logs
- Windows Defender
- PowerShell Logging
- FortiGate Syslog
- DShield/Cowrie Honeypot

### Detection Engineering

- Custom Wazuh Detection Rules
- Security Event Correlation
- Alert Investigation
- Detection Rule Tuning
- False Positive Reduction
- MITRE ATT&CK Framework
- Detection Validation
- Dashboard Query Language (DQL)

### Security Testing and Development

- Atomic Red Team
- AtomicSOC
- Python
- FastAPI
- PowerShell
- REST APIs
- OpenSSH

### Planned Automation and Containerization

- Proxmox LXC Containers
- Python-Based Web Scraping
- Job Board API Integration
- AI-Assisted Job Classification
- Automated Job Filtering
- Duplicate Detection
- Scheduled Automation
- Notification Systems

### Documentation and Version Control

- Git
- GitHub
- Markdown
- Draw.io
- Infrastructure Diagrams
- Technical Documentation

---

## Current Infrastructure

### Physical Network Infrastructure

The laboratory operates within a segmented home network utilizing a FortiGate 90D firewall for routing, security policy enforcement, and network access control.

A Ubiquiti Switch Lite 8 PoE provides managed switching and connectivity for network infrastructure and wireless access points.

The environment includes multiple VLANs designed to separate network traffic based on device purpose and security requirements.

The primary virtualization platform is a dedicated Proxmox VE host running virtual machines that support infrastructure administration, security monitoring, endpoint testing, and adversary simulation.

A dedicated physical server also supports a Palworld game server environment used for additional networking, firewall, and endpoint monitoring experience.

### Virtual Infrastructure

#### Wazuh SIEM Server

An Ubuntu Server virtual machine hosts Wazuh for centralized security monitoring and event analysis.

The SIEM collects telemetry from multiple security sources, including:

- Windows 11 SOC endpoint
- FortiGate firewall
- DShield/Cowrie honeypot

Wazuh provides a centralized platform for:

- Security event collection
- Alert investigation
- Detection rule development
- Event correlation
- Security monitoring
- Detection validation

Custom detection rules have been implemented and tuned to improve alert quality and reduce unnecessary alert noise.

#### Windows 11 SOC Endpoint — WIN11-SOC-01

A dedicated Windows 11 virtual machine serves as a monitored endpoint for security testing, event generation, and investigation.

Configured security components include:

- Sysmon
- Windows Defender
- PowerShell Logging
- Wazuh Agent
- OpenSSH Server

The endpoint is used to generate and investigate Windows security events, process execution activity, and controlled adversary simulations.

The system also hosts the AtomicSOC security testing application.

#### DShield/Cowrie Honeypot

A DShield/Cowrie SSH honeypot collects unauthorized connection attempts and attacker activity.

The honeypot provides opportunities to analyze:

- SSH connection attempts
- Credential guessing
- Unauthorized authentication activity
- Attacker command execution
- Automated attack behavior
- Suspicious network activity

Collected telemetry is integrated into Wazuh for centralized monitoring and custom detection development.

The honeypot provides a source of real-world attack telemetry for security analysis and investigation.

#### AtomicSOC Security Testing Platform

AtomicSOC is a custom security testing application developed using Python and FastAPI.

The application provides a web interface for executing selected Atomic Red Team tests against the Windows 11 SOC endpoint.

Its primary purpose is to generate controlled security events that can be analyzed through Windows telemetry and Wazuh.

Implemented functionality includes:

- Web-based test execution
- Python FastAPI backend
- Atomic Red Team integration
- MITRE ATT&CK technique mapping
- Randomized test selection
- Controlled execution of security tests

Successfully executed techniques include:

| MITRE ATT&CK ID | Technique | Test Activity |
|----------------|-----------|---------------|
| T1057 | Process Discovery | Process enumeration |
| T1070.004 | File Deletion | File deletion activity |
| T1016 | System Network Configuration Discovery | Network configuration enumeration |

AtomicSOC provides a practical platform for studying endpoint telemetry, adversary behavior, and detection engineering.

#### Palworld Dedicated Server

A dedicated physical system hosts a Palworld game server.

The server environment provides additional experience with:

- Windows server administration
- SteamCMD
- Dedicated server deployment
- Network segmentation
- DHCP reservations
- FortiGate firewall policies
- UDP networking
- Windows Scheduled Tasks
- REST API configuration
- Wazuh endpoint monitoring

The server is integrated into the broader laboratory environment while remaining logically separated through network segmentation.

---

## Current Project Status

### Completed

#### Network Infrastructure

- Proxmox VE installed and configured.
- FortiGate firewall deployed and configured.
- Ubiquiti managed switch configured.
- VLAN segmentation implemented.
- Wireless access points configured.
- Firewall policies implemented.
- Network address translation configured.
- DHCP reservations implemented.
- Dedicated Palworld server deployed.

#### Virtual Infrastructure

- Ubuntu Server virtual machines deployed.
- Windows 11 SOC virtual machine deployed.
- Wazuh SIEM server deployed.
- DShield/Cowrie honeypot deployed.
- Virtual machine snapshots configured.
- Backup storage configured.

#### Security Monitoring

- Wazuh SIEM deployed and operational.
- Windows 11 endpoint registered with Wazuh.
- FortiGate firewall logs forwarded to Wazuh.
- Honeypot telemetry integrated into Wazuh.
- Windows endpoint telemetry configured.
- Sysmon installed and configured.
- PowerShell logging configured.
- Windows Defender telemetry enabled.
- Palworld server Wazuh agent installed.

#### Detection Engineering

- Custom Wazuh detection rules developed.
- Honeypot detection rules tuned.
- Alert severity and rule behavior investigated.
- False-positive and alert-noise analysis performed.
- Security events investigated using Wazuh.
- Detection configuration baselines snapshotted.

#### Security Testing

- Atomic Red Team testing environment established.
- AtomicSOC web application developed.
- Multiple MITRE ATT&CK techniques executed successfully.
- Randomized security test selection implemented.
- Controlled security event generation established.

### In Progress

- Expanding security detection coverage.
- Improving detection validation workflows.
- Investigating additional security telemetry sources.
- Refining custom Wazuh detection rules.
- Documenting Wazuh architecture and configurations.
- Documenting Windows 11 SOC endpoint configurations.
- Documenting AtomicSOC architecture and functionality.
- Updating network and security architecture diagrams.
- Expanding technical documentation for the GitHub repository.

---

## Planned Projects

### 1. Windows Server and Active Directory

Deploy a Windows Server virtual machine to introduce centralized identity management into the laboratory.

Planned implementation:

- Install and configure Windows Server.
- Deploy Active Directory Domain Services (AD DS).
- Configure Active Directory-integrated DNS.
- Establish a dedicated laboratory domain.
- Create organizational units, users, and security groups.
- Domain-join WIN11-SOC-01.
- Configure Group Policy Objects (GPOs).
- Implement security auditing policies.
- Forward relevant Windows security events to Wazuh.
- Develop detection rules for suspicious authentication activity.

The objective is to expand the laboratory into identity security monitoring and Active Directory attack detection.

### 2. Active Directory Detection Engineering

Expand Wazuh detection capabilities to monitor identity-based threats.

Planned detection scenarios include:

- Failed authentication attempts
- Password spraying
- Account enumeration
- Privileged group membership changes
- Suspicious account creation
- Unauthorized privilege escalation
- Abnormal authentication patterns
- Suspicious PowerShell activity

Controlled security testing will be used to validate detection coverage and improve investigation workflows.

### 3. AI-Powered SOC Job Discovery Platform

Develop a lightweight, automated cybersecurity job discovery application.

The platform will focus on identifying relevant employment opportunities for SOC Analysts, Junior Security Engineers, and related cybersecurity positions.

Planned implementation:

- Deploy a lightweight Linux container using Proxmox LXC.
- Develop a Python-based job aggregation application.
- Integrate supported job board APIs and permitted web scraping.
- Collect cybersecurity job postings from multiple sources.
- Filter opportunities by job title, experience requirements, location, and technical qualifications.
- Use AI-assisted analysis to classify relevant job descriptions.
- Implement duplicate detection and job tracking.
- Schedule automated searches for newly posted positions.
- Develop notifications for newly identified opportunities.
- Store collected job data for future analysis.

The project will provide additional experience with:

- Python automation
- API integration
- Data processing
- Containerization
- Scheduled tasks
- AI-assisted analysis
- Application development

The application may be maintained in a separate GitHub repository while its infrastructure deployment is documented within CyberSecurity-HomeLab.

### 4. Expanded Security Automation

Continue developing automation capabilities within the laboratory.

Potential improvements include:

- Automated detection testing
- Additional AtomicSOC test scenarios
- Security event enrichment
- Automated log analysis
- Python-based investigation utilities
- PowerShell security administration scripts
- Additional MITRE ATT&CK technique coverage

---

## Security Operations Focus

The laboratory is designed to support practical SOC analyst and security engineering workflows.

### Security Monitoring

Collect and analyze telemetry from endpoints, firewalls, and honeypots to identify suspicious behavior.

### Alert Investigation

Investigate security alerts by examining:

- Event timestamps
- Source and destination systems
- User and service accounts
- Process execution
- Parent and child processes
- Network connections
- Associated security events

### Detection Engineering

Develop, test, and refine security detection rules using controlled event generation and observed telemetry.

### Threat Analysis

Map suspicious behavior and simulated adversary activity to the MITRE ATT&CK framework.

### Detection Validation

Execute controlled security tests and evaluate the resulting telemetry to determine whether security monitoring and detection capabilities function as expected.

### Continuous Improvement

Review detection results, investigate alert noise, and refine configurations to improve monitoring effectiveness.

---

## Project Roadmap

| Phase | Project | Status |
|-------|---------|--------|
| 1 | Core Network Infrastructure | Completed |
| 2 | Proxmox Virtualization | Completed |
| 3 | VLAN Segmentation and Firewall Policies | Completed |
| 4 | DShield/Cowrie Honeypot | Completed |
| 5 | Wazuh SIEM Deployment | Completed |
| 6 | Windows 11 SOC Endpoint | Completed |
| 7 | AtomicSOC Security Testing Platform | Initial Implementation Complete |
| 8 | Security Detection Engineering | Ongoing |
| 9 | Infrastructure and SOC Documentation | In Progress |
| 10 | Windows Server and Active Directory | Planned |
| 11 | Active Directory Security Monitoring | Planned |
| 12 | AI-Powered SOC Job Discovery Platform | Planned |
| 13 | Expanded Security Automation | Planned |

---

## Project Philosophy

This project is being developed through a practical, hands-on approach emphasizing technical understanding, repeatability, and continuous improvement.

Infrastructure changes are researched, implemented, tested, validated, and documented before progressing to the next major phase.

The project emphasizes:

- Hands-on technical experience
- Security-focused infrastructure design
- Documentation and knowledge sharing
- Troubleshooting and root cause analysis
- Detection engineering
- Practical security investigations
- Controlled adversary simulation
- Automation and operational efficiency
- Continuous learning and professional development

The objective is not simply to build a functional home laboratory, but to develop practical skills and engineering practices applicable to enterprise IT infrastructure, SOC operations, and cybersecurity engineering.

The environment will continue evolving as new technologies, security tools, and detection capabilities are introduced.
