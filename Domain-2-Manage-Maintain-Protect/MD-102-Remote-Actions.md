# MD-102 Study Notes — Remote Actions in Intune
> Domain 2: Manage and Maintain Devices (30–35% of exam)

---

## Table of Contents
1. [Core Concept — What Are Remote Actions?](#1-core-concept--what-are-remote-actions)
2. [The Complete Remote Actions Reference](#2-the-complete-remote-actions-reference)
3. [Wipe vs Retire — The Critical Distinction](#3-wipe-vs-retire--the-critical-distinction)
4. [Fresh Start vs Wipe](#4-fresh-start-vs-wipe)
5. [BitLocker Key Rotation](#5-bitlocker-key-rotation)
6. [Device Query with KQL](#6-device-query-with-kql)
7. [Bulk Remote Actions](#7-bulk-remote-actions)
8. [The Decision Ladder](#8-the-decision-ladder)
9. [Exam Scenario Practice](#9-exam-scenario-practice)
10. [Key Terms Glossary](#10-key-terms-glossary)

---

## 1. Core Concept — What Are Remote Actions?

Remote actions are commands IT can trigger on enrolled devices directly from
the Microsoft Intune portal — without physically touching the device.

### Why They Matter
```
Lost device       →  Wipe it remotely before data is accessed
Employee leaving  →  Retire their BYOD device, remove company data
Policy not syncing →  Force a Sync to pull latest policies immediately
BitLocker key     →  Rotate it if potentially compromised
Troubleshooting   →  Query live device state without remoting in
```

### The Golden Rule for Exam Scenarios
> **Always use the least destructive action that solves the problem.**
>
> Sync before Restart. Restart before Fresh Start.
> Retire before Wipe. Never Wipe a BYOD device.

---

## 2. The Complete Remote Actions Reference

### Maintenance Actions (Non-destructive)

| Action | What it does | When to use |
|---|---|---|
| **Sync** | Forces device to check in with Intune immediately | Device hasn't received latest policies |
| **Restart** | Remotely reboots the device | Apply pending updates, resolve software issues |
| **Collect diagnostics** | Gathers logs and uploads to Intune | Troubleshooting without remoting in |

### Recovery Actions

| Action | What it does | Data impact |
|---|---|---|
| **Fresh Start** | Reinstalls Windows cleanly | Personal data kept, company apps removed |
| **Wipe** | Factory reset — removes everything | ALL data removed — device reissuable |
| **Retire** | Removes company data only | Personal data untouched |
| **Delete** | Removes device record from Intune portal | Record only — run after Retire or Wipe |

### Security Actions

| Action | What it does | When to use |
|---|---|---|
| **BitLocker key rotation** | Generates new recovery key, invalidates old | Key compromised, key used for recovery |
| **Device query (KQL)** | Live query against single device current state | Real-time troubleshooting |

---

## 3. Wipe vs Retire — The Critical Distinction

> ⚠️ **Most commonly tested remote action distinction on the exam.**
> Exam scenarios are specifically designed to make candidates choose the wrong one.

### Side by Side

```
WIPE                                    RETIRE
────────────────────────────────        ────────────────────────────────
Factory reset                           Selective data removal
ALL data removed                        Company data only removed
Personal data: GONE                     Personal data: UNTOUCHED
Company data: GONE                      Company data: GONE
Apps: ALL removed                       Work profile / managed apps: removed
Device after: Ready to reissue          Device after: Personal device again
Use for: Corporate owned devices        Use for: BYOD / personal devices
```

### The Decision Question

> **"Who owns the device?"**
>
> Company owns it → Wipe (factory reset, reissue to next user)
> Employee owns it → Retire (remove work data, leave personal data)

### Wipe Options

```
Standard Wipe
    └── Factory reset — everything removed
    └── Device must be re-enrolled from scratch

Wipe with "Retain enrollment state and user account"
    └── Device resets but re-enrols automatically
    └── Useful for troubleshooting corrupted devices
    └── Saves re-enrollment effort for IT
```

### Retire + Delete — Two Separate Steps

```
Retire alone  →  Company data removed, device unenrolled
               Device record REMAINS in Intune portal

Delete alone  →  Removes device record from portal
               Does NOT remove company data from device

Retire then Delete  →  Complete removal — data gone AND record gone
```

> ⚠️ **Exam trap:** "Remove the device from Intune entirely" requires BOTH
> Retire (to remove data) AND Delete (to remove the record).

---

## 4. Fresh Start vs Wipe

Both reset a Windows device — but with a critical difference:

```
FRESH START                             WIPE
────────────────────────────────        ────────────────────────────────
Reinstalls Windows                      Factory reset
Personal files: KEPT                    Personal files: REMOVED
User accounts: KEPT (option)            Everything: REMOVED
Company apps: removed                   All apps: REMOVED
OEM bloatware: removed                  Returns to factory state
Use for: Fixing corrupted Windows       Use for: Reissuing device to new user
         keeping user's personal data            or securing lost/stolen device
```

### When the Exam Uses Fresh Start

```
Trigger phrases:
- "reinstall Windows without losing personal files"
- "remove company apps but keep the user's documents"
- "clean Windows installation on employee's own device"
```

---

## 5. BitLocker Key Rotation

### The Problem It Solves

BitLocker encrypts the Windows drive. The recovery key unlocks it if the user
forgets their PIN or the device needs recovery. If this key is compromised —
written down, photographed, or used — it needs to be rotated immediately.

### How Rotation Works

```
IT triggers rotation from Intune portal
        │
        ▼
Device generates a new random recovery key
Old key is immediately invalidated
        │
        ▼
New key automatically escrowed to Entra ID and Intune
IT can retrieve new key via portal (RBAC controlled)
```

### Where Recovery Keys Are Stored

```
Primary location:   Microsoft Entra ID (device blade)
Also visible in:    Microsoft Intune portal
Access control:     RBAC — only authorised IT staff can retrieve
Audit trail:        Every retrieval logged — who, when, which device
```

### Automatic Rotation

BitLocker key also rotates automatically when:
```
→  User uses the recovery key to unlock the drive
   (prevents reuse of a key that has been exposed)
→  Configured rotation schedule in compliance policy
```

### The Stolen Laptop Sequence

> ⚠️ **Exam favourite — two actions required, order matters:**

```
Step 1: Rotate BitLocker key
    └── Invalidates any key the thief might have seen or written down
    └── Even if they have the physical device, old key won't work

Step 2: Wipe the device
    └── Factory reset — all data removed
    └── Device safe to recover and reissue if found

Common mistake: Only running Wipe without rotating key first
               A thief with the old key could attempt recovery before wipe completes
```

---

## 6. Device Query with KQL

### Two Types of KQL in Intune — Don't Confuse Them

```
Advanced Analytics KQL          Device Query (Remote Action) KQL
──────────────────────────────  ──────────────────────────────────
Queries FLEET-WIDE data         Queries ONE specific device
Historical telemetry            Live / current state
"All Dell devices last 30 days" "Is Windows Update running right now?"
Used for: trend analysis        Used for: real-time troubleshooting
```

### What Device Query Can Check (Live)

```
Running processes     →  Is a specific service currently running?
Installed software    →  Is the required app installed?
Hardware inventory    →  What hardware is in this device right now?
User sessions         →  Who is currently logged in?
Network adapters      →  What network connections are active?
Windows services      →  Status of any Windows service
```

### Real-World Example

```
Problem: User can't receive Windows Updates after Patch Tuesday
KQL query on their specific device:
→  Check: Is Windows Update service (wuauserv) running?
→  Check: Is BITS (Background Intelligent Transfer Service) running?
→  Check: What is the last Windows Update attempt status?

Result: IT gets live data without needing Remote Help session
        Faster diagnosis — no user interaction required
```

### Device Query vs Collect Diagnostics

```
Device Query KQL        →  Targeted, specific, real-time questions
                            IT knows what they're looking for

Collect Diagnostics     →  Broad log collection uploaded to Intune
                            IT reviews logs after collection
                            Better for unknown/complex issues
```

---

## 7. Bulk Remote Actions

Most remote actions can be applied to multiple devices simultaneously:

```
Bulk Sync      →  Force all devices in a group to check in at once
               →  Useful after pushing a major policy change
Bulk Restart   →  Reboot multiple devices (e.g. after update deployment)
Bulk Retire    →  Off-board multiple BYOD devices at once
Bulk Wipe      →  Mass reset (use with extreme caution)
Bulk Delete    →  Clean up stale device records
```

> ⚠️ **Bulk actions are powerful and largely irreversible.**
> Always filter carefully before executing bulk Wipe or Retire.

---

## 8. The Decision Ladder

When a device has a problem — always start at the top and work down:

```
LEAST DESTRUCTIVE
        │
        ▼
1. SYNC          Force check-in — pull latest policies
        ▼
2. RESTART       Reboot — resolve transient software issues
        ▼
3. COLLECT DIAGNOSTICS   Gather logs — investigate before acting
        ▼
4. FRESH START   Reinstall Windows — keep personal data
        ▼
5. RETIRE        Remove company data — leave personal data
        ▼
6. WIPE          Factory reset — remove everything
        ▼
7. DELETE        Remove device record from Intune
        │
        ▼
MOST DESTRUCTIVE
```

### How the Exam Uses This Ladder

```
"Device hasn't received new policy"   →  Sync (step 1)
"Device is slow / misbehaving"        →  Restart or Fresh Start
"Employee is leaving (BYOD)"          →  Retire (step 5)
"Employee is leaving (corporate)"     →  Wipe (step 6)
"Device is lost / stolen"             →  Rotate BitLocker → Wipe
"Remove device from Intune entirely"  →  Retire → Delete
```

---

## 9. Exam Scenario Practice

### Scenario 1
> A user reports their device hasn't received a security policy pushed an hour ago.
> The device last checked in 18 hours ago. No data loss is acceptable.

**Answer:** **Sync**. Forces the device to check in with Intune immediately and
pull the latest policies. Non-destructive — no data affected.

---

### Scenario 2
> A laptop is reported stolen. Company-owned Windows device with sensitive
> financial data. IT needs data to be unrecoverable and device reissuable if found.

**Answer:** Two actions in sequence:
1. **Rotate BitLocker recovery key** — invalidates any key the thief may have
2. **Wipe** — factory reset removes all data, device ready to reissue

BitLocker rotation alone does not remove data. Wipe alone doesn't protect
against recovery using a previously compromised key.

---

### Scenario 3
> An employee is leaving Friday. They use a personal iPhone with a BYOD work
> profile for work email and Teams. IT needs all company data removed by Friday
> without affecting personal photos and messages.

**Answer:** **Retire**. Removes the work profile and all company data — email,
Teams, managed apps. Personal photos, messages and apps are completely untouched.
Followed by **Delete** to remove the device record from Intune.

---

### Scenario 4
> IT is investigating why a specific laptop isn't receiving Windows Updates.
> They want to check in real time whether the Windows Update service is running
> without using Remote Help.

**Answer:** **Device query with KQL** (remote action). Run a live query against
the specific device to check the current status of the Windows Update service
(wuauserv) — real-time results without needing a Remote Help session.

---

### Scenario 5
> A user's Windows device has become corrupted after a failed update. IT needs
> to reinstall Windows cleanly. The user has important personal documents they
> cannot afford to lose. The device is company-owned.

**Answer:** **Fresh Start**. Reinstalls Windows cleanly, removes company and
OEM apps, but **retains personal files**. Wipe would be wrong here — it would
remove the user's personal documents.

---

## 10. Key Terms Glossary

| Term | Plain English Definition |
|---|---|
| **Remote action** | Command triggered from Intune portal that executes on an enrolled device |
| **Sync** | Forces device to immediately check in with Intune and pull latest policies |
| **Restart** | Remote reboot of a device — no data loss |
| **Collect diagnostics** | Gathers device logs and uploads them to Intune for IT review |
| **Fresh Start** | Reinstalls Windows — personal data kept, company apps removed |
| **Wipe** | Factory reset — removes ALL data, device ready to reissue |
| **Retire** | Removes company data only — personal data untouched, device unenrolled |
| **Delete** | Removes device record from Intune portal — run after Retire or Wipe |
| **BitLocker key rotation** | Generates new recovery key, invalidates old key |
| **Recovery key escrow** | Automatic storage of BitLocker key in Entra ID / Intune |
| **Device query** | Live KQL query against a single specific device's current state |
| **Bulk action** | Applying a remote action to multiple devices simultaneously |
| **Least privilege action** | Security principle — use the minimum action needed to solve the problem |
| **Wipe with retain** | Wipe option that keeps enrollment state so device re-enrols automatically |

---

## Quick Revision Card

```
REMOTE ACTIONS — QUICK REFERENCE
├── Sync              →  Policy not received — force check-in
├── Restart           →  Reboot — no data loss
├── Collect diagnostics →  Gather logs for investigation
├── Fresh Start       →  Reinstall Windows, keep personal files
├── Wipe              →  Factory reset — everything gone — corporate devices
├── Retire            →  Company data only — BYOD devices
├── Delete            →  Remove Intune record (after Retire or Wipe)
├── BitLocker rotation →  New key generated, old key invalid
└── Device query KQL  →  Live query — current device state

WIPE vs RETIRE (most tested)
├── Who owns it?  Company = Wipe  |  Employee = Retire
├── Wipe: ALL data gone, device reissuable
└── Retire: personal data stays, company data gone

STOLEN LAPTOP SEQUENCE
├── Step 1: Rotate BitLocker key (invalidate old key)
└── Step 2: Wipe (remove all data)

DECISION LADDER
Sync → Restart → Fresh Start → Retire → Wipe → Delete
(always use least destructive action that solves the problem)
```

---

*MD-102 Study Notes | Domain 2: Manage and Maintain Devices | Remote Actions*
*Last updated: 2026*
