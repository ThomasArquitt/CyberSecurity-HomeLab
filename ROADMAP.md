# CyberSecurity HomeLAB Roadmap

**Project Status:** Operational — Active Development  
**Last Updated:** October 20, 2026

## Overview

This roadmap outlines the development of the CyberSecurity HomeLAB, including completed infrastructure, active cybersecurity projects, and planned improvements.

The objective is to build an enterprise-inspired cybersecurity environment that provides practical experience with:

- Network security and segmentation
- Virtualization and infrastructure administration
- Security Information and Event Management (SIEM)
- Security Operations Center (SOC) investigations
- Endpoint detection and monitoring
- Detection engineering
- Adversary simulation
- Identity and access management
- Security automation
- Backup and disaster recovery

The environment is actively used to develop technical skills relevant to SOC Analyst and Security Engineering roles.

---

# Phase 1 — Core Infrastructure ✅ Complete

## Network Infrastructure

- [x] Deploy FortiGate 90D firewall
- [x] Configure direct fiber WAN connectivity
- [x] Deploy Ubiquiti Switch Lite 8 PoE
- [x] Configure IEEE 802.1Q VLAN segmentation
- [x] Configure Layer 3 inter-VLAN routing
- [x] Configure DHCP scopes
- [x] Deploy wireless infrastructure
- [x] Configure private and public wireless networks
- [x] Implement firewall access policies
- [x] Configure Network Address Translation
- [x] Establish SSH-based infrastructure administration

## Virtualization

- [x] Deploy Proxmox Virtual Environment
- [x] Configure VLAN-aware virtual networking
- [x] Deploy Ubuntu-based DShield honeypot
- [x] Deploy Windows 11 SOC endpoint
- [x] Deploy Ubuntu-based Wazuh SIEM
- [x] Configure virtual machine snapshots
- [x] Configure external backup storage
- [x] Configure automated backup scheduling
- [x] Implement backup retention policies
- [x] Retire unused Ubuntu Management VM

## Documentation

- [x] Document high-level network architecture
- [x] Document VLAN design
- [x] Document IP addressing architecture
- [x] Document Proxmox configuration
- [x] Document FortiGate configuration
- [x] Document Ubiquiti switch configuration
- [x] Document Ubuntu infrastructure
- [x] Document DShield honeypot
- [x] Document backup and recovery strategy
- [x] Establish GitHub project documentation

### Phase 1 Outcome

The HomeLAB operates on a segmented FortiGate-managed network with centralized virtualization and documented infrastructure.

---

# Phase 2 — Security Documentation 🚧 In Progress

## Firewall

- [x] Document firewall architecture
- [x] Document firewall policy design
- [x] Document NAT and VIP architecture
- [x] Document administrative access controls
- [x] Document network segmentation
- [ ] Complete additional firewall security-hardening validation

## Honeypot

- [x] Document DShield deployment
- [x] Document honeypot architecture
- [x] Document Wazuh telemetry integration
- [x] Document attack telemetry collection
- [ ] Expand documented attack observations
- [ ] Create detailed honeypot investigation walkthroughs

## Wazuh SIEM

- [x] Deploy centralized Wazuh infrastructure
- [x] Configure endpoint agents
- [x] Integrate Windows security telemetry
- [x] Integrate Linux endpoint telemetry
- [x] Integrate FortiGate firewall logs
- [x] Integrate DShield/Cowrie telemetry
- [x] Create custom detection rules
- [x] Perform initial rule tuning
- [ ] Complete dedicated Wazuh configuration documentation
- [ ] Document custom detection rules and investigation workflows

## Windows SOC Endpoint

- [x] Deploy Windows 11 virtual machine
- [x] Configure Microsoft Defender
- [x] Configure Sysmon
- [x] Enable PowerShell logging
- [x] Install Wazuh agent
- [x] Configure remote administration
- [ ] Complete Windows SOC endpoint documentation

## AtomicSOC

- [x] Install Atomic Red Team
- [x] Develop Python FastAPI backend
- [x] Develop web-based testing interface
- [x] Implement controlled security test execution
- [x] Implement randomized test selection
- [x] Execute initial MITRE ATT&CK tests
- [ ] Complete AtomicSOC documentation
- [ ] Expand available security tests
- [ ] Document detection validation results

## Repository

- [x] Establish structured infrastructure documentation
- [x] Complete initial public documentation sanitization
- [ ] Complete Wazuh, Windows SOC, and AtomicSOC documentation
- [ ] Add updated architecture diagrams
- [ ] Add sanitized configuration screenshots
- [ ] Create detailed troubleshooting documentation
- [ ] Perform final repository-wide review

### Phase 2 Outcome

A comprehensive public cybersecurity portfolio documenting operational infrastructure, security monitoring, and hands-on detection engineering.

---

# Phase 3 — Security Operations 🚧 Partially Complete

## Centralized Monitoring

- [x] Deploy centralized log collection
- [x] Integrate Windows endpoint monitoring
- [x] Integrate Linux endpoint monitoring
- [x] Integrate firewall telemetry
- [x] Integrate honeypot telemetry
- [x] Integrate gaming server monitoring
- [x] Validate active Wazuh agent connectivity
- [ ] Expand infrastructure health monitoring
- [ ] Develop additional performance dashboards

## Wazuh SIEM

- [x] Deploy Wazuh SIEM
- [x] Configure endpoint log ingestion
- [x] Configure network security log ingestion
- [x] Implement custom detection rules
- [x] Tune selected detection rules
- [x] Generate controlled security events
- [ ] Expand alert investigation workflows
- [ ] Develop documented SOC investigation scenarios
- [ ] Create additional detection use cases
- [ ] Build repeatable detection validation procedures

## Microsoft Sentinel

**Status:** Planned

- [ ] Evaluate Microsoft Sentinel deployment
- [ ] Configure Azure integration
- [ ] Configure appropriate data connectors
- [ ] Ingest selected security telemetry
- [ ] Develop KQL detection queries
- [ ] Configure analytics rules
- [ ] Investigate generated incidents
- [ ] Compare Sentinel and Wazuh capabilities

Microsoft Sentinel is a potential future expansion rather than a currently deployed HomeLAB service.

## Detection Engineering

- [x] Implement initial custom Wazuh detection rules
- [x] Perform detection rule tuning
- [x] Execute controlled Atomic Red Team tests
- [x] Generate endpoint security telemetry
- [ ] Expand MITRE ATT&CK technique coverage
- [ ] Develop repeatable threat hunting workflows
- [ ] Document detection-to-telemetry mappings
- [ ] Create investigation playbooks
- [ ] Develop additional custom detections
- [ ] Evaluate automated detection testing

### Phase 3 Outcome

A practical SOC environment supporting log analysis, alert triage, threat hunting, and detection engineering.

---

# Phase 4 — Enterprise Services 📋 Planned

## Windows Server Infrastructure

- [ ] Deploy Windows Server virtual machine
- [ ] Configure static IPv4 addressing
- [ ] Configure Windows Server networking
- [ ] Install Active Directory Domain Services
- [ ] Promote server to domain controller
- [ ] Configure Active Directory-integrated DNS
- [ ] Create organizational units
- [ ] Create domain users and security groups
- [ ] Configure Group Policy Objects
- [ ] Join Windows SOC endpoint to the domain

## Active Directory Security

- [ ] Configure Windows security auditing
- [ ] Enable relevant domain controller event collection
- [ ] Integrate Active Directory telemetry with Wazuh
- [ ] Monitor failed authentication attempts
- [ ] Detect suspicious account creation
- [ ] Monitor privileged group changes
- [ ] Investigate password spraying activity
- [ ] Investigate account enumeration
- [ ] Develop identity-focused detection rules

## Additional Enterprise Services

- [ ] Evaluate Active Directory Certificate Services
- [ ] Explore certificate-based authentication
- [ ] Evaluate Windows DHCP services where appropriate
- [ ] Implement additional Group Policy security baselines
- [ ] Explore service accounts and delegated administration

### Phase 4 Outcome

An enterprise-inspired Windows domain environment supporting identity administration, authentication monitoring, and Active Directory-focused SOC investigations.

---

# Phase 5 — Automation 📋 Planned

## PowerShell Automation

- [ ] Develop Windows endpoint inventory scripts
- [ ] Automate selected system administration tasks
- [ ] Create Windows event log collection utilities
- [ ] Develop Active Directory auditing scripts
- [ ] Automate selected security configuration checks

## Python Automation

- [ ] Develop security log analysis utilities
- [ ] Create automated event enrichment workflows
- [ ] Expand AtomicSOC functionality
- [ ] Develop detection validation scripts
- [ ] Evaluate security API integrations

## Infrastructure Automation

- [ ] Evaluate automated Proxmox VM provisioning
- [ ] Develop infrastructure health monitoring scripts
- [ ] Evaluate automated configuration backups
- [ ] Implement additional backup verification
- [ ] Test virtual machine restoration
- [ ] Resolve backup notification delivery
- [ ] Evaluate additional backup destinations

## AI-Powered Cybersecurity Job Discovery

- [ ] Deploy lightweight Proxmox LXC container
- [ ] Develop Python-based job discovery application
- [ ] Integrate supported job board APIs
- [ ] Implement permitted web scraping
- [ ] Add AI-assisted job classification
- [ ] Filter SOC Analyst and Junior Security Engineer opportunities
- [ ] Implement duplicate detection
- [ ] Configure scheduled job searches
- [ ] Implement notifications

### Phase 5 Outcome

A collection of reusable automation tools that improve infrastructure management, security monitoring, and operational efficiency.

---

# Phase 6 — Advanced SOC Engineering 📋 Future

## Detection Engineering

- [ ] Expand MITRE ATT&CK technique coverage
- [ ] Develop multi-event correlation rules
- [ ] Build structured detection testing procedures
- [ ] Document detection logic and tuning decisions
- [ ] Evaluate threat intelligence enrichment

## Threat Hunting

- [ ] Create hypothesis-driven hunting exercises
- [ ] Develop reusable hunting queries
- [ ] Investigate suspicious process execution
- [ ] Analyze authentication anomalies
- [ ] Correlate endpoint and network telemetry

## Incident Response

- [ ] Develop incident investigation playbooks
- [ ] Create simulated incident scenarios
- [ ] Document investigation timelines
- [ ] Practice evidence collection
- [ ] Develop incident reporting templates

## Security Engineering

- [ ] Evaluate additional endpoint security integrations
- [ ] Explore security orchestration workflows
- [ ] Implement additional monitoring automation
- [ ] Evaluate SIEM performance and scalability
- [ ] Expand identity and network detection capabilities

### Phase 6 Outcome

A mature cybersecurity laboratory demonstrating SOC investigation, detection engineering, threat hunting, and security automation capabilities.

---

# Current Priorities

The immediate development priorities are:

1. Complete Wazuh SIEM documentation.
2. Complete Windows SOC endpoint documentation.
3. Complete AtomicSOC documentation.
4. Perform the final infrastructure documentation review.
5. Deploy Windows Server and Active Directory.
6. Join the Windows SOC endpoint to the domain.
7. Implement Active Directory security monitoring.
8. Develop the AI-powered cybersecurity job discovery application.
9. Add final diagrams and sanitized screenshots.

---

# Long-Term Goals

- Build and maintain an enterprise-inspired cybersecurity laboratory.
- Continuously document infrastructure and technical decisions.
- Develop practical SOC Analyst investigation skills.
- Expand detection engineering and threat hunting capabilities.
- Gain experience with enterprise identity infrastructure.
- Develop PowerShell and Python security automation.
- Demonstrate practical cybersecurity engineering skills.
- Maintain a professional GitHub portfolio.
- Continue expanding the environment with defensive security technologies.

---

# Project Documentation

Related documentation includes:

- `01_Project_Overview.md`
- `02_Hardware_Inventory.md`
- `03_Network_Architecture.md`
- `04_VLAN_Design.md`
- `05_IP_Addressing_Plan.md`
- `06_Proxmox_Configuration.md`
- `07_FortiGate_Configuration.md`
- `08_Ubiquiti_Switch_Configuration.md`
- `09_Ubuntu_Server.md`
- `10_DShield_Honeypot.md`
- `11_Backup_and_Recovery.md`
- `PROJECT_STATUS.md`

---

**Current Status:** Core infrastructure operational. Security documentation and SOC capabilities actively expanding.

**Last Reviewed:** October 20, 2026
