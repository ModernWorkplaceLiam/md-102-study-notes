# MD-102 Study Notes — Intune Suite Add-On Capabilities
> Domain 2: Manage and Maintain Devices (30–35% of exam)

---

## Table of Contents
1. [Core Concept — What Is the Intune Suite?](#1-core-concept--what-is-the-intune-suite)
2. [Endpoint Privilege Management (EPM)](#2-endpoint-privilege-management-epm)
3. [Remote Help](#3-remote-help)
4. [Advanced Analytics](#4-advanced-analytics)
5. [Microsoft Tunnel for MAM](#5-microsoft-tunnel-for-mam)
6. [Cloud PKI](#6-cloud-pki)
7. [Exam Scenario Practice](#7-exam-scenario-practice)
8. [Key Terms Glossary](#8-key-terms-glossary)

---

## 1. Core Concept — What Is the Intune Suite?

The Intune Suite is a collection of **premium add-on capabilities** that sit on
top of core Microsoft Intune. They are not included in standard Intune licensing
and must be purchased separately — either individually or as a bundle.

### The Five Capabilities

| Feature | Problem it solves | Who benefits |
|---|---|---|
| **EPM** | Standard users need temporary admin rights | End users + IT |
| **Remote Help** | IT needs to see/control remote devices | IT helpdesk |
| **Advanced Analytics** | IT needs deeper device health insights | IT management |
| **Tunnel for MAM** | BYOD users need secure app access without enrollment | End users |
| **Cloud PKI** | Cloud devices need certificates without on-prem CA | IT infrastructure |

> ⚠️ **Exam note:**
> The exam tests what each feature does and which scenario it solves.
> It does not test pricing or specific licensing SKUs.

---

## 2. Endpoint Privilege Management (EPM)

### The Problem It Solves

Standard users run without admin rights for security — but occasionally need
elevated privileges for specific tasks. The traditional solutions are bad:

```
Give permanent admin rights  →  Too risky — user can do anything
Call IT for every elevation  →  Slow, expensive, frustrating
```

EPM provides **just-in-time, task-specific elevation** — the minimum privilege
needed, only when needed, only for that specific application.

### The Security Principle

```
Least privilege  →  Users get only the rights needed for their job
Just-in-time     →  Elevation is temporary, not permanent
Audited          →  Every elevation is logged — who, what, when, why
```

### The Three Elevation Types

#### Type 1 — Automatic Elevation
```
How it works:   IT pre-approves specific apps via EPM policy
                App runs elevated automatically — no user prompt
User experience: Seamless — user doesn't notice the elevation
Best for:       Known, trusted apps that always need elevation
Example:        Corporate software that always requires admin to run
```

#### Type 2 — User Confirmed Elevation
```
How it works:   App is pre-approved but user must confirm each time
                User sees a prompt — must acknowledge before proceeding
                Business justification may be required
User experience: One extra click — minimal friction
Best for:       Apps that need elevation occasionally, not always
Example:        Driver installer that IT has approved but wants user awareness
```

#### Type 3 — Support Approved Elevation
```
How it works:   User requests elevation for an app NOT on the approved list
                Request goes to IT helpdesk for review
                User provides business justification
                IT approves or denies — user gets a time-limited elevation
User experience: Requires helpdesk interaction — adds delay
Best for:       One-off, unanticipated elevation needs
Example:        Specialist lab equipment USB driver — one-time installation
```

### EPM vs PIM — The Distinction

```
EPM (Endpoint Privilege Management)
→  Just-in-time APP elevation
→  Standard user running one specific app with admin rights
→  Lives in: Windows device, managed by Intune

PIM (Privileged Identity Management)
→  Just-in-time ROLE elevation
→  IT admin gaining temporary Entra ID admin role
→  Lives in: Entra ID, manages admin roles
```

Same security principle — different scope and layer.

### Exam Scenario Pattern for EPM

```
Trigger phrases in exam questions:
- "standard user needs to install..."
- "user needs admin rights just for..."
- "without giving permanent admin access..."
- "least privilege..."

Answer structure:
→  Feature: Endpoint Privilege Management (EPM)
→  Elevation type: depends on whether app is pre-approved or not
   Pre-approved app   →  Automatic or User Confirmed
   Unapproved app     →  Support Approved
```

---

## 3. Remote Help

### The Problem It Solves

IT needs to troubleshoot and fix issues on remote devices — without the user
having to physically bring the device in or describe what they're seeing.

### How It Works

```
Prerequisites:
  Both IT admin AND end user must have Remote Help app installed
  Session is always user-initiated and user-consented
  IT cannot silently connect without user knowledge

Session flow:
  User opens Remote Help → shares a session code → IT admin joins
  IT selects session mode → troubleshooting begins
```

### The Two Session Modes

#### View Only
```
What IT can do:   Watch the user's screen in real time
What IT cannot:   Control mouse or keyboard
Best for:         Diagnosing issues by watching the user reproduce them
                  Understanding the user's workflow before intervening
Privacy:          User retains full control of their device
```

#### Full Control
```
What IT can do:   Take over mouse and keyboard completely
                  Run command prompt, install/uninstall software
                  Access admin panels and system settings
                  Elevate their session for admin tasks
Best for:         Actively fixing issues once diagnosed
Privacy:          User can see everything IT is doing
```

### The Recommended Sequence

```
Step 1  →  View only: watch user reproduce the issue — diagnose the cause
Step 2  →  Full control: IT takes over to implement the fix
Step 3  →  View only: hand back to user — confirm issue resolved
```

### Key Exam Facts

```
✅ Both IT and user need Remote Help app installed
✅ Session is user-consented — not silent
✅ Available for: Windows, macOS, iOS, Android
✅ IT can elevate within a full control session for admin tasks
✅ Sessions are audited — logged in Intune
⚠️ Not the same as other remote tools (TeamViewer, RDP) — Intune-integrated
```

---

## 4. Advanced Analytics

### The Problem It Solves

Standard Intune reporting shows compliance status and enrollment data.
Advanced Analytics provides **proactive device health intelligence** — identifying
problems before they become helpdesk calls.

### What It Provides

```
Device health scores     →  Overall health rating per device and fleet
Startup performance      →  Boot time analysis — which devices are slow to start
Battery health           →  Battery degradation tracking across the fleet
App reliability          →  Crash rates per application, per device model
Resource performance     →  CPU, RAM, storage utilisation trends
Anomaly detection        →  Flags devices behaving unusually vs their baseline
```

### Breakdown Dimensions

Advanced Analytics can segment insights by:
```
Manufacturer    →  Dell vs HP vs Lenovo performance comparison
Model           →  Specific model lines with performance issues
Firmware        →  Firmware versions correlated with problems
OS version      →  Build-level performance differences
```

### Device Queries with KQL

KQL = **Kusto Query Language** — the same language used in Microsoft Sentinel
and Azure Log Analytics.

```
What standard dashboards give you:   Pre-built reports with fixed parameters
What KQL device queries give you:    Custom questions against raw device telemetry
```

#### KQL Query Examples

```
Find all Dell devices with camera issues in last 30 days:
→  Filter: Manufacturer = Dell
   Filter: ComponentName = Camera
   Filter: TimeRange = last 30 days

Find devices with startup time > 60 seconds on Windows 11 23H2:
→  Filter: OSBuild = 22631
   Filter: StartupDurationSeconds > 60

Find all devices with battery health below 50%:
→  Filter: BatteryHealthPercentage < 50
```

### Exam Scenario Pattern for Advanced Analytics

```
Trigger phrases:
- "IT wants to identify which devices are causing most helpdesk calls"
- "find all [manufacturer] devices with [specific issue]"
- "proactive health monitoring..."
- "custom query against device data..."

Answer:
→  Feature: Advanced Analytics
→  Specific capability: KQL device queries (for custom, specific queries)
                        or Advanced Analytics dashboards (for overview)
```

---

## 5. Microsoft Tunnel for MAM

### The Problem It Solves

Standard VPN requires full device enrollment in Intune.
BYOD users on personal devices often cannot or will not enroll their devices.
Yet they still need secure access to corporate resources like SharePoint and Teams.

Additionally — routing ALL traffic through a corporate VPN on a personal device
is a privacy concern. Personal Netflix and Instagram should not go through the
company network.

### The Solution

**Per-app VPN tunnel** that works on **unenrolled devices**.

```
Work apps (Outlook, Teams, SharePoint)  →  Traffic routes through Tunnel
Personal apps (Netflix, Instagram)      →  Traffic bypasses Tunnel entirely
```

### MAM vs MDM

```
MDM (Mobile Device Management)   →  Full device enrollment required
                                     IT manages the whole device

MAM (Mobile Application Management) →  No enrollment required
                                        IT manages only the work apps
                                        Tunnel for MAM uses this approach
```

### How It Works

```
Microsoft Tunnel Gateway
    └── Deployed by IT in Azure or on-premises
    └── Acts as the VPN endpoint for work app traffic

Intune App Protection Policy
    └── Configured to route specific apps through Tunnel
    └── Applied to unenrolled BYOD devices via MAM

User experience
    └── Work apps automatically use Tunnel — transparent to user
    └── Personal apps unaffected — normal internet connection
```

### Key Exam Facts

```
✅ Works on UNENROLLED devices — key differentiator
✅ Per-app tunnel — not whole-device VPN
✅ Requires Microsoft Tunnel Gateway deployment
✅ Supported on: iOS and Android (not Windows)
✅ Works alongside App Protection Policies (MAM)
⚠️ Not the same as standard Intune VPN profile — that requires enrollment
```

---

## 6. Cloud PKI

### The Problem It Solves

Many enterprise services authenticate devices using **digital certificates**
rather than passwords — Wi-Fi networks, VPNs, internal web services.

Traditionally, a **Certificate Authority (CA) server** on-premises issues these
certificates. But cloud-only and remote devices have no line of sight to
on-premises infrastructure — they can't reach the CA server to get a certificate.

### The Solution

Move the Certificate Authority to the cloud — **Cloud PKI** is a fully
cloud-based CA managed through Intune.

```
On-premises CA                          Cloud PKI
──────────────────────────────          ──────────────────────────────
Physical server on corporate network    Hosted in Microsoft cloud
Devices must reach the network          Any Intune device can get a cert
Manual certificate management           Automated via Intune policies
Infrastructure to maintain              No server to manage
Cannot reach remote/cloud devices       Reaches any enrolled device
```

### What Certificates Are Used For

```
Wi-Fi authentication     →  Device proves identity to join corporate Wi-Fi
                             No password needed — certificate is the credential
VPN authentication       →  Secure tunnel established using certificate identity
Internal web services    →  HTTPS authentication to internal sites
Device identity          →  Proves the device is trusted and managed
```

### How It Works with Intune

```
Cloud PKI configured in Intune admin centre
        │
        ▼
Certificate profile created in Intune
(SCEP or PKCS profile — delivers cert to device)
        │
        ▼
Profile assigned to device group
        │
        ▼
Device receives certificate automatically
No manual request — fully automated
        │
        ▼
Device uses certificate to authenticate
to Wi-Fi, VPN, or other services
```

### Key Exam Facts

```
✅ Replaces on-premises CA server entirely
✅ Fully managed through Intune — no separate infrastructure
✅ Certificates delivered via SCEP or PKCS certificate profiles
✅ Works with: Wi-Fi profiles, VPN profiles, device identity
⚠️ Exam trigger: "no on-premises infrastructure" + "certificates" = Cloud PKI
```

---

## 7. Exam Scenario Practice

### Scenario 1
> A standard user needs to install a USB driver for specialist lab equipment.
> IT doesn't want to give permanent admin rights. The driver isn't on the
> pre-approved list. Which EPM elevation type applies?

**Answer:** **Support approved elevation**. User submits elevation request with
business justification. IT helpdesk reviews and approves a time-limited elevation
for that specific installer. No permanent admin rights granted.

---

### Scenario 2
> A remote worker in Scotland has a broken Outlook profile. IT needs to first
> watch her reproduce the sending issue, then fix the mail profile settings.
> Which feature and session modes are used?

**Answer:** **Remote Help**. IT starts in **view only** mode to watch the user
reproduce the issue and diagnose the cause. IT then switches to **full control**
to take over keyboard and mouse and fix the Outlook profile settings.

---

### Scenario 3
> IT notices high helpdesk call volumes about slow startup on Dell Latitude
> laptops running a specific firmware. They want to confirm the pattern across
> all 200 Dell devices before escalating to Dell support.

**Answer:** **Advanced Analytics** using **KQL device queries**. Query filters
on manufacturer = Dell, model = Latitude, firmware version, and startup
performance metrics — confirming the pattern with data before escalation.

---

### Scenario 4
> 300 employees use personal Android phones to access Teams and SharePoint.
> IT wants work app traffic routed securely through the corporate network.
> Personal apps should use normal internet. Device enrollment is not required.

**Answer:** **Microsoft Tunnel for MAM**. Per-app VPN routes only work app
traffic through the Tunnel Gateway. Personal apps bypass it entirely.
No device enrollment needed — works via MAM App Protection Policies.

---

### Scenario 5
> A company is going fully cloud-native. All devices are Intune-managed with
> no on-premises infrastructure. They need devices to authenticate to corporate
> Wi-Fi using certificates instead of passwords — but have no on-premises CA.

**Answer:** **Cloud PKI**. Cloud-based Certificate Authority managed through
Intune. Issues certificates to enrolled devices automatically via SCEP/PKCS
certificate profiles. No on-premises CA server required.

---

## 8. Key Terms Glossary

| Term | Plain English Definition |
|---|---|
| **Intune Suite** | Premium add-on bundle of five advanced Intune capabilities |
| **EPM** | Endpoint Privilege Management — just-in-time app elevation for standard users |
| **Least privilege** | Security principle — users get only the minimum rights needed |
| **Automatic elevation** | EPM type — pre-approved app elevates without user prompt |
| **User confirmed elevation** | EPM type — pre-approved app requires user to acknowledge prompt |
| **Support approved elevation** | EPM type — unapproved app requires helpdesk review and approval |
| **PIM** | Privileged Identity Management — just-in-time role elevation in Entra ID (different from EPM) |
| **Remote Help** | Intune-integrated remote assistance tool — view only and full control modes |
| **View only** | Remote Help mode — IT watches screen, user retains control |
| **Full control** | Remote Help mode — IT takes over mouse and keyboard |
| **Advanced Analytics** | Premium Intune reporting — device health scores, performance insights |
| **KQL** | Kusto Query Language — used for custom device queries in Advanced Analytics |
| **Tunnel for MAM** | Per-app VPN for unenrolled BYOD devices — routes only work app traffic |
| **MAM** | Mobile Application Management — manages apps without device enrollment |
| **MDM** | Mobile Device Management — full device management requiring enrollment |
| **Per-app VPN** | VPN that only applies to specific apps — not all device traffic |
| **Cloud PKI** | Cloud-based Certificate Authority — issues device certs without on-prem server |
| **CA** | Certificate Authority — system that issues and manages digital certificates |
| **SCEP** | Simple Certificate Enrollment Protocol — automated cert delivery method |
| **PKCS** | Public Key Cryptography Standards — certificate format used in Intune |
| **Certificate profile** | Intune profile that delivers a certificate to a device |

---

## Quick Revision Card

```
FIVE INTUNE SUITE FEATURES
├── EPM           →  Standard user needs temp admin rights
│   ├── Automatic elevation      Pre-approved, silent
│   ├── User confirmed           Pre-approved, user confirms
│   └── Support approved         Not pre-approved, helpdesk reviews
│
├── Remote Help   →  IT remotely assists user devices
│   ├── View only               Watch and diagnose
│   └── Full control            Take over and fix
│
├── Advanced Analytics  →  Proactive device health intelligence
│   └── KQL queries            Custom questions against device telemetry
│
├── Tunnel for MAM  →  Per-app VPN for UNENROLLED BYOD devices
│   ├── No enrollment needed
│   └── Only work apps tunnel — personal apps bypass
│
└── Cloud PKI  →  Cloud CA — certificates without on-prem server
    ├── Replaces on-premises Certificate Authority
    └── Automated cert delivery via SCEP/PKCS profiles

KEY DISTINCTIONS
├── EPM vs PIM:    EPM = app elevation | PIM = role elevation
├── MAM vs MDM:    MAM = app management only | MDM = full device
└── Tunnel for MAM vs VPN profile: MAM = no enrollment | VPN = enrollment required
```

---

*MD-102 Study Notes | Domain 2: Manage and Maintain Devices | Intune Suite Add-Ons*
*Last updated: 2026*
