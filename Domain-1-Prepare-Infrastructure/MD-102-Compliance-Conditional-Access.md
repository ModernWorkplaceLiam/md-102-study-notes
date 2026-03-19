# MD-102 Study Notes — Compliance Policies & Conditional Access
> Domain 1: Prepare Infrastructure for Devices (25–30% of exam)

---

## Table of Contents
1. [Core Concept — The Security Door Analogy](#1-core-concept--the-security-door-analogy)
2. [Compliance Policies](#2-compliance-policies)
3. [Actions for Non-Compliance](#3-actions-for-non-compliance)
4. [Conditional Access](#4-conditional-access)
5. [Signals — The IF Conditions](#5-signals--the-if-conditions)
6. [Grant Controls — The THEN Outcomes](#6-grant-controls--the-then-outcomes)
7. [Named Locations & Trusted IPs](#7-named-locations--trusted-ips)
8. [Compliance + Conditional Access Working Together](#8-compliance--conditional-access-working-together)
9. [Exam Scenario Practice](#9-exam-scenario-practice)
10. [Key Terms Glossary](#10-key-terms-glossary)

---

## 1. Core Concept — The Security Door Analogy

### The Three-Part Model

```
Entra ID identity          =  Your staff ID badge
                              Proves WHO you are

Intune compliance policy   =  The security health check
                              Proves your device is SAFE ENOUGH

Conditional Access         =  The security door
                              Checks BOTH before letting you through
```

### The Critical Relationship

These are **three separate components** that work as a system:

| Component | Lives in | Job |
|---|---|---|
| Compliance policy | Microsoft Intune | Defines the health rules and marks devices compliant / non-compliant |
| Conditional Access policy | Microsoft Entra ID | Reads the compliance state and decides allow / challenge / block |
| Identity | Microsoft Entra ID | Confirms who the user is via their account |

> ⚠️ **Exam critical — the most commonly tested point:**
> Intune compliance policies mark devices as non-compliant.
> They do NOT block access to resources on their own.
> Blocking access is the job of Conditional Access.
> Without a Conditional Access policy, a non-compliant device still has full access.

---

## 2. Compliance Policies

A compliance policy is a set of rules a device must meet to be considered healthy. Intune evaluates every enrolled device against these rules on a regular schedule.

### Common Compliance Settings (Windows)

| Setting | What it checks | Why it matters |
|---|---|---|
| Minimum OS version | e.g. Windows 11 23H2 or later | Ensures devices have current security patches |
| BitLocker required | Disk encryption enabled | Protects data if device is lost or stolen |
| Secure Boot required | UEFI Secure Boot on | Prevents malicious OS from loading at startup |
| TPM required | TPM chip present and active | Hardware security anchor for device identity |
| Antivirus enabled | Windows Defender or approved AV | Protects against malware |
| Antivirus up to date | Definitions current | Ensures latest threat signatures active |
| Firewall enabled | Windows Firewall on | Blocks unauthorised network traffic |
| Password required | Device has a PIN or password | Prevents unauthorised physical access |

### Compliance Settings by Platform

```
Windows    →   OS version, BitLocker, Secure Boot, TPM, Defender, Firewall
iOS        →   OS version, passcode required, jailbreak detection
Android    →   OS version, device encryption, root detection
macOS      →   OS version, FileVault encryption, Gatekeeper, password
```

### Compliance State

After evaluation, every device receives one of two states:

```
COMPLIANT        All policy rules are met → device is healthy
NON-COMPLIANT    One or more rules are not met → action required
```

### Industry Benchmarks
Compliance policy settings are typically aligned with security frameworks including:
- **NIST** — National Institute of Standards and Technology
- **CIS** — Center for Internet Security benchmarks
- **NCSC** — National Cyber Security Centre (UK)

---

## 3. Actions for Non-Compliance

When a device fails a compliance check, Intune does not immediately block access. Instead it follows a **graduated response timeline** — configurable per policy.

### The Timeline Model

```
Device fails compliance check
        │
        ▼
Grace period begins (e.g. 0–7 days)
        │    Device still has access
        │    IT notified or user notified
        ▼
Marked as non-compliant (e.g. day 8–30)
        │    Compliance state = non-compliant
        │    Conditional Access can now act on this state
        ▼
Further actions triggered (e.g. day 30+)
             Remote lock, retire device, escalate to IT
```

### Configurable Actions for Non-Compliance

| Action | What happens | When to use |
|---|---|---|
| Mark device non-compliant | Updates compliance state | Immediately or after grace period |
| Send email to user | Notifies user to remediate | Early in the timeline |
| Send email to IT admin | Escalates for investigation | When user hasn't self-remediated |
| Push notification | Alert via Company Portal app | Immediate user awareness |
| Remotely lock device | Locks screen until PIN entered | Extended non-compliance |
| Retire device | Removes company data + unenrols | Last resort |

> ⚠️ **Why the grace period matters:**
> IT teams configure grace periods to account for staff on annual leave,
> sick leave, or extended travel who cannot immediately remediate.
> A 7–21 day grace period is common in enterprise environments.
> Immediate blocking without grace periods causes unnecessary helpdesk calls.

---

## 4. Conditional Access

Conditional Access is Microsoft's **IF/THEN policy engine** — it evaluates signals about the user, device and context, then decides whether to grant, challenge or block access.

### The IF/THEN Model

```
IF  (user identity is valid)
AND (device is compliant)
AND (location is trusted)
AND (app being accessed is permitted)
THEN grant access

OTHERWISE → require MFA / block access
```

### Where Conditional Access Lives

Conditional Access policies are configured in **Microsoft Entra ID** (not Intune).
They read the compliance state that Intune produces and act on it.

```
Intune          →    produces compliance state (compliant / non-compliant)
Entra ID CA     →    reads that state and enforces access decisions
```

---

## 5. Signals — The IF Conditions

Conditional Access evaluates multiple signals simultaneously before making an access decision.

### Signal Categories

| Signal | Examples | Exam relevance |
|---|---|---|
| **User / group identity** | Specific user, group membership, role | Target policies to specific teams |
| **Device compliance** | Compliant / non-compliant (from Intune) | Core integration point with Intune |
| **Device platform** | Windows, iOS, Android, macOS | Different rules per platform |
| **Named location** | Inside office IP range, country, region | MFA for external, trust for internal |
| **Cloud app / action** | SharePoint, Exchange, admin portals | Per-app access rules |
| **Sign-in risk** | Unusual sign-in, unknown device, leaked credentials | Evaluated by Entra ID Protection |
| **Client app** | Browser, mobile app, desktop client | Block legacy auth protocols |

### Your Signals Mapped to Exam Terms

```
"Geographic location"    →    Named Location signal
"What they're accessing" →    Cloud app / action signal
"Unknown device"         →    Sign-in risk signal
"OS platform"            →    Device platform signal
"Corporate vs personal"  →    Device compliance + Entra registration state
```

---

## 6. Grant Controls — The THEN Outcomes

Once signals are evaluated, Conditional Access applies one of three outcomes:

### The Three Outcomes

```
GRANT ACCESS          All conditions met — user reaches the resource
                      No additional challenge required

GRANT WITH CONDITIONS One or more conditions trigger a challenge
                      User must complete additional step to proceed
                      Examples:
                        → Require MFA
                        → Require compliant device
                        → Require Entra joined device
                        → Require approved client app

BLOCK ACCESS          Policy conditions fail — access denied entirely
                      No way for user to proceed
                      Used for: personal devices on sensitive apps,
                      sign-ins from blocked countries, high risk sign-ins
```

> ⚠️ **Common exam trap:**
> Blocking is not always the right answer. Many scenarios call for
> "grant with conditions" (e.g. require MFA) rather than outright blocking.
> Read the scenario carefully — if the user should be able to get through
> with an extra step, the answer is grant controls not block.

---

## 7. Named Locations & Trusted IPs

Named Locations allow IT to define specific geographic or network locations as trusted — unlocking location-based Conditional Access rules.

### How It Works

```
IT configures Named Location
        │
        ├── By IP range  →  "Office network" = 192.168.1.0/24
        └── By country   →  "UK only" or "Block sign-ins from [country]"

Conditional Access reads location signal
        │
        ├── Inside trusted location  →  Grant directly (no MFA)
        └── Outside trusted location →  Require MFA before granting
```

### Practical Example — Office vs Remote Access

```
Requirement: SharePoint accessible from office without MFA,
             but requires MFA from anywhere else

Solution:
  Step 1 → Create Named Location: "Corporate Office"
            Add office IP range as trusted
  Step 2 → Create CA policy:
            IF app = SharePoint
            AND location = outside "Corporate Office"
            THEN grant with MFA required
```

---

## 8. Compliance + Conditional Access Working Together

### The Complete Flow

```
User attempts to access resource (e.g. finance portal)
                    │
                    ▼
        Entra ID checks identity
        Is this a valid user account?
                    │
          ┌─────────┴─────────┐
          │ No                │ Yes
          ▼                   ▼
       Block              CA policy evaluates signals
                               │
                    ┌──────────┼──────────┐
                    │          │          │
               Compliant   Location   Risk level
               device?     trusted?   acceptable?
                    │
          ┌─────────┴─────────┐
          │ All pass           │ One fails
          ▼                   ▼
    Grant access      Grant with conditions
                      or Block
```

### Policy Design Principle

> Each Conditional Access policy should do ONE job cleanly.
> Complex requirements need multiple targeted policies —
> not one policy trying to handle everything.

### The Financial Services Example — Full Solution

| Policy | Type | What it does |
|---|---|---|
| Compliance policy | Intune | Requires BitLocker + OS 23H2 + Defender. Marks non-compliant after 7 days |
| CA policy 1 | Conditional Access | Blocks all personal / non-compliant devices from finance portal |
| CA policy 2 | Conditional Access | Requires MFA for compliant corporate devices outside trusted location |

**Total: 1 compliance policy + 2 Conditional Access policies = 3 policies**

---

## 9. Exam Scenario Practice

### Scenario 1
> An organisation wants to ensure only devices with BitLocker enabled can access
> company resources. A user's laptop doesn't have BitLocker. No CA policy exists yet.
> What happens to the user's access?

**Answer:** Nothing changes immediately. Intune marks the device as non-compliant but without a Conditional Access policy pairing, access to resources is unaffected. CA is required to enforce the block.

---

### Scenario 2
> IT needs users signing in from outside the office to complete MFA before
> accessing Exchange Online, but office users should have seamless access.
> What does the IT admin configure?

**Answer:**
1. Create a **Named Location** using the office IP range, marked as trusted
2. Create a **Conditional Access policy**: IF app = Exchange Online AND location = outside trusted location THEN grant with **MFA required**

---

### Scenario 3
> A device has been non-compliant for 45 days. The user has not remediated.
> IT wants the device locked remotely. Which feature handles this?

**Answer:** **Actions for non-compliance** in the Intune compliance policy. Configure a remote lock action triggered after a set number of days of non-compliance.

---

### Scenario 4
> A company wants personal devices blocked from their HR portal entirely,
> even if the user completes MFA successfully. Which feature enforces this
> and what grant control is used?

**Answer:** **Conditional Access policy** targeting the HR portal app. Grant control = **Block** for devices that are not Entra joined or compliant. MFA cannot override a block grant control.

---

### Scenario 5
> An IT admin creates a compliance policy requiring Windows 11 24H2.
> An employee is on maternity leave for 3 months. Her laptop is on 23H2.
> How should the grace period be configured to avoid disruption?

**Answer:** Configure the **actions for non-compliance** grace period to extend beyond the leave duration — e.g. 90+ days before the device is marked non-compliant and access is affected. This prevents unnecessary disruption for legitimate absence.

---

## 10. Key Terms Glossary

| Term | Plain English Definition |
|---|---|
| **Compliance policy** | Rules a device must meet to be considered healthy by Intune |
| **Compliant** | Device meets all compliance policy requirements |
| **Non-compliant** | Device fails one or more compliance policy rules |
| **Actions for non-compliance** | Configurable responses Intune takes as a device remains non-compliant over time |
| **Grace period** | Days allowed before a failing device is marked non-compliant — accounts for leave/travel |
| **Conditional Access** | Entra ID policy engine that evaluates signals and enforces access decisions |
| **Signal** | A condition Conditional Access evaluates — identity, location, compliance, risk etc. |
| **Grant control** | The outcome of a CA policy — grant, grant with conditions, or block |
| **Named Location** | A defined trusted network or geographic region used as a CA signal |
| **Trusted IP range** | Specific IP addresses designated as trusted within a Named Location |
| **MFA** | Multi-Factor Authentication — a second verification step (code, app, biometric) |
| **Sign-in risk** | Entra ID Protection's assessment of how suspicious a sign-in looks |
| **Block** | CA grant control — denies access entirely, no way for user to proceed |
| **Require MFA** | CA grant control — allows access only after completing MFA challenge |
| **Selective wipe** | Removes only company data from a device — personal data untouched |
| **NIST** | National Institute of Standards and Technology — US security framework |
| **CIS** | Center for Internet Security — device hardening benchmarks |
| **NCSC** | National Cyber Security Centre — UK government security guidance |

---

## Quick Revision Card

```
COMPLIANCE POLICIES (Intune)
├── Define health rules: OS version, BitLocker, Secure Boot, TPM, AV
├── Mark devices: compliant or non-compliant
├── Actions for non-compliance: notify → lock → retire (graduated)
└── Grace periods account for leave / travel

CONDITIONAL ACCESS (Entra ID)
├── Reads compliance state from Intune
├── Evaluates signals: identity, compliance, location, platform, risk, app
├── Grant controls: Grant | Grant with MFA | Block
└── Named Locations: trusted IP ranges for office vs remote rules

THE GOLDEN RULES
├── Compliance policy alone does NOT block access
├── Conditional Access alone cannot check device health without compliance
├── They always work as a PAIR
├── Each CA policy should do ONE job — use multiple policies for complex needs
└── MFA is a middle ground — not every scenario needs a full block
```

---

*MD-102 Study Notes | Domain 1: Prepare Infrastructure | Compliance & Conditional Access*
*Last updated: 2026*
