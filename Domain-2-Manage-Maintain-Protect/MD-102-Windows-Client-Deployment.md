# MD-102 Study Notes — Windows Client Deployment & Upgrades
> Domain 2: Manage and Maintain Devices (30–35% of exam)

---

## Table of Contents
1. [Core Concept — Two Deployment Paths](#1-core-concept--two-deployment-paths)
2. [In-Place Upgrade](#2-in-place-upgrade)
3. [Wipe and Reload](#3-wipe-and-reload)
4. [Feature Update Policy vs Edition Upgrade Profile](#4-feature-update-policy-vs-edition-upgrade-profile)
5. [Windows 365 Cloud PC](#5-windows-365-cloud-pc)
6. [Windows 365 vs Azure Virtual Desktop](#6-windows-365-vs-azure-virtual-desktop)
7. [Exam Scenario Practice](#7-exam-scenario-practice)
8. [Key Terms Glossary](#8-key-terms-glossary)

---

## 1. Core Concept — Two Deployment Paths

When upgrading Windows devices, IT has two fundamental approaches:

```
PATH 1 — IN-PLACE UPGRADE
    Existing installation upgraded to new version
    Apps, settings, and user data preserved
    Less disruptive — users continue working
    Analogy: Renovating a house — structure stays, everything updated

PATH 2 — WIPE AND RELOAD
    Device wiped first — clean Windows installation
    All data removed before new OS installed
    More disruptive — requires data backup first
    Analogy: Demolishing and rebuilding — clean slate
```

### When to Choose Each Path

| Scenario | Path | Reason |
|---|---|---|
| Upgrade fleet from W10 to W11, preserve user data | In-place upgrade | Non-destructive, preserves everything |
| Device too corrupted to upgrade cleanly | Wipe and reload | Fresh start needed |
| Standardise configuration across entire fleet | Wipe and reload | Ensures identical baseline |
| Controlled staged rollout across departments | In-place upgrade | Feature update policy with rings |

---

## 2. In-Place Upgrade

### How It Works in Intune

```
IT creates feature update policy or edition upgrade profile in Intune
        │
        ▼
Policy assigned to device group (with filters for targeting)
        │
        ▼
Device receives upgrade instruction at next check-in
        │
        ▼
Windows upgrades in background — user notified to restart
        │
        ▼
After restart: new Windows version, all data and apps intact
```

### Windows 11 Hardware Requirements

For an in-place upgrade to Windows 11 to succeed, the device must meet:

```
TPM 2.0          →  Hardware security chip (same as Hello for Business)
4GB RAM          →  Minimum memory
64GB storage     →  Minimum disk space
Compatible CPU   →  Intel 8th gen+ or AMD Zen 2+ (broadly)
Secure Boot      →  UEFI firmware with Secure Boot capable
DirectX 12       →  Graphics requirement
```

> ⚠️ **Exam tip:** If a scenario mentions devices that cannot upgrade to
> Windows 11 — the answer often involves checking hardware compatibility
> first, particularly TPM 2.0.

### What Is Preserved During In-Place Upgrade

```
✅ User files and documents
✅ Installed applications
✅ User settings and preferences
✅ Device configuration
✅ Domain / Entra ID join state
✅ Intune enrollment
```

---

## 3. Wipe and Reload

### How It Works via Intune

The primary Intune mechanism for wipe and reload on existing devices is
**Autopilot for Existing Devices** — covered in Domain 1.

```
IT deploys Autopilot profile via Configuration Manager (SCCM)
        │
        ▼
Device is wiped and Windows reinstalled
        │
        ▼
Autopilot runs at first boot — device Entra joins and Intune enrols
        │
        ▼
Configuration profiles, apps and policies applied automatically
```

### When Wipe and Reload Is the Right Choice

```
Device is corrupted and cannot upgrade cleanly
Device hardware meets W11 requirements but OS state is unreliable
IT wants to standardise all devices to identical baseline config
Security incident — device needs guaranteed clean state
```

---

## 4. Feature Update Policy vs Edition Upgrade Profile

> ⚠️ **These are two distinct Intune tools solving two different problems.**
> The exam tests whether you pick the right one for the scenario.

### Feature Update Policy

```
What it does:   Controls WHICH version of Windows devices receive
                and controls the PACE of upgrades across the fleet
Use case:       Staged rollout — upgrade 25% of fleet each fortnight
                Move devices from Windows 11 22H2 → 23H2 in waves
Result:         Same edition, newer version of Windows
Key tool:       Update rings define which devices upgrade first
```

**The staged rollout pattern:**
```
Update ring 1 — Pilot group (IT staff)        →  Week 1
Update ring 2 — Early adopters (10% of fleet) →  Week 3
Update ring 3 — Broad deployment (remainder)  →  Week 6
```

### Edition Upgrade Profile

```
What it does:   Changes the EDITION of Windows on a device
                Uses a product key or licence file
Use case:       Upgrade Windows 10 Home → Windows 11 Pro
                Upgrade Windows 11 Pro → Windows 11 Enterprise
Result:         Different edition — unlocks additional features
Key benefit:    Enables features only available in higher editions
                e.g. BitLocker (Pro+), Group Policy (Pro+)
```

### Side by Side

| | Feature Update Policy | Edition Upgrade Profile |
|---|---|---|
| **Changes** | Version (22H2 → 23H2) | Edition (Home → Pro) |
| **Staged rollout** | Yes — via update rings | No — applies immediately |
| **Requires licence** | No | Yes — product key needed |
| **Preserves data** | Yes | Yes |
| **Use when** | Controlling upgrade pace | Changing Windows edition |

### The Decision Question

> **"Is the problem about WHEN devices upgrade — or WHAT edition they run?"**
>
> When/pace → Feature Update Policy
> What edition → Edition Upgrade Profile

---

## 5. Windows 365 Cloud PC

### Core Concept

Windows 365 is Microsoft's **dedicated cloud PC** service — a full Windows
desktop that lives entirely in the cloud, provisioned for a single named user,
and managed through Microsoft Intune.

```
The analogy: A personal laptop that lives in the cloud
             The user's desktop, apps and files are always there
             Accessible from any device, anywhere
             IT manages it exactly like a physical PC
```

### Key Characteristics

```
DEDICATED        →  One Cloud PC per user — not shared
PERSISTENT       →  State saves between sessions — pick up where you left off
FIXED SPEC       →  IT chooses CPU/RAM/storage tier at provisioning
INTUNE MANAGED   →  Appears in Intune portal like any Windows device
                    Same compliance policies, config profiles, remote actions
ANY DEVICE       →  Accessed via browser or Remote Desktop app
                    Works from MacBooks, iPads, Chromebooks, thin clients
```

### How It Is Provisioned

```
IT selects Windows 365 plan (CPU/RAM/storage tier)
        │
        ▼
Cloud PC provisioned in Microsoft cloud via Intune
        │
        ▼
User assigned to their specific Cloud PC
        │
        ▼
User accesses via windows365.microsoft.com or Remote Desktop app
        │
        ▼
IT manages via Intune — same as physical device
```

### Windows 365 Remote Actions

Because Windows 365 Cloud PCs are managed via Intune, they support the
same remote actions as physical devices:

```
✅ Sync          ✅ Restart         ✅ Reprovision (reset Cloud PC)
✅ Wipe          ✅ Collect diagnostics
```

**Reprovision** is unique to Windows 365 — resets the Cloud PC to its
initial state without removing the user's licence assignment.

### When to Use Windows 365

```
Remote workers who need full Windows — on personal or non-Windows devices
Contractors or seasonal staff — provision quickly, deprovision when done
BYOD users — give them a managed Windows environment without touching their device
Regulated industries — keep company data in the cloud, never on personal device
Employees in regions where shipping hardware is impractical
```

---

## 6. Windows 365 vs Azure Virtual Desktop

> ⚠️ **Commonly tested comparison — know the key differences.**

### The Core Distinction

```
Windows 365    =  Personal laptop in the cloud (dedicated, one user)
AVD            =  Hot-desk in the cloud (shared, many users)
```

### Full Comparison

| | Windows 365 | Azure Virtual Desktop |
|---|---|---|
| **Users per instance** | One — dedicated | Many — shared sessions |
| **Desktop persistence** | Always persistent | Can be persistent or non-persistent |
| **IT management** | Intune only — simple | Azure + Intune — complex |
| **Scaling** | Fixed spec per user | Dynamic — scales with demand |
| **Infrastructure management** | Microsoft manages everything | IT manages Azure infrastructure |
| **Best for** | Remote workers, contractors | Call centres, shift workers |
| **Pricing model** | Per user per month (fixed) | Consumption based (variable) |

### The Two Exam Scenarios

```
WINDOWS 365 SCENARIO TRIGGERS:
- "Each user needs their own persistent desktop"
- "Managed via Intune"
- "Accessible from personal / non-Windows devices"
- "No physical hardware to ship"
- "Contractor / remote worker needing full Windows"

AVD SCENARIO TRIGGERS:
- "Multiple users sharing sessions"
- "Shift workers / rotating staff"
- "No personal desktop needed"
- "Users don't save anything locally"
- "High-density virtual desktop environment"
```

---

## 7. Exam Scenario Practice

### Scenario 1
> A company has 300 Windows 10 Pro devices. They've purchased Windows 11 Pro
> licences and want to upgrade over 8 weeks — 25% of devices each fortnight.
> Apps, settings and user data must be preserved.

**Answer:** **In-place upgrade** using **feature update policy** in Intune.
Staged rollout managed via **update rings** — 25% of devices per ring per
fortnight. Data, apps and settings preserved throughout.

---

### Scenario 2
> A company has 50 devices running Windows 10 Home. They need Windows 11 Pro
> to enable BitLocker and Group Policy. The edition must change.

**Answer:** **Edition upgrade profile** in Intune. Provides a Windows 11 Pro
product key via profile — upgrades the edition from Home to Pro. Feature update
policy alone cannot change the edition.

---

### Scenario 3
> A law firm has 20 senior partners working from home on personal MacBooks.
> Each needs a full persistent Windows desktop with specialist legal software,
> managed centrally via Intune. No hardware will be shipped.

**Answer:** **Windows 365 Cloud PC**.
Reason 1: Each partner needs a dedicated persistent desktop — Windows 365 provides
one Cloud PC per user that saves state between sessions.
Reason 2: Managed entirely via Intune — same compliance policies and remote
actions as physical devices.
AVD is wrong here — it uses shared multi-user sessions, not dedicated persistent desktops.

---

### Scenario 4
> A call centre has 200 agents in rotating shifts. Only 120 are logged in at
> any time. Agents use whichever available workstation is free — no personal
> desktop, nothing saved locally.

**Answer:** **Azure Virtual Desktop (AVD)**. Multi-session — many agents share
the same Windows session. Non-persistent — nothing saved between sessions.
Dynamic scaling matches active agent count. Windows 365 is wrong — it provides
dedicated instances, wasting resource for shift workers who don't need persistence.

---

### Scenario 5
> An IT admin needs to upgrade 500 Windows 11 22H2 devices to 23H2. They want
> IT staff to receive it first, followed by a pilot group, then the full fleet
> — with two weeks between each wave.

**Answer:** **Feature update policy** with **update rings**.
Ring 1: IT staff — immediate.
Ring 2: Pilot group — 2 week deferral.
Ring 3: Broad deployment — 4 week deferral.
Windows Autopatch can automate this staged ring progression.

---

## 8. Key Terms Glossary

| Term | Plain English Definition |
|---|---|
| **In-place upgrade** | Upgrading Windows while preserving apps, settings and user data |
| **Wipe and reload** | Wiping device first then installing fresh Windows — clean slate |
| **Feature update policy** | Intune policy controlling which Windows version devices receive and when |
| **Edition upgrade profile** | Intune profile that changes the Windows edition using a product key |
| **Update ring** | Group of devices that receive updates at the same time — used for staged rollout |
| **Windows 365** | Microsoft's dedicated cloud PC service — one persistent PC per user in the cloud |
| **Cloud PC** | A Windows desktop that runs in Microsoft's cloud — accessed remotely |
| **Persistent desktop** | Virtual desktop that saves state between sessions — like a real PC |
| **Non-persistent desktop** | Virtual desktop that resets after each session — no personal state saved |
| **Azure Virtual Desktop (AVD)** | Microsoft's multi-user virtual desktop platform — shared sessions |
| **Reprovision** | Windows 365 remote action — resets Cloud PC to initial state |
| **Windows Autopatch** | Premium service that automates Windows update ring management |
| **TPM 2.0** | Hardware chip required for Windows 11 upgrade (also needed for Hello for Business) |
| **Staged rollout** | Deploying updates to a percentage of devices at a time to reduce risk |

---

## Quick Revision Card

```
TWO UPGRADE PATHS
├── In-place upgrade   →  Preserve data/apps — feature update policy
└── Wipe and reload    →  Clean slate — Autopilot existing devices

FEATURE UPDATE vs EDITION UPGRADE
├── Feature update  →  Control WHEN and WHICH VERSION (22H2 → 23H2)
│                      Uses update rings for staged rollout
└── Edition upgrade →  Change WHICH EDITION (Home → Pro → Enterprise)
                       Requires product key — applies immediately

WINDOWS 365 CLOUD PC
├── One dedicated persistent Cloud PC per user
├── Managed via Intune — same as physical device
├── Accessible from any device (Mac, iPad, Chromebook)
└── Best for: remote workers, contractors, BYOD users

WINDOWS 365 vs AVD
├── Windows 365  →  Dedicated, persistent, one user, Intune managed
└── AVD          →  Shared, multi-user, shift workers, Azure managed

EXAM TRIGGERS
├── "Staged rollout / waves"           →  Feature update policy + rings
├── "Home to Pro / Pro to Enterprise"  →  Edition upgrade profile
├── "Persistent personal cloud desktop" →  Windows 365
└── "Shared sessions / shift workers"  →  Azure Virtual Desktop
```

---

*MD-102 Study Notes | Domain 2: Manage and Maintain Devices | Windows Client Deployment*
*Last updated: 2026*
