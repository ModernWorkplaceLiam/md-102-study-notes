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
7. [Quick Comparison — All Five Features](#7-quick-comparison--all-five-features)
8. [Exam Scenario Practice](#8-exam-scenario-practice)
9. [Key Terms Glossary](#9-key-terms-glossary)

---

## 1. Core Concept — What Is the Intune Suite?

The Intune Suite is a collection of **premium add-on capabilities** built on top
of core Microsoft Intune. Each feature solves a specific problem that standard
Intune licensing does not address.

```
Core Intune          →   Enrol devices, push policies, deploy apps, compliance
Intune Suite add-ons →   Advanced scenarios: privilege management, remote support,
                         deep analytics, BYOD VPN, cloud certificates
```

> ⚠️ **Exam note:**
> The exam tests what each feature does and which problem it solves —
> not licensing costs or pricing tiers.
> All five are premium features not included in standard Intune.

### The Five Features at a Glance

| Feature | Problem it solves | Who benefits |
|---|---|---|
| EPM | Standard users needing temporary admin rights | End users + IT security |
| Remote Help | IT supporting remote users without physical access | IT helpdesk |
| Advanced Analytics | Understanding device health and performance at scale | IT operations |
| Tunnel for MAM | Secure corporate access from unenrolled BYOD devices | Remote / BYOD workers |
| Cloud PKI | Certificate issuance without on-premises CA server | Cloud-first organisations |

---

## 2. Endpoint Privilege Management (EPM)

### The Problem
Standard users run without admin rights — a security best practice.
But occasionally they need elevated privileges for a specific task
(installing a driver, running a specialist tool) without IT granting
permanent admin access.

### The Solution
EPM allows standard users to run specific applications with elevated
(admin) privileges — temporarily, for that task only, with full audit logging.

```
Security principle:  Least privilege
                     Users get minimum rights needed
                     Only when needed
                     Only for that specific task
                     Elevation is temporary and audited
```

### The Three Elevation Types

#### Type 1 — Automatic Elevation
```
What happens:   IT pre-approves specific apps via EPM policy
                App runs with elevated rights automatically
                No user action or prompt required
Best for:       Known, trusted apps that always need elevation
                e.g. a specific corporate tool that requires admin rights
User experience: Seamless — elevation is invisible to the user
```

#### Type 2 — User Confirmed
```
What happens:   App is pre-approved by IT
                User must explicitly confirm the elevation via a prompt
                May require business justification text
Best for:       Apps that need elevation but IT wants user awareness
                e.g. software update tools
User experience: User sees a confirmation prompt before elevation proceeds
```

#### Type 3 — Support Approved
```
What happens:   User requests elevation for an app NOT on the pre-approved list
                User submits request with business justification
                IT helpdesk reviews and approves or denies the request
                Elevation is granted only after IT approval
Best for:       One-off or unusual elevation requirements
                e.g. installing a USB driver for specialist lab equipment
User experience: User submits request → waits for IT approval → proceeds
```

### EPM vs PIM — Important Distinction

```
EPM (Endpoint Privilege Management)
    →  Just-in-time APP elevation
    →  Standard user running ONE app as admin on their device
    →  Managed through Intune

PIM (Privileged Identity Management)
    →  Just-in-time ROLE elevation
    →  IT admin gaining temporary Entra ID admin rights
    →  Managed through Entra ID

Same security principle (least privilege, JIT) — different scope and layer.
```

### EPM Exam Scenario Pattern
```
Trigger words:  "standard user", "one-off", "without permanent admin",
                "specific app", "least privilege"
Answer:         Endpoint Privilege Management (EPM)
Elevation type: Match to scenario —
                Pre-approved + automatic    →  Automatic elevation
                Pre-approved + user prompt  →  User confirmed
                Not pre-approved + IT reviews →  Support approved
```

---

## 3. Remote Help

### The Problem
Remote workers have technical issues IT cannot fix without physical access.
Traditional remote tools lack Intune integration, compliance checking,
and the controlled consent model required by modern IT security policies.

### The Solution
Remote Help is an Intune-integrated remote assistance tool that allows
IT to view or control an end user's device — with full user consent
and audit logging.

### The Two Session Modes

#### View Only
```
What IT can do:   Watch the user's screen in real time
                  See exactly what the user is doing step by step
                  Diagnose the issue without touching the device
User control:     User retains full control of mouse and keyboard
Best for:         Diagnosing user behaviour issues
                  Understanding what the user is experiencing
                  Privacy-sensitive situations
```

#### Full Control
```
What IT can do:   Take control of mouse and keyboard
                  Run Command Prompt, PowerShell as admin
                  Install or uninstall software
                  Access admin panels and settings
User control:     User can see everything IT does — transparent
Best for:         Actively fixing issues — broken profiles, software,
                  configuration problems
```

### Typical Session Flow
```
Step 1  →  User calls helpdesk with issue
Step 2  →  IT initiates Remote Help session — user receives consent prompt
Step 3  →  User accepts — session begins in View Only mode
Step 4  →  IT diagnoses the issue by watching the user
Step 5  →  IT switches to Full Control to fix the problem
Step 6  →  Session ends — full audit log recorded
```

### Key Exam Facts
```
✅ Both IT admin AND end user must have Remote Help app installed
✅ Session is always user-initiated and user-consented
✅ IT cannot silently connect — user must approve every session
✅ IT can elevate their session during Full Control to run admin tasks
✅ Full audit trail of every session — who connected, when, what was done
✅ Works with Intune-enrolled devices — compliance status visible to IT
```

---

## 4. Advanced Analytics

### The Problem
Standard Intune reporting shows compliance status and policy assignment.
It doesn't explain WHY devices are slow, crashing, or generating helpdesk
tickets — or spot patterns across manufacturers, models and firmware versions.

### The Solution
Advanced Analytics provides deep device health intelligence — battery health
scores, startup performance, app reliability, crash analysis — all surfaced
as actionable insights rather than raw data.

### What Advanced Analytics Provides

```
Device health scores      →  Overall health rating per device
Startup performance       →  Boot time analysis — identify slow devices
Battery health            →  Battery degradation tracking — predict failures
App reliability           →  Crash frequency and error analysis per application
Resource performance      →  CPU, RAM, storage performance trends
Manufacturer / model view →  Spot patterns across hardware makes and models
Firmware analysis         →  Identify firmware versions causing issues
```

### Device Queries Using KQL

KQL = **Kusto Query Language** — the same query language used in
Microsoft Sentinel and Azure Log Analytics.

```
Pre-built dashboards   →  Answer known questions with fixed reports
KQL device queries     →  Answer YOUR specific questions of the data
```

**Why KQL matters:**
Pre-built reports show average startup time across all devices.
KQL lets you ask: *"Show me all Dell Latitude laptops running firmware
version X that have had startup times over 60 seconds in the last 30 days."*

**Exam-relevant KQL example:**
```
DeviceHealth
| where Manufacturer == "Dell"
| where Model contains "Latitude"
| where StartupTime > 60
| where ReportDate > ago(30d)
| summarize count() by FirmwareVersion
```

> ⚠️ **Exam note:**
> You don't need to write KQL for the exam — you need to know that
> KQL device queries exist within Advanced Analytics and what they enable:
> custom, flexible querying of device telemetry beyond pre-built reports.

---

## 5. Microsoft Tunnel for MAM

### The Problem
BYOD users need secure access to corporate resources (SharePoint, Teams,
internal apps) from personal devices. Standard VPN routes ALL device traffic
through the corporate network — including personal apps — which is both
a privacy concern and a bandwidth overhead.

Additionally, some users haven't enrolled their devices in Intune at all —
they only use managed apps (MAM without enrollment). Traditional VPN
requires full device enrollment to function.

### The Solution
Microsoft Tunnel for MAM provides a **per-app VPN tunnel** that:
- Routes only work app traffic through the corporate network
- Leaves personal app traffic on the user's normal internet connection
- Works on **unenrolled devices** — no MDM enrollment required

### How It Works

```
Without Tunnel for MAM:
Personal device → ALL traffic → Corporate VPN → Internet
(personal Netflix + work SharePoint both route through company network)

With Tunnel for MAM:
Personal device → Work apps (Teams, SharePoint) → Tunnel → Corporate network
                → Personal apps (Netflix, Instagram) → Normal internet
(split at app level — not device level)
```

### Key Exam Facts

```
✅ Works on UNENROLLED devices — this is the key differentiator
✅ Per-app tunnel — only Intune-managed apps use it
✅ Personal apps bypass it entirely — privacy preserved
✅ Requires Microsoft Tunnel Gateway (can be on-prem or in Azure)
✅ Configured via App Protection Policies in Intune
✅ Supported on iOS and Android
```

### MAM vs MDM — The Context

```
MDM (Mobile Device Management)  →  Device enrolled, Intune manages the whole device
MAM (Mobile Application Management) →  Apps managed, device NOT enrolled
Tunnel for MAM                  →  VPN that works with MAM — no enrollment needed
```

> ⚠️ **Exam scenario trigger:**
> "BYOD", "unenrolled", "per-app VPN", "personal device secure access"
> → Answer: Microsoft Tunnel for MAM

---

## 6. Cloud PKI

### The Problem
Device certificates prove identity — used for Wi-Fi authentication, VPN
access, and device identity verification. Certificates are issued by a
Certificate Authority (CA) server.

In traditional environments, the CA server is on-premises. Cloud-only or
remote Intune-managed devices have **no line of sight** to the on-premises
CA — they can't request or receive certificates.

### The Solution
Cloud PKI is a fully cloud-based Certificate Authority managed through
Intune. It issues certificates to any Intune-managed device — regardless
of whether that device ever connects to the corporate network.

### How It Works

```
Traditional (on-premises CA):
Device → requests certificate → on-prem CA server
Problem: remote / cloud devices can't reach the on-prem server

Cloud PKI:
Device → requests certificate → Cloud PKI (in Azure / Intune)
Result: any Intune-managed device gets certificates automatically
        No network line-of-sight required
        No on-premises server to maintain
```

### Use Cases for Device Certificates

```
Wi-Fi authentication    →  Certificate proves device identity to Wi-Fi controller
                           No username/password needed for corporate Wi-Fi
VPN authentication      →  Certificate-based VPN — more secure than passwords
Device identity         →  Conditional Access can require a valid certificate
SCEP / PKCS profiles    →  Intune delivers certificates via these profile types
```

### Key Exam Facts

```
✅ Replaces on-premises CA server entirely
✅ Fully managed through Microsoft Intune admin centre
✅ Issues certificates to Intune-managed devices automatically
✅ Supports SCEP and PKCS certificate delivery via Intune profiles
✅ Removes dependency on corporate network connectivity for cert issuance
✅ PKI = Public Key Infrastructure — the system for managing certificates
```

> ⚠️ **Exam scenario trigger:**
> "No on-premises CA", "cloud-only", "certificates without infrastructure",
> "remote devices need certificates"
> → Answer: Cloud PKI

---

## 7. Quick Comparison — All Five Features

| Feature | Trigger words in exam scenarios | Key fact |
|---|---|---|
| **EPM** | "standard user", "temporary elevation", "one-off admin task", "least privilege" | Three types: automatic, user confirmed, support approved |
| **Remote Help** | "remote support", "take control", "see user's screen", "helpdesk" | Two modes: view only + full control. User must consent |
| **Advanced Analytics** | "device health", "performance patterns", "slow devices", "battery", "KQL" | KQL enables custom queries beyond pre-built dashboards |
| **Tunnel for MAM** | "BYOD", "unenrolled", "per-app VPN", "personal device" | Only work apps tunnel — personal apps bypass it |
| **Cloud PKI** | "certificates", "no on-prem CA", "cloud-only", "line of sight" | Cloud CA — any Intune device gets certs, no network needed |

---

## 8. Exam Scenario Practice

### Scenario 1
> A standard user needs to install a USB driver for specialist lab equipment.
> It's a one-off requirement. The driver isn't on the pre-approved app list.
> IT doesn't want to give permanent admin rights.

**Answer:** **EPM — support approved elevation.** User submits request with
business justification. IT helpdesk reviews and approves. Elevation is
granted temporarily for that specific installer only.

---

### Scenario 2
> A remote worker in another city has a broken Outlook profile. IT needs to
> first see what happens when she clicks send, then fix the mail settings directly.

**Answer:** **Remote Help** — starting in **view only** mode to observe the
user's behaviour and diagnose the issue, then switching to **full control**
to fix the mail profile settings directly.

---

### Scenario 3
> The helpdesk is receiving high volumes of slow startup complaints from Dell
> Latitude users. The IT manager suspects a specific firmware version.
> They want to confirm the pattern across all Dell devices before escalating.

**Answer:** **Advanced Analytics** using **KQL device queries** — query device
telemetry filtered to Dell Latitude devices, grouping slow startup events
by firmware version to confirm the pattern.

---

### Scenario 4
> 300 BYOD Android users access SharePoint and Teams from personal phones.
> IT wants work app traffic to route through the corporate network securely.
> Personal apps must not be affected. Device enrollment is not required.

**Answer:** **Microsoft Tunnel for MAM** — per-app VPN tunnel routes only
Intune-managed work apps through the corporate network. Personal apps
use the user's normal internet connection. No device enrollment needed.

---

### Scenario 5
> A company is going fully cloud-based with no on-premises infrastructure.
> They need devices to receive certificates for Wi-Fi authentication,
> but have no on-premises CA server.

**Answer:** **Cloud PKI** — cloud-based Certificate Authority managed through
Intune. Issues certificates to any Intune-managed device without requiring
network line-of-sight to an on-premises CA server.

---

## 9. Key Terms Glossary

| Term | Plain English Definition |
|---|---|
| **Intune Suite** | Premium add-on bundle for Microsoft Intune — advanced capabilities beyond core licensing |
| **EPM** | Endpoint Privilege Management — temporary app elevation for standard users |
| **Least privilege** | Security principle — users get minimum rights needed, only when needed |
| **Automatic elevation** | EPM type — pre-approved app elevates silently, no user prompt |
| **User confirmed** | EPM type — pre-approved app, user must confirm elevation via prompt |
| **Support approved** | EPM type — unapproved app, IT helpdesk reviews and approves request |
| **PIM** | Privileged Identity Management — JIT role elevation for IT admins in Entra ID |
| **Remote Help** | Intune-integrated remote assistance tool — view only or full control |
| **View only** | Remote Help mode — IT watches user's screen, user retains control |
| **Full control** | Remote Help mode — IT takes mouse and keyboard to fix issues |
| **Advanced Analytics** | Intune add-on providing deep device health and performance intelligence |
| **KQL** | Kusto Query Language — used for custom device queries in Advanced Analytics |
| **Tunnel for MAM** | Per-app VPN for unenrolled BYOD devices — work apps only |
| **MAM** | Mobile Application Management — manages apps without device enrollment |
| **Per-app VPN** | VPN tunnel that applies only to specific managed apps — not all device traffic |
| **Cloud PKI** | Cloud-based Certificate Authority managed through Intune |
| **PKI** | Public Key Infrastructure — system for issuing and managing digital certificates |
| **CA** | Certificate Authority — server that issues digital certificates |
| **SCEP** | Simple Certificate Enrollment Protocol — method Intune uses to deliver certificates |
| **Line of sight** | Network connectivity between a device and a server — required for on-prem CA |

---

## Quick Revision Card

```
EPM — ENDPOINT PRIVILEGE MANAGEMENT
├── Solves: standard users needing one-off admin rights
├── Automatic: pre-approved, silent elevation
├── User confirmed: pre-approved, user must confirm
├── Support approved: not pre-approved, IT reviews request
└── Principle: least privilege — temporary, audited, specific

REMOTE HELP
├── Solves: IT supporting remote users without physical access
├── View only: IT watches — user retains control
├── Full control: IT takes mouse + keyboard
├── User must consent to every session
└── Both parties need the Remote Help app installed

ADVANCED ANALYTICS
├── Solves: understanding WHY devices underperform at scale
├── Provides: health scores, startup, battery, app crashes
├── KQL queries: custom questions beyond pre-built reports
└── Example: all Dell Latitude devices with slow startup in 30 days

TUNNEL FOR MAM
├── Solves: secure BYOD access without device enrollment
├── Per-app: only work apps tunnel — personal apps bypass
├── No enrollment required — MAM only
└── Trigger words: BYOD, unenrolled, per-app VPN

CLOUD PKI
├── Solves: certificate issuance without on-prem CA server
├── Cloud CA: issues certs to any Intune device
├── No line of sight needed to corporate network
└── Use cases: Wi-Fi auth, VPN auth, device identity
```

---

*MD-102 Study Notes | Domain 2: Manage and Maintain Devices | Intune Suite Add-Ons*
*Last updated: 2026*
