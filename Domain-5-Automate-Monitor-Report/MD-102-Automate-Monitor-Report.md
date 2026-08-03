# MD-102 Study Notes — Automate, Monitor and Report
> Domain 5: Optimize Endpoint Operations (10–15% of exam)

---

## Table of Contents
1. [Core Concept — From Reactive to Proactive](#1-core-concept--from-reactive-to-proactive)
2. [PowerShell and Microsoft Graph](#2-powershell-and-microsoft-graph)
3. [PowerShell Scripts in Intune](#3-powershell-scripts-in-intune)
4. [Proactive Remediations](#4-proactive-remediations)
5. [Endpoint Analytics](#5-endpoint-analytics)
6. [Security Copilot in Intune](#6-security-copilot-in-intune)
7. [Reporting and Alerting](#7-reporting-and-alerting)
8. [Exam Scenario Practice](#8-exam-scenario-practice)
9. [Key Terms Glossary](#9-key-terms-glossary)

---

## 1. Core Concept — From Reactive to Proactive

Domain 5 is about reducing manual IT overhead by automating detection,
remediation, reporting, and alerting — so Intune manages itself as much
as possible.

```
REACTIVE (without Domain 5 tools)
    User calls helpdesk → IT investigates → IT fixes manually
    Board asks for report → IT runs report manually → IT emails results
    Enrollment fails → Nobody knows until user complains

PROACTIVE (with Domain 5 tools)
    Issue detected automatically → remediated silently → no helpdesk call
    Report scheduled → runs automatically → delivered without human intervention
    Enrollment fails → alert fires instantly → helpdesk ticket raised
```

### The Domain 5 Toolkit

| Tool | Problem it solves |
|---|---|
| **PowerShell + Microsoft Graph** | Automate any Intune task programmatically |
| **PowerShell scripts in Intune** | Configure devices beyond Settings Catalog |
| **Proactive remediations** | Detect and self-heal device issues automatically |
| **Endpoint Analytics** | Proactively identify device health issues before users complain |
| **Security Copilot** | AI-powered threat insight and direct action |
| **Reports + alerts** | Scheduled reporting and real-time notifications |

---

## 2. PowerShell and Microsoft Graph

### Microsoft Graph — The Backbone

Microsoft Graph is a **single REST API endpoint** that provides access to
virtually all Microsoft 365 data — including every Intune object.

```
Every device, policy, compliance state, app assignment, and report
in Intune is accessible via Microsoft Graph API
URL: https://graph.microsoft.com/v1.0/deviceManagement/...
```

### Microsoft Graph PowerShell SDK

The **Microsoft Graph PowerShell SDK** wraps Graph API calls in familiar
PowerShell cmdlets — allowing IT to automate Intune tasks via scripts.

```
Step 1 — Connect and authenticate
Connect-MgGraph -Scopes "DeviceManagement.Read.All"
(Requires appropriate app registration or admin consent)

Step 2 — Pull Intune data via Graph
Get-MgDeviceManagementManagedDevice |
Where-Object {$_.complianceState -eq "noncompliant"}

Step 3 — Process and export
Export-Csv -Path ".\non-compliant-devices.csv" -NoTypeInformation

Step 4 — Deliver results
Send-MgUserMail (via Graph) or Send-MailMessage (standard PowerShell)
```

### What IT Can Automate via PowerShell + Graph

```
Compliance reporting     →  Pull non-compliant devices, export to CSV, email
Device inventory         →  Audit all enrolled devices by OS, model, version
Policy assignment        →  Bulk-assign profiles or apps to groups
Stale device cleanup     →  Find devices not checked in for 90+ days, retire
Group membership         →  Add/remove devices from groups dynamically
Onboarding reports       →  Track new device enrollment over time
```

### Scheduled Automation

```
Azure Automation Runbooks  →  Schedule scripts to run automatically
                               e.g. Non-compliant report every Monday 08:00
Local Task Scheduler       →  Run scripts on a management device
Azure Logic Apps           →  Trigger scripts based on events
```

> ⚠️ **Exam phrase:** "Automatically generate and email a report without
> manual intervention"
> Answer: PowerShell + Microsoft Graph (+ scheduling mechanism)

---

## 3. PowerShell Scripts in Intune

Beyond admin automation, Intune can deploy PowerShell scripts **directly
to Windows devices** — running them silently in the background.

### What PowerShell Scripts Can Do That Policies Cannot

```
Registry keys        →  Read/write/delete specific registry values
                         e.g. Set regional settings not in Settings Catalog
Software detection   →  Check if specific software version is installed
Software removal     →  Uninstall apps without Win32 packaging
Local configuration  →  Set local policies, create local accounts
Diagnostic scripts   →  Collect specific logs or system information
```

### Key Script Settings in Intune

```
Run as:     System context (runs as SYSTEM — full admin rights)
            User context (runs as the logged-in user)

Signature:  Require script to be signed (security best practice)

Retry:      Retry if script fails
Scope:      Assign to user or device groups (with filters)
```

### PowerShell Scripts vs Proactive Remediations

```
PowerShell script          Proactive remediation
──────────────────────      ──────────────────────────────
One script                  Two scripts (detect + remediate)
Runs once (or on demand)    Runs on schedule — continuously
No detection logic          Only remediates when issue found
Use for: one-off tasks      Use for: ongoing self-healing
```

---

## 4. Proactive Remediations

### Core Concept

Proactive remediations use **two paired scripts** running on a schedule
to continuously detect and silently fix issues — creating self-healing devices.

```
The analogy: A security guard who checks a door every hour
             If unlocked → locks it silently
             If locked → does nothing and moves on
```

### The Two-Script Flow

```
DETECTION SCRIPT runs on schedule (hourly / daily / weekly)
        │
        ├── Exit code 0  →  Issue NOT present → do nothing → wait for next run
        │
        └── Exit code 1  →  Issue DETECTED → trigger remediation script
                                    │
                                    ▼
                            REMEDIATION SCRIPT runs
                            Silently fixes the issue
                            Reports outcome to Intune
```

### Exit Codes — The Exam Detail

```
Exit 0   =  Compliant — no action needed
Exit 1   =  Non-compliant — run remediation script
```

### Real-World Examples

```
Time zone drift
    Detection:    Check if time zone = "GMT Standard Time"
                  Exit 1 if wrong
    Remediation:  Set-TimeZone -Id "GMT Standard Time"
    Schedule:     Daily

Missing registry key
    Detection:    Check if HKLM:\Software\Company\Key exists
                  Exit 1 if missing
    Remediation:  Create the registry key with correct value
    Schedule:     Hourly

Disk cleanup
    Detection:    Check if C:\ free space < 10GB
                  Exit 1 if below threshold
    Remediation:  Run Disk Cleanup silently
    Schedule:     Weekly
```

### Reporting in Proactive Remediations

Intune shows per-device remediation status:
```
Without issue    →  Device was compliant — no action taken
Remediated       →  Issue detected and fixed automatically
Failed           →  Remediation script ran but could not fix issue
                    IT investigation needed
```

---

## 5. Endpoint Analytics

### What It Measures

Endpoint Analytics collects telemetry from managed devices and produces
health scores and insights — identifying problems before users report them.

| Score area | What it measures |
|---|---|
| **Startup performance** | Boot time and login time per device |
| **App reliability** | App crash rates per app, per device, per OS version |
| **Battery health** | Battery degradation across the fleet |
| **Resource performance** | CPU, RAM, storage utilisation trends |
| **Overall score** | Composite 0–100 health score per device and fleet |

### Benchmark Comparison

Endpoint Analytics benchmarks your fleet against **similar organisations** —
showing whether your device health is above or below industry peers.

```
Your fleet score: 68/100
Industry baseline: 61/100 → your fleet is performing above average
```

This helps justify hardware refresh decisions with data rather than anecdote.

### Prerequisites

```
Licensing:   Microsoft Intune Plan 2 OR Intune Suite (Advanced Analytics)
Setup:       Data collection policy assigned to device groups
             Devices must be Intune-managed
Time:        24–48 hours for initial data to populate
```

### Endpoint Analytics vs KQL Device Queries

```
Endpoint Analytics       →  Historical fleet-wide trend data
                             "Battery health across all Dell devices"
                             Dashboards and scores

KQL device query         →  Live query against ONE specific device
(Remote action)             "Is Windows Update service running right now?"
                             Real-time troubleshooting
```

---

## 6. Security Copilot in Intune

### What It Is

Microsoft Security Copilot is an **AI-powered security assistant** embedded
into the Microsoft security stack — including Intune. It reads signals across
MDE, compliance policies, Entra ID Protection, and Endpoint Analytics
simultaneously and surfaces insights in natural language.

### Two Modes

#### Natural Language Queries
IT asks questions in plain English:
```
"Show all devices with a High MDE risk score"
"Which devices haven't checked in for more than 7 days?"
"Show me non-compliant devices still accessing SharePoint"
"Which users have risky sign-ins this week?"
```

Copilot queries across all connected signals and returns a summarised answer.

#### Security Copilot Agents
Autonomous AI agents that **proactively surface insights** without IT asking:
```
Agent monitors:  MDE risk scores, compliance drift, enrollment failures
Agent surfaces:  "15 devices have High risk score and active resource access"
Agent enables:   Direct action from the same interface
```

### The Key Exam Distinction — Agents vs Reports

```
Standard Intune report      →  IT navigates to Reports → runs report
                               Findings shown → IT navigates elsewhere to act

Security Copilot agent      →  Agent proactively surfaces insight
                               Recommendation + action available in same view
                               IT acts immediately without portal navigation
```

### What IT Can Action Directly from Copilot

```
Rotate BitLocker key on flagged devices
Sync non-compliant devices
Retire high-risk devices
Apply additional compliance policies
Escalate to MDE investigation
```

> ⚠️ **Exam trigger:** "Natural language query about device risk" or
> "Act on security recommendations without switching portals"
> Answer: Security Copilot / Security Copilot agents

---

## 7. Reporting and Alerting

### Two Types of Reporting Need

```
SCHEDULED REPORTS (stakeholder-facing)
    Audience:   Board, management, auditors, compliance officers
    Frequency:  Quarterly, monthly, weekly — consistent schedule
    Content:    Compliance %, update status, security baseline adherence
    Format:     Consistent — same structure each time
    Tool:       Built-in reports or custom reports (scheduled export)

REAL-TIME ALERTS (operational)
    Audience:   Helpdesk, IT admins, security team
    Trigger:    Event-based — fires the moment something goes wrong
    Content:    Enrollment failure, policy conflict, non-compliance
    Delivery:   Email, notification, ITSM ticket
    Tool:       Intune alerts and notifications
```

### Built-In Reports

```
Compliance reports        →  Per-device compliance status, trends
Device inventory          →  All enrolled devices, OS versions, models
Update compliance         →  Windows update status across fleet
App install status        →  Deployment success/failure per app
Configuration profile     →  Profile assignment success/failure
Enrollment reports        →  Enrollment success/failure by method
```

All reports can be **exported to CSV** for further analysis or stakeholder sharing.

### Custom Reports and Workbooks

```
Custom reports   →  Filter any built-in report by any device property
                    Save filter configuration for repeated use
                    Schedule automatic export

Azure Monitor    →  Connect Intune diagnostic data to Azure Monitor
Workbooks        →  Visual dashboards built on top of Azure Monitor data
                    Custom charts, graphs, compliance visualisations
```

### Alerts and Notifications

Intune can send automatic notifications when specific events occur:

```
Alert types:
    Enrollment failure          →  Device failed to enrol
    Policy conflict             →  Two policies applying conflicting settings
    Non-compliance detected     →  Device dropped below compliance threshold
    Update failure              →  Windows Update failed on device
    Low storage                 →  Device storage below threshold
    Configuration profile fail  →  Profile failed to apply

Delivery:
    Email notification          →  IT admin or helpdesk email address
    In-portal notification      →  Alert badge in Intune admin centre
    ITSM integration            →  Via Power Automate / webhook → ServiceNow,
                                   Jira, Freshdesk ticket auto-creation
```

### Microsoft Graph as the Reporting Backbone

```
Every Intune report is powered by Microsoft Graph underneath
PowerShell can pull the same data programmatically:

Get-MgDeviceManagementManagedDevice (all devices)
Get-MgDeviceManagementManagedDeviceCompliancePolicy (compliance)
Get-MgDeviceManagementReport (built-in reports via Graph)

This enables: custom scheduled reports, automated delivery,
              data integration with external systems
```

---

## 8. Exam Scenario Practice

### Scenario 1
> IT needs to automatically generate a list of all non-compliant devices every
> Monday morning and email it to the security team — no manual intervention.

**Answer:** **PowerShell + Microsoft Graph**.
Script uses `Connect-MgGraph` to authenticate, pulls non-compliant devices
via Graph API, exports to CSV, and emails to the security team.
Scheduled via Azure Automation Runbook or Task Scheduler to run every Monday.

---

### Scenario 2
> Devices keep losing their correct time zone setting. IT wants Intune to
> automatically detect when it's wrong and silently fix it daily.

**Answer:** **Proactive remediations** — two scripts required:
- Detection script: checks current time zone, exits 1 if incorrect
- Remediation script: sets correct time zone silently
Scheduled to run daily. Intune reports per-device remediation status.

---

### Scenario 3
> A security analyst asks which devices have a High MDE risk score and are
> still accessing company resources — and wants to act on findings immediately
> without navigating through multiple portals.

**Answer:** **Security Copilot / Security Copilot agents**. Natural language
query surfaces the insight. The agent presents findings AND enables direct
action (rotate key, sync device, retire) from the same interface.
Standard Intune reports require navigating separately to act.

---

### Scenario 4
> The helpdesk only finds out about enrollment failures when users ring in —
> sometimes hours later. IT wants instant notification the moment a device
> fails enrollment.

**Answer:** **Intune alerts and notifications**. Configure an enrollment
failure alert with email delivery to the helpdesk address. Fires instantly
when the event occurs. Can be extended via Power Automate to automatically
create a ticket in ServiceNow or similar ITSM platform.

---

### Scenario 5
> An IT manager wants to identify which devices in the fleet are most likely
> to generate helpdesk calls due to slow startup times — before users complain.

**Answer:** **Endpoint Analytics** — startup performance score identifies
devices with above-average boot and login times. IT prioritises these for
hardware refresh or remediation before users raise tickets.
Requires Intune Plan 2 or Intune Suite licensing and a data collection policy.

---

## 9. Key Terms Glossary

| Term | Plain English Definition |
|---|---|
| **Microsoft Graph** | Single REST API providing access to all Microsoft 365 data including Intune |
| **Graph PowerShell SDK** | PowerShell module that wraps Microsoft Graph API calls as cmdlets |
| **Connect-MgGraph** | PowerShell cmdlet to authenticate to Microsoft Graph |
| **Proactive remediation** | Two-script Intune feature — detection script + remediation script on schedule |
| **Detection script** | First script in proactive remediation — exits 0 (ok) or 1 (issue found) |
| **Remediation script** | Second script — only runs when detection script exits 1 |
| **Exit code 0** | Script reports: no issue — do nothing |
| **Exit code 1** | Script reports: issue detected — run remediation |
| **Endpoint Analytics** | Fleet-wide device health monitoring — startup, app reliability, battery |
| **Startup performance** | Endpoint Analytics score for device boot and login times |
| **App reliability** | Endpoint Analytics score for application crash rates |
| **Benchmark** | Endpoint Analytics comparison against similar organisations |
| **Security Copilot** | AI-powered security assistant — natural language queries + agent insights |
| **Copilot agent** | Autonomous Security Copilot component — proactively surfaces insights |
| **Built-in reports** | Pre-configured Intune reports — compliance, inventory, updates, apps |
| **Custom reports** | Filtered and saved report configurations for repeated use |
| **Workbooks** | Visual dashboards built on Azure Monitor data |
| **Alert** | Event-triggered notification — enrollment failure, policy conflict, non-compliance |
| **ITSM integration** | Connecting Intune alerts to IT service management tools via Power Automate |
| **Azure Automation Runbook** | Scheduled cloud-based script execution — runs PowerShell on a schedule |

---

## Quick Revision Card

```
POWERSHELL + MICROSOFT GRAPH
├── Graph = REST API for all Intune data
├── Connect-MgGraph → authenticate → pull data → export/email
├── Automate: compliance reports, device inventory, stale device cleanup
└── Schedule via: Azure Automation Runbook, Task Scheduler

POWERSHELL SCRIPTS IN INTUNE
├── Deploy scripts directly to Windows devices
├── Use for: registry keys, software detection, config beyond Settings Catalog
└── Run as System or User context

PROACTIVE REMEDIATIONS
├── Two scripts: detection (exit 0/1) + remediation
├── Runs on schedule — only remediates when issue detected
├── Self-healing devices — no helpdesk call needed
└── Reports: without issue / remediated / failed

ENDPOINT ANALYTICS
├── Fleet-wide health: startup, app reliability, battery, resource
├── Composite score 0–100 — benchmarked against similar orgs
├── Requires: Intune Plan 2 or Intune Suite
└── Historical trends (vs KQL device query = live single device)

SECURITY COPILOT
├── Natural language queries about device risk, compliance, users
├── Copilot agents: proactively surface insights without asking
├── Key difference: act directly from same interface
└── vs standard report: report shows data, Copilot enables action

REPORTING + ALERTING
├── Scheduled reports: board/audit facing, consistent, CSV export
├── Real-time alerts: enrollment failure, policy conflict, non-compliance
├── Delivery: email, in-portal, ITSM integration via Power Automate
└── All reports powered by Microsoft Graph underneath

EXAM TRIGGERS
├── "Automated report without manual intervention" → PowerShell + Graph
├── "Self-healing devices" → Proactive remediations (2 scripts)
├── "Proactively identify slow devices" → Endpoint Analytics
├── "Natural language + act immediately" → Security Copilot agents
└── "Instant helpdesk notification" → Intune alerts + notifications
```

---

*MD-102 Study Notes | Domain 5: Optimize Endpoint Operations*
*Last updated: 2026*
