# MD-102 Study Notes — Enrollment Methods
> Domain 1: Prepare Infrastructure for Devices (25–30% of exam)

---

## Table of Contents
1. [Core Concept — What Is Enrollment?](#1-core-concept--what-is-enrollment)
2. [Entra ID Join Types](#2-entra-id-join-types)
3. [Windows Enrollment](#3-windows-enrollment)
4. [Windows Autopilot — All Four Modes](#4-windows-autopilot--all-four-modes)
5. [iOS Enrollment](#5-ios-enrollment)
6. [Android Enrollment Profiles](#6-android-enrollment-profiles)
7. [Bulk Enrollment](#7-bulk-enrollment)
8. [Decision Framework — Choosing the Right Method](#8-decision-framework--choosing-the-right-method)
9. [Exam Scenario Practice](#9-exam-scenario-practice)
10. [Key Terms Glossary](#10-key-terms-glossary)

---

## 1. Core Concept — What Is Enrollment?

### The One-Sentence Version
Enrollment is the process of creating an **active management relationship** between a device and Microsoft Intune — so that IT can push policies, apps, and security settings remotely.

### The Analogy
Think of enrollment like an **employment contract**:
- The **hardware hash / serial number** = the job offer letter (device is recognised before it starts)
- **Enrollment** = signing the contract (the active management relationship begins)
- **Intune policies** = the employee handbook (rules that apply once the contract is signed)

> ⚠️ **Critical exam distinction:**
> Uploading a hardware hash registers the device for future Autopilot rebuilds.
> It does NOT enrol the device or push policies. Enrollment is a separate step.

---

## 2. Entra ID Join Types

Before a device can be enrolled in Intune, it needs an **identity** in Microsoft Entra ID. There are three ways a device can appear in Entra ID — and they are not interchangeable.

### The Three Join Types at a Glance

| Join Type | Who Owns It | IT Control Level | Typical Use Case |
|---|---|---|---|
| **Entra Joined** | Company | Full — Intune manages everything | New work laptops, cloud-only orgs |
| **Hybrid Entra Joined** | Company | Full — co-managed by Intune + on-prem AD | Orgs with legacy on-premises infrastructure |
| **Entra Registered** | Employee (BYOD) | Partial — work apps/data only | Personal phones, BYOD laptops |

### The Badge Analogy

```
Entra Joined        = Staff keycard      → full building access, company device
Hybrid Joined       = Dual-badge         → works in old and new buildings
Entra Registered    = Visitor lanyard    → limited access, personal device
```

### Key Exam Points
- **Joining** = establishing identity in Entra ID
- **Enrollment** = establishing management in Intune
- These are **two separate actions** — a device can be joined without being enrolled
- Entra Registered devices receive **App Protection Policies** not full device policies
- Hybrid Join requires **Microsoft Entra Connect** to sync on-premises AD with Entra ID

---

## 3. Windows Enrollment

### Method 1 — Automatic Enrollment (via Autopilot)
The preferred method for new corporate Windows devices.
- Hardware hash uploaded to Intune before device ships
- User signs in with work account at first boot
- Device automatically Entra joins and Intune enrolls
- Policies and apps deploy without IT touching the device

### Method 2 — Company Portal App (Existing Devices)
Used to enrol **existing Windows devices** that were not set up via Autopilot.

```
Key facts:
✅ Does NOT require a wipe — device enrolls in its current state
✅ Policies begin flowing immediately after enrollment
✅ User installs Company Portal, signs in with work account
⚠️  The device will NOT get Autopilot behaviour unless hash is also uploaded
```

### Method 3 — Group Policy / MDM Auto-Enrolment
Used in **Hybrid environments** where devices are already domain-joined to on-premises AD.
- Configured via Group Policy
- Devices auto-enroll in Intune silently, no user action needed

### Hardware Hash vs Company Portal — The Critical Distinction

```
Hardware Hash Upload
    └── Purpose: Registers device for FUTURE Autopilot rebuilds
    └── Effect on device today: NONE — no policies, no management
    └── Analogy: Adding a student to the admissions list before term

Company Portal Enrollment
    └── Purpose: Creates ACTIVE management relationship RIGHT NOW
    └── Effect on device today: Policies and apps flow immediately
    └── Analogy: The student attending their first day of school
```

---

## 4. Windows Autopilot — All Four Modes

### The Decision Question
> **"Who is doing the setup — and is there a user assigned to this device?"**

That single question determines which mode to use.

### Mode 1 — User-Driven
**The Dell laptop scenario** — user completes setup themselves.

```
Who sets it up:    The end user
Device ownership:  Company
User assigned:     Yes
IT involvement:    Zero physical contact needed
When to use:       Remote workers, devices shipped directly to employees
```

**Flow:**
```
Hash uploaded → IT assigns Autopilot profile → User unboxes device
→ User signs in with work account → Entra join + Intune enrol
→ Enrollment Status Page (ESP) shows progress → Ready to work
```

**Analogy:** A new student enrolling themselves on their first day

---

### Mode 2 — Self-Deploying
**No user involved at all** — device configures itself completely.

```
Who sets it up:    Nobody — fully automatic
Device ownership:  Company
User assigned:     No
IT involvement:    Zero
When to use:       Kiosks, shared devices, digital signage, meeting room screens
```

> ⚠️ **Hardware requirement — exam favourite:**
> Self-deploying mode **requires TPM 2.0**. Without it, the mode will fail.
> The device uses TPM to prove its identity since no user is signing in.

**Analogy:** A vending machine — runs itself, no staff needed, ever

---

### Mode 3 — Pre-Provisioning (White Glove)
**Two phases** — IT pre-loads everything, user just finalises setup.

```
Who sets it up:    IT first, then the user
Device ownership:  Company
User assigned:     Yes
IT involvement:    Technician phase (in office), then ships to user
When to use:       Roles where zero downtime matters — lawyers, doctors, executives
```

**The Two Phases:**

```
Phase 1 — Technician Flow (IT)
    ├── IT powers on device in office
    ├── Selects "Pre-provision" at Windows setup screen
    ├── All apps, policies, and config pre-loaded by Intune
    └── IT reseals device — "ready to ship" state

Phase 2 — User Flow (Employee)
    ├── User receives device, powers it on
    ├── Signs in with their work account
    ├── Personalisation only — everything else already done
    └── Immediately ready to work
```

**Why choose White Glove over User-Driven?**

| | User-Driven | White Glove |
|---|---|---|
| Apps pre-installed | No — installs during user setup | Yes — fully pre-loaded |
| User waits for apps | Yes — can take time | No — ready instantly |
| IT physically handles device | No | Yes — before shipping |
| Best for time-critical roles | No | Yes |

**Analogy:** Builders finish the house completely — you just move your furniture in

---

### Mode 4 — Existing Devices
**For old devices** already in use that need to be migrated into Autopilot.

```
Who sets it up:    IT via Configuration Manager (SCCM)
Device ownership:  Company
User assigned:     Depends on config
IT involvement:    Uses existing SCCM infrastructure
When to use:       Migrating legacy Windows 10 machines to Autopilot management
```

**Analogy:** Renovating an old house to meet new building codes

---

### Autopilot Modes — Quick Reference

```
Has a user?
├── NO  → Self-deploying (check TPM 2.0!)
└── YES → New device or existing?
          ├── EXISTING → Existing Devices mode (via SCCM)
          └── NEW → Speed critical?
                    ├── YES → Pre-provisioning (White Glove)
                    └── NO  → User-Driven
```

---

## 5. iOS Enrollment

### The Problem Autopilot Solves for Windows — ADE Solves for iOS

| Windows | iOS Equivalent |
|---|---|
| Hardware hash | Device serial number |
| Autopilot | Automated Device Enrollment (ADE) |
| Intune | Connected via Apple Business Manager (ABM) |

### Apple Business Manager (ABM)
ABM is Apple's device management portal — the bridge between Apple and Intune.

```
Apple / Reseller
    └── Registers device serial numbers in ABM
    
ABM (Apple Business Manager)
    └── Organisation claims devices by serial number
    └── Syncs to Intune via MDM token
    
Intune
    └── ADE enrollment profile assigned per serial number
    └── User turns on iPhone → auto-enrolls at first boot
    └── Policies and apps deploy automatically
```

### Supervised Mode — The Critical iOS Concept

ADE automatically places devices into **supervised mode**.

```
Supervised mode (ADE)               Unsupervised (Company Portal)
─────────────────────────────────   ──────────────────────────────
Management profile = LOCKED         Management profile = removable
User CANNOT remove management       User CAN unenroll themselves
Deepest level of IT control         Standard management only
Required for strictest policies     Basic policy support only
```

**Supervised-only controls (exam relevant):**
- Block App Store entirely
- Prevent personal Apple ID sign-in
- Lock device to single app (Single App Mode — used for kiosks)
- Block iCloud backup of work data
- Prevent device name changes

> ⚠️ **Exam scenario tip:**
> If a question asks which enrollment method allows IT to **block the App Store** or **prevent profile removal** — the answer is always **ADE (supervised)**.

### When to Use ADE vs Company Portal for iOS

| Scenario | Method | Reason |
|---|---|---|
| Company iPhone for field sales rep | **ADE supervised** | Sensitive data — profile must be locked, user cannot remove management |
| Company iPhone for office worker | **ADE supervised** | Best practice for any company-owned iOS device |
| Employee's personal iPhone for work email | **Company Portal (BYOD)** | Personal device — user retains control |

---

## 6. Android Enrollment Profiles

Android has **four distinct enrollment profiles** — more than any other platform. The key variables are:
1. **Who owns the device** — company or employee?
2. **Is there a specific user** — assigned or shared?
3. **How much personal use is allowed?**

### The Four Profiles

#### Profile 1 — Fully Managed
```
Ownership:      Company
User assigned:  Yes — one specific user
Personal use:   None — work device only
IT control:     Entire device
Best for:       Field workers, warehouse operatives, roles with work-only phones
Analogy:        A company car — you drive it, but IT owns and controls everything
```

#### Profile 2 — Dedicated Device
```
Ownership:      Company
User assigned:  No — shared or unassigned
Personal use:   None
IT control:     Entire device, locked to specific apps
Best for:       Kiosks, till points, shared tablets, front-line shared devices
Analogy:        A self-service machine — no individual owner, locked to one purpose
```

#### Profile 3 — Corporate-Owned Work Profile
```
Ownership:      Company
User assigned:  Yes
Personal use:   Yes — work and personal spaces separated
IT control:     Work side only (but company owns the hardware)
Best for:       Employees who use a company phone but also want personal apps
Analogy:        A company phone with a personal locker built in
```

#### Profile 4 — BYOD Work Profile
```
Ownership:      Employee
User assigned:  Yes — it's their personal device
Personal use:   Yes — work apps sit in a managed container
IT control:     Work container only — personal side is private
Best for:       Employees using personal phones for work email/apps
Analogy:        A work drawer in your personal desk — IT manages the drawer, not the desk
```

### Android Profiles — Side by Side

| Profile | Owns Device | Has User | Personal Apps | IT Controls |
|---|---|---|---|---|
| Fully Managed | Company | Yes | No | Whole device |
| Dedicated Device | Company | No | No | Whole device |
| Corp. Work Profile | Company | Yes | Yes | Work side only |
| BYOD Work Profile | Employee | Yes | Yes | Work container only |

### Why BYOD Work Profile Matters for Security
The work profile container means:
- If the employee leaves → IT can **wipe the work container only**, leaving personal data untouched
- If the device is lost/stolen → IT can **remote wipe work data** without affecting personal photos, messages
- If an account is compromised → IT can **revoke access** to work apps only
- **Data cannot leak** from work apps to personal apps — the container enforces separation

---

## 7. Bulk Enrollment

### The Problem
Enrolling devices one-by-one is impractical at scale. Bulk enrollment methods allow IT to enroll many devices simultaneously — often with no user interaction at all.

### Bulk Enrollment by Platform

#### Windows — Provisioning Package
```
Tool:           Windows Configuration Designer (WCD)
How applied:    USB drive, NFC tap, shared network folder, or SD card
What it does:   Provides device with credentials to connect to Intune
                (Intune then delivers the actual apps and policies)
Best for:       Large Windows deployments without Autopilot
```

#### iOS — Apple Configurator 2
```
Tool:           Apple Configurator 2 (Mac app)
How applied:    USB connection to Mac
What it does:   Enrols multiple iPhones/iPads simultaneously
When to use:    When devices were NOT purchased through ABM (no ADE available)
Note:           ADE via ABM is preferred — Configurator is the fallback
```

#### Android — Enrollment Token / QR Code
```
Tool:           Microsoft Intune admin centre
How applied:    QR code scanned at first boot, or NFC tap
What it does:   Tells device to connect to Intune automatically
Best for:       Dedicated devices — warehouse tablets, kiosks, till points
No user:        Device enrolls as Dedicated Device profile
```

### The Letter of Introduction Analogy

```
Provisioning Package / Token / QR Code
    = The letter of introduction
    = Says "trust this device — it belongs to us"
    = Gets the device through the door

Intune
    = The briefcase that follows
    = Delivers all actual apps, policies, and configuration
    = Does the real work once the introduction is made
```

> ⚠️ **Common exam trap:**
> A provisioning package does NOT contain apps or policies itself.
> It contains the enrollment credentials to connect the device to Intune.
> Intune is what delivers the apps and policies.

---

## 8. Decision Framework — Choosing the Right Method

### Master Scenario Table

| Scenario | Platform | Method | Key Reason |
|---|---|---|---|
| New laptop shipped to employee at home | Windows | **User-Driven Autopilot** | User completes setup, no IT touch |
| Laptop ready instantly — executive, no wait time | Windows | **White Glove Autopilot** | IT pre-loads everything, user just signs in |
| Hospital kiosk, no user, runs one app | Windows / iOS | **Self-Deploying Autopilot / ADE** | No user assigned, automated |
| Existing laptop brought under management | Windows | **Company Portal** | Non-destructive, enrolls in current state |
| Old Windows 10 machines migrated to Intune | Windows | **Autopilot Existing Devices** | Migration path via SCCM |
| Company iPhone for field sales rep | iOS | **ADE supervised** | Sensitive data, profile cannot be removed |
| Employee's personal iPhone for work email | iOS | **BYOD Company Portal** | Personal device, user retains control |
| 40 warehouse Android tablets, shared, no users | Android | **Dedicated Device + QR** | Shared, no user, locked to apps |
| Company Android phone, employee wants personal apps | Android | **Corp. Work Profile** | Company device, work/personal separated |
| Employee's personal Android for work | Android | **BYOD Work Profile** | Personal device, work container only |

### The 3-Question Decision Tree

```
Question 1: Who owns the device?
├── COMPANY → Question 2
└── EMPLOYEE → BYOD (Android Work Profile / iOS Company Portal)

Question 2: Is a specific user assigned?
├── NO → Self-Deploying Autopilot / Android Dedicated Device
└── YES → Question 3

Question 3: Does setup speed / security depth matter critically?
├── YES (field worker, sensitive data, executive) → White Glove / ADE Supervised
└── NO (standard office worker) → User-Driven Autopilot / ADE
```

---

## 9. Exam Scenario Practice

### Scenario 1
> A retail chain is deploying 40 Android tablets as till points. No individual users are assigned. IT cannot visit the store. All tablets need identical configuration.

**Answer:** Android Dedicated Device enrollment using a **QR code / enrollment token**. No user interaction needed — device scans QR at first boot and connects to Intune automatically.

---

### Scenario 2
> A law firm is onboarding 30 lawyers on Monday. Each laptop must have 25 specialist apps fully installed the moment the lawyer opens it — zero setup wait time.

**Answer:** **White Glove (Pre-provisioning) Autopilot**. IT runs the technician flow this week — pre-loading all 25 apps and policies. User-Driven would not work here because specialist apps deploy post-enrollment, causing delays.

---

### Scenario 3
> A field sales rep is leaving the company. They had a company iPhone enrolled via Corporate Work Profile. IT needs to ensure all company data is removed immediately.

**Answer:** IT performs a **selective wipe** via Intune — removing the work profile and all work data. Personal data is unaffected. This is a key advantage of the work profile separation.

---

### Scenario 4
> An employee has an existing Windows laptop that has never been enrolled in Intune. IT needs to bring it under management today without wiping it.

**Answer:** Install the **Company Portal app** and enroll from the device. No wipe required. Policies begin applying immediately. Note: uploading the hardware hash is a separate action for future Autopilot rebuilds — it does not enroll the device.

---

### Scenario 5
> A company wants to deploy iPhones where users absolutely cannot remove the management profile, and IT must be able to block the App Store entirely.

**Answer:** **ADE (Automated Device Enrollment)** via Apple Business Manager. ADE automatically places devices into **supervised mode** — which locks the management profile and unlocks supervised-only controls including App Store blocking.

---

## 10. Key Terms Glossary

| Term | Plain English Definition |
|---|---|
| **Enrollment** | Creating the active management relationship between a device and Intune |
| **Entra ID** | Microsoft's cloud identity system — gives devices and users an identity |
| **Entra Joined** | Company-owned device fully managed by Intune, cloud-only |
| **Hybrid Entra Joined** | Company-owned device managed by both Intune and on-premises AD |
| **Entra Registered** | Personal (BYOD) device with limited work management |
| **Hardware Hash** | Unique fingerprint of a Windows device — used for Autopilot pre-registration |
| **Autopilot** | Microsoft's zero-touch Windows deployment system |
| **User-Driven** | Autopilot mode where the end user completes device setup |
| **Self-Deploying** | Autopilot mode requiring no user — needs TPM 2.0 |
| **White Glove** | Autopilot mode with IT technician phase + user phase (fastest user experience) |
| **Existing Devices** | Autopilot mode for migrating legacy Windows devices via SCCM |
| **ESP** | Enrollment Status Page — shows progress during Autopilot setup |
| **TPM 2.0** | Hardware security chip — required for Self-Deploying Autopilot |
| **ABM** | Apple Business Manager — Apple's portal for managing device serial numbers |
| **ADE** | Automated Device Enrollment — iOS equivalent of Autopilot |
| **Supervised Mode** | iOS state where management profile is locked — user cannot remove it |
| **Fully Managed** | Android profile — company device, one user, full IT control |
| **Dedicated Device** | Android profile — company device, no user, locked to specific apps |
| **Corp. Work Profile** | Android profile — company device, work + personal separated |
| **BYOD Work Profile** | Android profile — personal device, work container managed by IT |
| **Provisioning Package** | Windows bulk enrollment file — acts as letter of introduction to Intune |
| **Enrollment Token / QR** | Android bulk enrollment mechanism — scanned at first boot |
| **Company Portal** | Microsoft app used to manually enroll existing devices into Intune |
| **Technician Flow** | White Glove phase 1 — IT pre-loads apps and policies |
| **User Flow** | White Glove phase 2 — employee completes personal sign-in |
| **Selective Wipe** | Removes only work data from a device — personal data untouched |

---

## Quick Revision Card

```
WINDOWS ENROLLMENT
├── New device + user sets up        → User-Driven Autopilot
├── New device + IT pre-loads        → White Glove Autopilot
├── New device + no user             → Self-Deploying Autopilot (needs TPM 2.0)
├── Old device + migrate             → Autopilot Existing Devices (via SCCM)
└── Existing device + enrol now      → Company Portal (no wipe needed)

iOS ENROLLMENT
├── Company device (any role)        → ADE via ABM (supervised)
└── Personal device                  → Company Portal (BYOD, unsupervised)

ANDROID ENROLLMENT
├── Company device + one user + work only      → Fully Managed
├── Company device + no user + locked apps     → Dedicated Device
├── Company device + one user + personal ok    → Corp. Work Profile
└── Personal device + work container           → BYOD Work Profile

BULK ENROLLMENT
├── Windows (no Autopilot)          → Provisioning Package (WCD tool)
├── iOS (no ABM)                    → Apple Configurator 2
└── Android (shared devices)        → Enrollment Token / QR Code
```

---

*MD-102 Study Notes | Domain 1: Prepare Infrastructure | Enrollment Methods*
*Last updated: 2026*
