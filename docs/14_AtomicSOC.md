# 14 — AtomicSOC: Custom Adversary Simulation Platform

## Project Overview

AtomicSOC is a custom cybersecurity training application developed for my HomeLAB environment.

The application provides a browser-based interface for executing controlled Atomic Red Team tests against a dedicated Windows 11 SOC endpoint.

AtomicSOC was developed to make adversary simulation more accessible, repeatable, and useful for hands-on security monitoring and SOC investigation exercises.

The application integrates Python, FastAPI, PowerShell, Atomic Red Team, and Windows endpoint telemetry within a segmented cybersecurity lab.

**Current Application Version:** 0.6.0

**Project Status:** Functional — Continued Development

### Primary Objectives

- Develop a custom security testing application.
- Integrate Atomic Red Team with a Python backend.
- Execute approved MITRE ATT&CK technique simulations.
- Provide a browser-based interface for test execution.
- Support randomized security investigation exercises.
- Record test execution results.
- Generate Windows endpoint telemetry for SOC training.
- Investigate resulting activity using Wazuh SIEM.
- Develop practical detection engineering skills.

---

## 1. Infrastructure

AtomicSOC runs on the dedicated Windows SOC endpoint hosted within Proxmox VE.

### Hosting Environment

| Component | Configuration |
|---|---|
| Hypervisor | Proxmox VE |
| Endpoint | WIN11-SOC-01 |
| Operating System | Windows 11 Pro |
| Architecture | 64-bit |
| Virtual CPUs | 4 |
| Memory | 8 GB |
| Storage | 80 GB |
| Network | Segmented Security VLAN |
| Backend Language | Python |
| API Framework | FastAPI |
| ASGI Server | Uvicorn |
| Frontend | HTML, CSS, JavaScript |
| Test Framework | Atomic Red Team |
| Execution Environment | PowerShell 7 |
| SIEM | Wazuh |
| Application Version | 0.6.0 |

The application is hosted entirely within the home lab.

Internal IP addresses, firewall policies, and management details are intentionally excluded from this public documentation.

---

## 2. Application Architecture

AtomicSOC uses a browser-based frontend that communicates with a Python FastAPI backend.

The backend manages approved tests, executes Atomic Red Team commands, and records execution results.

### Architecture Diagram

```text
                  AtomicSOC Web Interface
                            |
                            v
                    HTML / CSS / JavaScript
                            |
                            v
                      FastAPI Backend
                            |
               +------------+------------+
               |                         |
               v                         v
        Approved Test Catalog      Execution Logging
               |                         ^
               v                         |
        Test Authorization               |
               |                         |
               v                         |
          PowerShell 7                   |
               |                         |
               v                         |
       Invoke-AtomicRedTeam -------------+
               |
               v
        Windows 11 SOC Endpoint
               |
               v
       Windows Security Telemetry
               |
               v
            Wazuh Agent
               |
               v
           Wazuh SIEM
               |
               v
         SOC Investigation
```

The application separates test selection, test execution, and execution logging into distinct functions.

This design makes it possible to extend the test catalog without rewriting the application's execution logic.

---

## 3. Application Directory Structure

AtomicSOC is organized into separate directories for application code, configuration, certificates, and execution logs.

### Verified Directory Structure

```text
AtomicSOC/
|
+-- Start-AtomicSOC.ps1
|
+-- certs/
|   +-- [TLS certificates and private keys]
|
+-- config/
|   +-- approved-tests.json
|   +-- [configuration backup]
|
+-- logs/
|   +-- execution-log.jsonl
|
+-- web/
    +-- app.py
    |
    +-- static/
        +-- index.html
        +-- app.js
        +-- styles.css
```

### Component Responsibilities

| Component | Purpose |
|---|---|
| `Start-AtomicSOC.ps1` | Starts the application server |
| `web/app.py` | FastAPI backend and execution logic |
| `web/static/index.html` | Browser interface |
| `web/static/app.js` | Frontend functionality and API communication |
| `web/static/styles.css` | Application styling |
| `config/approved-tests.json` | Atomic test catalog and approval configuration |
| `logs/execution-log.jsonl` | Persistent execution history |
| `certs/` | HTTPS certificate and private-key storage |

### Security Considerations

TLS private keys and certificate bundles are stored separately from the application source code.

These files must not be published in a public repository.

Execution logs and local configuration files should also be reviewed before publication because they may contain sensitive operational information.

---

## 4. FastAPI Backend

AtomicSOC uses FastAPI to expose application functionality through HTTP API endpoints.

### Application Metadata

```python
app = FastAPI(
    title="HomeLAB AtomicSOC",
    description="Controlled Atomic Red Team SOC training platform",
    version="0.6.0"
)
```

### Backend Responsibilities

The FastAPI application handles:

- Serving the web interface.
- Serving static frontend resources.
- Loading the approved-test catalog.
- Validating requested test identifiers.
- Selecting randomized tests.
- Executing Atomic Red Team commands.
- Capturing execution output.
- Recording execution results.
- Returning structured JSON responses.
- Reporting application health.

### Static File Hosting

The backend mounts the frontend's static directory through FastAPI.

```python
app.mount(
    "/static",
    StaticFiles(directory=STATIC_DIR),
    name="static"
)
```

The root endpoint returns the application's HTML interface.

---

## 5. API Endpoints

The application exposes six verified API routes.

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/` | Serve the web interface |
| GET | `/health` | Return application health information |
| GET | `/api/tests` | Retrieve approved Atomic tests |
| GET | `/api/random/select` | Select a random approved test |
| POST | `/api/run/{test_id}` | Execute a selected approved test |
| POST | `/api/random/run/{test_id}` | Execute a selected approved test using the random workflow |

### Health Endpoint

```http
GET /health
```

The health endpoint returns:

- Application health status.
- Target endpoint identifier.
- Current timestamp.

### Approved Test Endpoint

```http
GET /api/tests
```

The endpoint loads the approved-test catalog and returns:

- Number of approved tests.
- Approved test definitions.

### Manual Execution Endpoint

```http
POST /api/run/{test_id}
```

This endpoint validates the requested test against the approved catalog before execution.

### Random Selection Endpoint

```http
GET /api/random/select
```

This endpoint randomly selects one approved test and returns its metadata.

**Selecting a random test does not execute it.**

### Random Execution Endpoint

```http
POST /api/random/run/{test_id}
```

This endpoint executes an approved test through the randomized workflow.

The backend records the execution source as `RANDOM`.

---

## 6. Approved Test Catalog

AtomicSOC uses a JSON configuration file to define its available Atomic Red Team tests.

### Configuration File

```text
config/approved-tests.json
```

### Catalog Fields

The backend expects test definitions containing fields such as:

| Field | Purpose |
|---|---|
| `id` | AtomicSOC test identifier |
| `technique_id` | MITRE ATT&CK technique identifier |
| `technique_name` | Technique name |
| `test_number` | Atomic Red Team test number |
| `name` | Test description |
| `guid` | Atomic test identifier |
| `risk` | Risk classification |
| `status` | Approval status |

### Approval Enforcement

The application loads tests from the JSON catalog and filters them by approval status.

```python
return [
    test for test in tests
    if test.get("status") == "APPROVED"
]
```

Only tests with the exact status `APPROVED` are returned by the catalog-loading function.

Both execution endpoints validate requested test identifiers against this filtered catalog.

### Unauthorized Test Requests

If a requested test identifier is not present in the approved catalog, the backend returns:

```text
HTTP 404
Requested Atomic test is not approved.
```

This prevents execution through the API of tests that are not included in the approved catalog.

### Important Distinction

The approved-test catalog restricts which predefined tests the application can execute.

It does not authenticate users or determine who is permitted to access the application.

User authentication and authorization remain separate security requirements.

---

## 7. Configured MITRE ATT&CK Tests

Five Atomic Red Team test definitions were verified in the application's configuration.

| AtomicSOC ID | MITRE ATT&CK ID | Technique | Atomic Test |
|---|---|---|---|
| ATOMIC-001 | T1059.001 | PowerShell | 17 |
| ATOMIC-002 | T1082 | System Information Discovery | 35 |
| ATOMIC-003 | T1057 | Process Discovery | 2 |
| ATOMIC-004 | T1070.004 | File Deletion | 4 |
| ATOMIC-005 | T1016 | System Network Configuration Discovery | 1 |

### ATOMIC-001 — PowerShell Command Execution

**MITRE ATT&CK:** T1059.001

**Technique:** PowerShell

**Atomic Test:** 17

This test is included to support investigations involving PowerShell execution activity.

Potential telemetry sources include:

- PowerShell Operational logs.
- Script Block Logging.
- Sysmon process telemetry, depending on configuration.
- Windows Security events, depending on audit policy.

### ATOMIC-002 — System Information Discovery

**MITRE ATT&CK:** T1082

**Technique:** System Information Discovery

**Atomic Test:** 35

The configured test checks operating system information using the Windows `ver` command.

This provides an example of system reconnaissance activity.

### ATOMIC-003 — Process Discovery

**MITRE ATT&CK:** T1057

**Technique:** Process Discovery

**Atomic Test:** 2

The configured test uses `tasklist` to enumerate running processes.

This technique is useful for investigating system discovery behavior.

### ATOMIC-004 — File Deletion

**MITRE ATT&CK:** T1070.004

**Technique:** File Deletion

**Atomic Test:** 4

The configured test simulates file deletion through Windows command execution.

This provides an opportunity to examine process and file-related telemetry.

### ATOMIC-005 — System Network Configuration Discovery

**MITRE ATT&CK:** T1016

**Technique:** System Network Configuration Discovery

**Atomic Test:** 1

This test simulates discovery of Windows network configuration information.

It provides a controlled example of network reconnaissance behavior.

### Previously Validated Execution

The following tests were successfully executed during earlier lab development:

- T1057 — Process Discovery, Test 2.
- T1070.004 — File Deletion, Test 4.
- T1016 — System Network Configuration Discovery, Test 1.

The presence of the remaining catalog entries does not, by itself, establish successful execution or detection.

---

## 8. Manual Test Execution

The frontend provides a workflow for manually selecting and executing Atomic Red Team tests.

### Workflow

```text
Open AtomicSOC
       |
       v
Load Approved Tests
       |
       v
Select Test
       |
       v
Display Confirmation
       |
       v
User Confirms
       |
       v
POST /api/run/{test_id}
       |
       v
Validate Approved Test
       |
       v
Execute Atomic Red Team
       |
       v
Capture Execution Result
       |
       v
Write Execution Log
       |
       v
Return Result to Browser
```

### Frontend Implementation

The JavaScript frontend includes:

```javascript
async function loadTests()
```

This function requests the approved-test catalog.

```javascript
async function runTest(testId, button)
```

This function handles manual test execution.

The frontend uses a browser confirmation dialog before submitting the execution request.

### API Communication

The frontend sends a POST request to:

```text
/api/run/{test_id}
```

The backend validates the test identifier before attempting execution.

### Execution Source

Manual executions are recorded with:

```json
{
  "source": "MANUAL"
}
```

---

## 9. Randomized Test Execution

AtomicSOC includes a randomized test workflow designed to support less predictable SOC investigation exercises.

### Purpose

Rather than repeatedly executing the same known test, the randomized workflow allows an approved test to be selected automatically.

This can help create training scenarios in which the analyst must determine what activity occurred by examining endpoint telemetry.

### Random Selection

The backend uses Python's random selection functionality.

```python
test = random.choice(tests)
```

The selection is made from the approved-test catalog.

### Random Execution Workflow

```text
User Selects Random Incident
              |
              v
GET /api/random/select
              |
              v
Random Approved Test Selected
              |
              v
Test Information Returned
              |
              v
Browser Confirmation
              |
              v
POST /api/random/run/{test_id}
              |
              v
Approved Test Validated
              |
              v
Atomic Red Team Execution
              |
              v
Execution Result Logged
              |
              v
Result Displayed
```

### Frontend Implementation

The JavaScript frontend includes:

```javascript
async function runRandomIncident()
```

This function communicates with the random-selection endpoint.

The selected test is presented for confirmation before execution.

### Execution Source

Randomized executions are recorded with:

```json
{
  "source": "RANDOM"
}
```

### Design Benefit

Separating selection from execution prevents the random-selection API request from immediately triggering a test.

However, the backend execution endpoint itself does not enforce an interactive confirmation step. Confirmation is implemented in the browser frontend.

---

## 10. Atomic Red Team Execution Engine

AtomicSOC executes Atomic Red Team tests using PowerShell.

### Execution Method

The backend launches:

```text
pwsh.exe
```

with the following execution parameters:

```text
-NoProfile
-NonInteractive
-Command
```

### Command Structure

The backend constructs an Atomic Red Team command using the approved technique identifier and test number.

```powershell
Import-Module Invoke-AtomicRedTeam

Invoke-AtomicTest <TECHNIQUE_ID> -TestNumbers <TEST_NUMBER>
```

### Python Subprocess

The backend uses Python's `subprocess.run()` function.

Key execution settings include:

| Setting | Configuration |
|---|---|
| Executable | `pwsh.exe` |
| PowerShell Profile | Disabled |
| Interactive Mode | Disabled |
| Standard Output | Captured |
| Standard Error | Captured |
| Execution Timeout | 120 seconds |

### Timeout Handling

Each test has a maximum execution time of 120 seconds.

If the subprocess exceeds this limit, AtomicSOC records a timeout result and returns an HTTP 504 error.

### Execution Status

The application records:

- `SUCCESS` when the subprocess exits with code `0`.
- `FAILED` when the subprocess exits with a nonzero code.
- `TIMEOUT` when the configured execution timeout is exceeded.

### Detection Validation

A successful subprocess exit does not necessarily indicate that:

- Every intended test action completed.
- Windows recorded the expected telemetry.
- Wazuh ingested the resulting events.
- A Wazuh detection rule generated an alert.

These outcomes must be validated independently during SOC exercises.

---

## 11. Execution Logging

AtomicSOC maintains a persistent execution log.

### Log File

```text
logs/execution-log.jsonl
```

### Log Format

The application uses JSON Lines.

Each execution record is written as an individual JSON object on a separate line.

### Recorded Fields

For completed subprocess executions, the application records:

| Field | Description |
|---|---|
| `id` | AtomicSOC test identifier |
| `technique_id` | MITRE ATT&CK technique |
| `technique_name` | Technique name |
| `test_number` | Atomic test number |
| `name` | Test description |
| `guid` | Atomic test GUID |
| `risk` | Configured risk classification |
| `target` | Target endpoint |
| `timestamp` | Local execution timestamp |
| `mode` | Execution mode |
| `source` | Manual or random |
| `status` | Success or failure |
| `executed` | Execution-attempt indicator |
| `exit_code` | Subprocess exit code |
| `stdout` | Captured standard output |
| `stderr` | Captured standard error |

### Example Log Structure

The following is an illustrative record, not a copied execution result:

```json
{
  "id": "ATOMIC-003",
  "technique_id": "T1057",
  "technique_name": "Process Discovery",
  "test_number": 2,
  "name": "Process Discovery - tasklist",
  "target": "WIN11-SOC-01",
  "timestamp": "2026-10-20T12:00:00",
  "mode": "LIVE",
  "source": "MANUAL",
  "status": "SUCCESS",
  "executed": true,
  "exit_code": 0,
  "stdout": "[truncated example output]",
  "stderr": ""
}
```

### Output Retention

The application stores up to the last 4,000 characters of standard output and standard error for completed subprocess executions.

### Timeout Records

Timeout records contain a reduced set of fields and identify the execution status as:

```text
TIMEOUT
```

### Operational Value

Execution logs provide a record of:

- Which test was requested.
- When execution was attempted.
- Whether the test was selected manually or randomly.
- Whether the subprocess completed successfully.
- What output the execution process returned.

This information can be compared with Windows event timestamps and Wazuh telemetry during investigation.

### Current Limitation

Execution records are written to a local file.

The application code reviewed does not establish that AtomicSOC execution logs are independently forwarded to Wazuh.

---

## 12. HTTPS Configuration

AtomicSOC is launched using Uvicorn with TLS enabled.

### Startup Script

```text
Start-AtomicSOC.ps1
```

### Application Startup

The startup script changes to the application directory and launches Uvicorn.

A sanitized representation of the startup command is:

```powershell
Set-Location C:\AtomicSOC\web

python -m uvicorn app:app `
    --host 0.0.0.0 `
    --port 8443 `
    --ssl-certfile <CERTIFICATE_PATH> `
    --ssl-keyfile <PRIVATE_KEY_PATH>
```

### Transport Security

The application uses HTTPS for browser-to-server communication.

TLS certificate and private-key files are stored in the application's certificate directory.

### Network Binding

Uvicorn is configured to listen on:

```text
0.0.0.0
```

This binds the application to all available network interfaces on the endpoint.

Access must therefore be restricted through appropriate network and host firewall controls.

### Certificate Management

The application currently uses locally stored TLS certificate files.

The certificate trust configuration and renewal process were not evaluated during this documentation review.

---

## 13. Security Controls

AtomicSOC includes application-level restrictions intended to limit test execution to a predefined catalog.

### Implemented Controls

| Control | Implementation |
|---|---|
| Approved-Test Catalog | JSON-based test allowlist |
| Test Validation | Required before execution |
| Unknown Test Rejection | HTTP 404 |
| Execution Timeout | 120 seconds |
| Browser Confirmation | Implemented in frontend |
| HTTPS | Configured |
| Execution Logging | JSON Lines |
| Segmented Hosting | Security VLAN |

### Approved-Test Enforcement

The application does not accept arbitrary PowerShell commands directly through its documented API endpoints.

Instead, it retrieves technique identifiers and test numbers from the approved-test catalog.

This reduces the scope of commands that can be requested through the application.

### Security Limitations

The reviewed backend does not implement:

- User authentication.
- Per-user authorization.
- Role-based access control.
- Request rate limiting.
- API-level confirmation enforcement.
- Centralized application audit-log forwarding.

The application also executes PowerShell subprocesses under the permissions of the account running the web server.

### Recommended Access Model

AtomicSOC should remain accessible only from trusted systems within the lab.

Recommended safeguards include:

1. Restrict inbound connections using host and network firewall rules.
2. Avoid exposing the application directly to the public internet.
3. Run the application under a dedicated, least-privileged account where feasible.
4. Protect the approved-test catalog against unauthorized modification.
5. Protect TLS private keys and certificate bundles.
6. Review execution logs before publishing any examples.
7. Add authentication before expanding access beyond trusted lab administrators.

### Additional Hardening Opportunity

The backend currently interpolates catalog values into a PowerShell command string.

Although execution requests are restricted to catalog entries, those entries must be treated as trusted configuration.

Future development should validate technique identifiers and test numbers against strict expected formats and protect the configuration file from unauthorized changes.

---

## 14. Wazuh SIEM Integration

AtomicSOC operates on a Windows endpoint monitored by Wazuh.

The endpoint has security telemetry collection configured through the Wazuh agent.

### Relevant Telemetry Sources

- Windows Security Event Log.
- Windows Application Event Log.
- Windows System Event Log.
- Sysmon Operational Log.
- Windows PowerShell Operational Log.
- PowerShell Core Operational Log.
- Microsoft Defender Operational Log.

### Monitoring Pipeline

```text
AtomicSOC
    |
    v
Atomic Red Team Execution
    |
    v
Windows Endpoint Activity
    |
    v
Windows Event Logs / Sysmon / PowerShell
    |
    v
Wazuh Agent
    |
    v
Wazuh Manager
    |
    v
Event Decoding and Detection Rules
    |
    v
Wazuh Dashboard
    |
    v
SOC Investigation
```

### Investigation Objective

After executing a test, the analyst can investigate whether the resulting activity was captured by the configured telemetry sources.

Questions include:

- Was a relevant Windows event generated?
- Did the Wazuh agent collect the event?
- Was the event indexed and searchable?
- Did a detection rule generate an alert?
- What process and command-line information was recorded?
- Did the activity map to the expected MITRE ATT&CK technique?

### Detection Coverage

AtomicSOC provides controlled activity generation.

It does not automatically establish detection coverage.

Each technique requires independent telemetry and alert validation.

---

## 15. SOC Training Workflow

AtomicSOC is intended to support repeatable security investigation exercises.

### Example Investigation

An analyst initiates a Process Discovery test through AtomicSOC.

The application executes the corresponding Atomic Red Team test.

The analyst then investigates the resulting endpoint activity.

### Workflow

```text
Select Atomic Test
        |
        v
Execute Test
        |
        v
Record Execution Result
        |
        v
Inspect Windows Telemetry
        |
        v
Search Wazuh Events
        |
        v
Review Related Alerts
        |
        v
Analyze Process Relationships
        |
        v
Determine Observed Behavior
        |
        v
Document Findings
```

### Investigation Questions

1. What technique was executed?
2. What command or process generated the activity?
3. Which account initiated the process?
4. What was the parent process?
5. What timestamp should be used for correlation?
6. Which telemetry source recorded the activity?
7. Was an alert generated?
8. Did the alert accurately represent the behavior?
9. Was additional context required?
10. What detection improvements could be made?

### Learning Outcomes

This process develops experience with:

- Windows process analysis.
- PowerShell activity investigation.
- MITRE ATT&CK mapping.
- SIEM event searches.
- Detection validation.
- False-positive analysis.
- Security event correlation.
- Investigation documentation.

---

## 16. Development Challenges and Lessons Learned

### Frontend and Backend Integration

Developing AtomicSOC required coordinating browser-based JavaScript with FastAPI endpoints.

The frontend requests available tests, submits execution requests, and displays execution results.

### Randomized Execution

The randomized workflow required separating test selection from test execution.

This design allows the user to review a selected test before initiating execution.

### Duplicate Execution Troubleshooting

During development, the random-test workflow produced duplicate confirmation dialogs.

The frontend interaction was corrected so a single user action selects one test and presents one confirmation.

### PowerShell Integration

The backend launches PowerShell as a subprocess and executes Atomic Red Team through the `Invoke-AtomicRedTeam` module.

This required integrating Python process execution with PowerShell-based security tooling.

### Execution Result Handling

The application captures subprocess output and records execution status.

This provides visibility into test execution without requiring the user to inspect the backend console for every result.

### Security Boundaries

Building a security testing application requires distinguishing between:

- Test approval.
- User authorization.
- Execution permissions.
- Network access.
- Audit logging.

An approved-test catalog is useful, but it is not a replacement for authentication or access control.

### Detection Versus Execution

A successful Atomic Red Team execution is not equivalent to a successful SIEM detection.

This distinction is central to meaningful detection engineering.

---

## 17. Current Project Status

| Component | Status |
|---|---|
| FastAPI Backend | Implemented |
| Application Version | 0.6.0 |
| Browser Frontend | Implemented |
| HTML/CSS/JavaScript Interface | Implemented |
| Approved-Test Catalog | Implemented |
| Five Test Definitions | Verified |
| Manual Execution API | Implemented |
| Random Selection API | Implemented |
| Random Execution API | Implemented |
| Browser Confirmation | Implemented |
| PowerShell Execution Engine | Implemented |
| Execution Timeout | Implemented |
| JSON Lines Logging | Implemented |
| HTTPS Startup Configuration | Implemented |
| Wazuh-Monitored Endpoint | Configured |
| End-to-End Detection Validation for Every Test | Not Complete |
| User Authentication | Not Implemented |
| Role-Based Access Control | Not Implemented |
| Centralized AtomicSOC Application Logs | Not Verified |

---

## 18. Future Development

### Application Security

- Implement user authentication.
- Add role-based authorization.
- Restrict application access to approved administrators.
- Improve input validation.
- Protect configuration file integrity.
- Review subprocess execution privileges.
- Implement structured application error handling.

### Detection Engineering

- Validate Wazuh telemetry for every approved test.
- Develop custom detection rules.
- Map detection results to MITRE ATT&CK.
- Record detection success and failure.
- Identify missing telemetry sources.
- Develop reusable investigation playbooks.

### Application Features

- Expand the approved-test catalog.
- Add test execution history to the web interface.
- Provide filtering by MITRE ATT&CK technique.
- Add execution statistics.
- Display associated Wazuh detection results.
- Support repeatable training scenarios.

### SOC Training

- Develop randomized investigation exercises.
- Create analyst investigation worksheets.
- Track investigation outcomes.
- Measure detection coverage.
- Practice alert triage and incident analysis.

### Active Directory Integration

After deploying Active Directory within the home lab, AtomicSOC can be used to support additional Windows domain security exercises.

Potential areas include:

- Domain authentication telemetry.
- Windows account activity.
- Identity-related investigations.
- Group Policy security controls.
- Domain endpoint monitoring.

All future tests should remain restricted to authorized lab systems.

---

## 19. Skills Demonstrated

### Python Development

- Python application development.
- FastAPI implementation.
- JSON configuration management.
- Subprocess execution.
- Exception handling.
- Structured execution logging.

### Web Development

- HTML.
- CSS.
- JavaScript.
- REST API integration.
- Asynchronous frontend requests.
- Browser confirmation workflows.

### Windows Administration

- PowerShell scripting.
- PowerShell 7 execution.
- Windows process management.
- Application deployment.
- TLS-enabled web services.

### Cybersecurity

- Atomic Red Team integration.
- MITRE ATT&CK technique mapping.
- Adversary simulation.
- Windows security telemetry.
- SIEM integration.
- Detection engineering.
- SOC investigation workflows.

### Security Architecture

- Approved-test execution controls.
- Network segmentation.
- HTTPS configuration.
- Security logging.
- Application security assessment.

---

## 20. Project Outcome

Developed a functional custom adversary simulation platform that integrates Atomic Red Team with a browser-based interface.

### Key Accomplishments

- Built a Python FastAPI backend.
- Developed an HTML, CSS, and JavaScript frontend.
- Implemented a JSON-based approved-test catalog.
- Configured five MITRE ATT&CK technique simulations.
- Integrated PowerShell-based Atomic Red Team execution.
- Implemented manual test execution.
- Developed randomized test selection and execution.
- Added execution confirmation to the frontend.
- Implemented persistent JSON Lines execution logging.
- Added subprocess output capture.
- Implemented execution timeout handling.
- Configured HTTPS application startup.
- Integrated the application into a Wazuh-monitored Windows SOC environment.

AtomicSOC provides a foundation for continued development in security automation, detection engineering, and SOC investigation.

The project demonstrates the ability to combine software development with practical cybersecurity operations.

---

## Related Documentation

- `01_Project_Overview.md` — HomeLAB architecture
- `12_Wazuh_SIEM.md` — Centralized security monitoring
- `13_Windows_11_SOC_Endpoint.md` — Windows endpoint security configuration

---

**Project Status:** Functional — Continued Development

**Application Version:** 0.6.0

**Last Reviewed:** October 20, 2026
