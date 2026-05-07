# MD-102 Study Notes — Device Update Management
> Domain 2: Manage and Maintain Devices (30–35% of exam)

---

## Table of Contents
1. [Core Concept — Why Staged Updates Matter](#1-core-concept--why-staged-updates-matter)
2. [Windows Update Rings](#2-windows-update-rings)
3. [Windows Autopatch](#3-windows-autopatch)
4. [Delivery Optimisation](#4-delivery-optimisation)
5. [iOS and macOS Update Policies](#5-ios-and-macos-update-policies)
6. [Android FOTA Updates](#6-android-fota-updates)
7. [Update Monitoring](#7-update-monitoring)
8. [Exam Scenario Practice](#8-exam-scenario-practice)
9. [Key Terms Glossary](#9-key-terms-glossary)

---

## 1. Core Concept — Why Staged Updates Matter

Pushing every update to every device simultaneously creates two problems:

```
Problem 1: BUGGY PATCHES
    A faulty update deployed to all 500 devices at once
    could break functionality across the entire organisation
    Solution: Update rings — staged rollout catches issues early

Problem 2: BANDWIDTH PRESSURE
    500 devices downloading 500MB simultaneously = 250GB WAN traffic
    Internet connection becomes unusable on Patch Tuesday
    Solution: Delivery optimisation — peer-to-peer sharing
```

---

## 2. Windows Update Rings

### What an Update Ring Contains

An update ring is a single Intune policy that controls:

```
DEFERRAL PERIODS    How many days to WAIT before update is offered
                    Quality updates:  0–30 days (security patches)
                    Feature updates:  0–365 days (major versions)

DEADLINE            How many days after deferral before update is FORCED
                    Device installs update regardless of user action

GRACE PERIOD        Warning window before forced restart
                    User sees countdown — restart is coming

PAUSE               IT can pause a ring for up to 35 days
                    Stops problematic update reaching more devices
```

### Two Types of Updates Controlled by Rings

| Update type | What it contains | Frequency | Size |
|---|---|---|---|
| **Quality updates** | Security patches, bug fixes | Monthly (Patch Tuesday) | Smaller |
| **Feature updates** | Major Windows versions (23H2, 24H2) | Annually | Larger |

### The Three-Ring Model — Best Practice

```
RING 1 — Pilot (IT staff)
    Deferral:   0 days — receives update immediately on release
    Purpose:    Catch obvious breakage before it reaches users
    Size:       Small — IT team only

RING 2 — Early adopters (10–20% of fleet)
    Deferral:   7 days after release
    Purpose:    Real-world user testing — find functionality issues
    Size:       Volunteer or selected non-technical users

RING 3 — Broad deployment (remainder of fleet)
    Deferral:   14+ days after release
    Purpose:    Full rollout once issues identified and resolved
    Size:       All remaining devices
```

### Deadline and Grace Period — The Forced Restart Flow

```
Day 0     →  Update released by Microsoft
Day 7     →  Deferral expires — update offered to device
Day 12    →  Deadline reached (5-day deadline example)
             Device begins 2-day grace period countdown
             User sees persistent restart warnings
Day 14    →  Grace period expires
             Device FORCES restart and installs update
             No further dismissal possible
```

> ⚠️ **Exam distinction:**
> Deferral = days before update is OFFERED
> Deadline = days before update is FORCED
> Grace period = warning time before forced restart
> All three settings live INSIDE the update ring — not a separate policy

### Pause Functionality

```
When to use:   IT discovers a buggy update causing issues in Ring 1
Action:        Pause affected rings — devices stop receiving the update
Duration:      Up to 35 days maximum
After pause:   IT resumes ring when Microsoft releases a fix
               or 35-day limit automatically expires
```

---

## 3. Windows Autopatch

### What It Does

Windows Autopatch is a **premium service** that automates the management of
update rings — Microsoft handles the rollout cadence rather than IT configuring
it manually.

```
Manual update rings  →  IT configures deferral periods, monitors, adjusts
Windows Autopatch    →  Microsoft manages ring progression automatically
                        IT just monitors outcomes
```

### What Autopatch Manages

```
Windows quality updates    (monthly security patches)
Windows feature updates    (major version upgrades)
Microsoft 365 Apps updates
Microsoft Edge updates
Microsoft Teams updates
```

### When the Exam Uses Autopatch

```
Trigger phrases:
- "IT wants to reduce the overhead of managing update rings"
- "Automate the Windows update rollout process"
- "Microsoft manages the update cadence"
Answer: Windows Autopatch
```

---

## 4. Delivery Optimisation

### The Peer-to-Peer Principle

```
WITHOUT delivery optimisation:
500 devices × 500MB update = 250GB downloaded from Microsoft
All at the same time → internet unusable

WITH delivery optimisation (LAN peer-to-peer):
Device 1 downloads 500MB from Microsoft (1 × 500MB = 500MB WAN traffic)
Devices 2–500 download from Device 1 via local network
WAN traffic reduced by ~99%
```

### Three Download Modes

| Mode | How it works | When to use |
|---|---|---|
| **HTTP only** | Downloads from Microsoft servers only — no peer sharing | Small orgs, security-sensitive environments |
| **LAN peer-to-peer** | Shares with devices on same local network | Office environments — most common exam answer |
| **Internet peer-to-peer** | Shares with devices across internet including other orgs | Maximum bandwidth saving — rare in exam answers |

> ⚠️ **Exam tip:**
> For office/corporate scenarios, the answer is almost always **LAN peer-to-peer**.
> Internet peer-to-peer shares data with unknown external devices — security concern.

### Bandwidth Controls

IT can limit how much bandwidth delivery optimisation uses:

```
Foreground limit    →  % of bandwidth when user is actively using device
Background limit    →  % of bandwidth when device is idle
                       Background limit is typically set higher
                       (more sharing when nobody is using the device)
```

### Delivery Optimisation in Intune

Configured via **device configuration profile** in Intune:
```
Platform:       Windows
Profile type:   Delivery Optimisation
Settings:       Download mode, bandwidth limits, cache size
```

---

## 5. iOS and macOS Update Policies

### iOS Update Policy

```
Policy type:    Software update policy (separate from Windows update rings)
Location:       Intune → Devices → iOS/iPadOS → Update policies

What it can do:
    Force specific iOS version          (supervised devices only)
    Prevent updates above a version     (supervised devices only)
    Schedule update installation time   (e.g. overnight only)
    Defer updates                       (up to 90 days)
```

### The Supervised Requirement for iOS

```
Force OS update       →  Requires supervised mode (ADE enrollment)
Prevent manual update →  Requires supervised mode (ADE enrollment)
Recommend update      →  Works on unsupervised devices
```

**The full chain:**
```
ADE enrollment → supervised mode → iOS update policy → forced OS version
```

> ⚠️ **Complete exam answer for forced iOS updates:**
> Two requirements:
> 1. Devices enrolled via ADE (automatically supervised)
> 2. iOS update policy created in Intune specifying target version

### macOS Update Policy

```
Defer updates:      Up to 90 days
Force updates:      Set deadline — device forces installation
Schedule:           Define maintenance windows for installation
```

---

## 6. Android FOTA Updates

### Why Android Is Different

```
Windows    →  Single unified Windows Update channel
iOS        →  Single Apple update channel
Android    →  Dozens of manufacturers (Samsung, Zebra, Motorola)
               Each with their own firmware update system
               No single unified update channel
```

FOTA = **Firmware Over The Air** — the manufacturer's mechanism for
delivering firmware updates to Android devices.

### How Intune Controls FOTA

```
Intune cannot directly push Android firmware updates
Instead: configuration profiles control FOTA behaviour

Settings IT can configure:
    Automatic update type    →  Automatic, windowed, or postponed
    Maintenance window       →  When updates are allowed to install
                                e.g. 22:00–06:00 (night shift window)
    Freeze periods           →  Block updates during critical business periods
```

### FOTA by Enrollment Profile

| Android profile | FOTA control method |
|---|---|
| Fully managed | Configuration profile — IT controls update behaviour |
| Dedicated device | Configuration profile — strict control |
| Corporate work profile | Configuration profile — work side controlled |
| BYOD work profile | Limited — personal device, user retains update control |

---

## 7. Update Monitoring

### What Intune Reports on Updates

```
Windows Update compliance report
    ├── Per-device update status (installed, pending, failed)
    ├── Update version installed on each device
    ├── Devices not yet compliant with required update
    └── Failed update alerts

Feature update report
    ├── Which devices are on which Windows version
    ├── Devices blocked from upgrading (hardware incompatibility)
    └── Ring-by-ring rollout progress
```

### Monitoring + Advanced Analytics

For deeper update insights — Advanced Analytics (Intune Suite) provides:
```
Startup performance impact of updates
App compatibility issues post-update
Device health changes following update installation
```

---

## 8. Exam Scenario Practice

### Scenario 1
> IT wants security patches tested by IT staff immediately, early adopters after
> 7 days, and full fleet after 14 days. Users keep dismissing restart prompts.
> IT wants forced restarts after 5 days with a 2-day warning. Which policy and
> which settings handle both the staged rollout and the forced restart?

**Answer:** **Windows update rings** — a single policy containing:
- Ring 1 (IT): 0-day deferral
- Ring 2 (Early adopters): 7-day deferral
- Ring 3 (Broad): 14-day deferral
- Deadline: 5 days (within each ring's deferral period)
- Grace period: 2 days

All settings live inside the update ring — no separate configuration profile needed.

---

### Scenario 2
> 300 office-based Windows devices simultaneously download Patch Tuesday updates.
> The internet connection becomes unusable every second Tuesday. What resolves this
> and which mode is most appropriate?

**Answer:** **Delivery Optimisation** configured in a device configuration profile.
Mode: **LAN peer-to-peer (HTTP + LAN)**. First device downloads from Microsoft,
remaining 299 devices download from local network peers. WAN traffic reduced
dramatically. Internet peer-to-peer is not appropriate — shares with unknown
external devices.

---

### Scenario 3
> IT needs to force all company iPhones to iOS 17.4 and prevent users from
> manually updating to newer versions. What two requirements must be met?

**Answer:**
1. Devices must be enrolled via **ADE** — automatically places them in
   **supervised mode**, enabling OS-level controls
2. **iOS update policy** created in Intune specifying iOS 17.4 as the
   enforced version with manual updates blocked

ADE/supervised mode is the prerequisite. The iOS update policy is the implementation.

---

### Scenario 4
> A logistics company manages 200 Zebra Android handheld scanners. They need
> firmware updates to install overnight (22:00–06:00) when devices aren't in use.

**Answer:** **Android FOTA** controlled via **configuration profile** in Intune.
Configure a maintenance window of 22:00–06:00 — firmware updates install
automatically during the night window without disrupting warehouse operations.

---

### Scenario 5
> An IT manager wants to reduce the overhead of manually managing Windows update
> rings. They want Microsoft to handle the rollout cadence automatically.

**Answer:** **Windows Autopatch**. Premium service where Microsoft automatically
manages the update ring progression for Windows quality updates, feature updates,
Microsoft 365 Apps, Edge, and Teams.

---

## 9. Key Terms Glossary

| Term | Plain English Definition |
|---|---|
| **Update ring** | Intune policy containing deferral periods, deadline, and grace period for Windows updates |
| **Quality update** | Monthly security patches and bug fixes (Patch Tuesday) |
| **Feature update** | Major Windows version upgrade (e.g. 22H2 → 23H2) |
| **Deferral period** | Days a device waits before an update is offered |
| **Deadline** | Days after deferral before update installation is forced |
| **Grace period** | Warning window before forced restart — user sees countdown |
| **Pause** | Stops an update ring receiving an update for up to 35 days |
| **Windows Autopatch** | Premium service — Microsoft automates update ring management |
| **Delivery Optimisation** | Peer-to-peer update sharing to reduce WAN bandwidth consumption |
| **HTTP only** | Delivery Optimisation mode — Microsoft servers only, no peer sharing |
| **LAN peer-to-peer** | Delivery Optimisation mode — share with local network devices |
| **Internet peer-to-peer** | Delivery Optimisation mode — share with internet devices including other orgs |
| **FOTA** | Firmware Over The Air — manufacturer's Android firmware update mechanism |
| **Maintenance window** | Scheduled time period when updates are allowed to install |
| **iOS update policy** | Intune policy specifying iOS version to enforce on supervised devices |
| **Supervised mode** | iOS device state required for forced OS updates (enabled via ADE) |

---

## Quick Revision Card

```
WINDOWS UPDATE RINGS
├── Contains: deferral + deadline + grace period (all in one policy)
├── Quality updates: monthly security patches
├── Feature updates: major Windows versions
├── Three rings: Pilot (0d) → Early adopters (7d) → Broad (14d+)
├── Pause: up to 35 days if buggy update detected
└── Autopatch: Microsoft manages ring cadence automatically

DELIVERY OPTIMISATION
├── Solves: bandwidth pressure from simultaneous downloads
├── LAN peer-to-peer: office environments — most common exam answer
├── HTTP only: no peer sharing — security-sensitive environments
└── Internet peer-to-peer: maximum saving — shares with external devices

iOS + macOS UPDATES
├── Force iOS update: requires ADE enrollment (supervised) + iOS update policy
├── Defer iOS/macOS: up to 90 days
└── Two requirements for forced iOS: supervised mode + update policy

ANDROID FOTA
├── No unified update channel — each manufacturer is different
├── Intune uses configuration profiles to control FOTA behaviour
├── Maintenance windows: control when firmware updates install
└── BYOD: limited control — personal device

EXAM TRIGGERS
├── "Staged rollout / waves"       →  Update rings
├── "Automate update management"   →  Windows Autopatch
├── "Bandwidth / Patch Tuesday"    →  Delivery Optimisation (LAN P2P)
├── "Force iOS version"            →  ADE supervised + iOS update policy
└── "Android firmware window"      →  FOTA via configuration profile
```

---

*MD-102 Study Notes | Domain 2: Manage and Maintain Devices | Device Update Management*
*Last updated: 2026*
