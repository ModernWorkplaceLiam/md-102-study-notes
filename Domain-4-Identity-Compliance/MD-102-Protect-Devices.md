# MD-102 Study Notes — Protect Devices
> Domain 4: Protect Devices (15–20% of exam)

---

## Table of Contents
1. [Core Concept — The Security Gaps](#1-core-concept--the-security-gaps)
2. [Microsoft Defender for Endpoint (MDE)](#2-microsoft-defender-for-endpoint-mde)
3. [Security Baselines](#3-security-baselines)
4. [Antivirus Policies](#4-antivirus-policies)
5. [Firewall Policies](#5-firewall-policies)
6. [Attack Surface Reduction (ASR)](#6-attack-surface-reduction-asr)
7. [Disk Encryption Policies](#7-disk-encryption-policies)
8. [RBAC in Endpoint Security](#8-rbac-in-endpoint-security)
9. [Exam Scenario Practice](#9-exam-scenario-practice)
10. [Key Terms Glossary](#10-key-terms-glossary)

---

## 1. Core Concept — The Security Gaps

Enrollment, configuration profiles, and compliance policies secure the
management layer. Domain 4 addresses the remaining threat landscape:

```
Threat                          Solution
──────────────────────────────  ──────────────────────────────────────
Malware / no EDR                Microsoft Defender for Endpoint (MDE)
Vulnerabilities / ransomware    Attack Surface Reduction (ASR) policies
Unencrypted device stolen       Disk encryption policies (BitLocker/FileVault)
Misconfigured firewall          Firewall policies
All of the above at once        Security baselines
```

### Where Endpoint Security Policies Live

All Domain 4 policies live in **Intune → Endpoint Security** — a dedicated
node separate from device configuration profiles.

```
Why separate?
1. Clarity — security team finds policies without searching config profiles
2. RBAC — security analysts can be scoped to endpoint security only
           without access to enrollment or configuration profile settings
```

---

## 2. Microsoft Defender for Endpoint (MDE)

### AV vs EDR — The Core Distinction

```
Traditional Antivirus
    Reactive — scans on schedule or manual trigger
    Detects known threats via signature matching
    Quarantines found threats
    Limited investigation capability
    No automated response beyond quarantine

Microsoft Defender for Endpoint (EDR)
    Proactive — continuous monitoring
    Detects known AND unknown threats via behavioural analysis
    Investigates automatically — traces attack chain
    Responds automatically — isolates device, kills processes
    Assigns device risk score — integrates with Intune compliance
```

### MDE + Intune Integration — Three Exam Facts

#### Fact 1 — The Connector
```
MDE connects to Intune via a connector in the Intune admin centre
Once connected:
    → MDE risk signals flow into Intune
    → Compliance policies can use MDE risk score as a rule
    → Conditional Access can enforce based on risk
```

#### Fact 2 — Device Onboarding
```
Devices onboarded into MDE via Intune policy — no manual agent install
Intune deploys the MDE onboarding package automatically
Supports: Windows, iOS, Android, macOS
```

#### Fact 3 — Device Risk Score
```
MDE continuously evaluates device threat level
Assigns one of four risk scores:
    Clear   →  No threats detected
    Low     →  Minor threats — informational
    Medium  →  Active threat present — action recommended
    High    →  Serious threat — immediate action required
```

### The Risk Signal Chain — Cross-Domain Integration

```
MDE detects active threat on device
        │
        ▼
MDE assigns risk score: High
        │
        ▼
Intune compliance policy rule: "max allowed risk = Medium"
        │
        ▼
Device marked NON-COMPLIANT
        │
        ▼
Conditional Access: non-compliant device blocked
        │
        ▼
User loses access to SharePoint, Teams, Exchange
        │
        ▼
Access restored automatically when threat remediated
```

> ⚠️ **Exam phrase:** "Automatically block access when a threat is detected"
> Answer: MDE risk score → compliance policy → Conditional Access

### MDE vs Intune Antivirus Policy

```
MDE integration     →  EDR capabilities, risk scores, automated investigation
                        Threat detection, behavioural analysis, response
                        Configured via connector + onboarding policy

Antivirus policy    →  Microsoft Defender AV scan settings
                        Schedule, exclusions, real-time protection toggle
                        Configured in Endpoint Security → Antivirus
```

---

## 3. Security Baselines

### What They Are

Pre-configured groups of security settings based on Microsoft's security
research, real-world attack data, and industry benchmarks (NIST, CIS, NCSC).

```
Instead of configuring 200+ security settings individually
→ Deploy a baseline → get Microsoft's recommended configuration instantly
→ Customise specific settings via Settings Catalog as needed
```

### Available Baselines in Intune

| Baseline | What it covers |
|---|---|
| **Windows Security Baseline** | Core Windows hardening — BitLocker, Defender, firewall, account policies |
| **Microsoft 365 Apps Baseline** | Office app security — macros, add-ins, cloud settings |
| **Microsoft Defender for Endpoint Baseline** | MDE-specific settings optimised for EDR |
| **Microsoft Edge Baseline** | Browser security settings |

### Three Exam-Critical Baseline Facts

```
1. VERSIONED
   Microsoft releases new baseline versions as the threat landscape evolves
   IT can update to newer versions — settings may change between versions
   Review before upgrading — new versions may enable additional restrictions

2. CONFLICT RULE — most restrictive wins
   Baseline sets PIN length = 6
   Config profile sets PIN length = 4
   Result: 6 is enforced (most restrictive)
   This applies across all conflicting settings

3. LAYERED APPROACH — recommended practice
   Step 1: Deploy security baseline (foundation)
   Step 2: Customise via Settings Catalog (organisation-specific needs)
   Step 3: Add endpoint security policies for specific controls
```

> ⚠️ **Exam trap:** "What happens when a baseline conflicts with a config profile?"
> Answer: The most restrictive setting wins

---

## 4. Antivirus Policies

Configured in **Endpoint Security → Antivirus**.
Controls Microsoft Defender Antivirus behaviour on Windows devices.

### Key Settings

```
Real-time protection
    ├── Enable/disable continuous file scanning
    └── Cloud-delivered protection (submit samples to Microsoft)

Scan configuration
    ├── Scan type: Quick scan vs Full scan
    ├── Scheduled scan day and time
    ├── Scan removable drives
    └── Scan network files

Exclusions
    ├── File extensions to exclude (e.g. .log files)
    ├── Folders to exclude (e.g. database folders)
    └── Processes to exclude

Remediation
    ├── Action on detected threat (quarantine, remove, allow)
    └── Days to keep quarantined items
```

### Antivirus vs Compliance Policy

```
Compliance policy    →  IS Defender AV enabled? (yes/no)
                         Device compliant or non-compliant

Antivirus policy     →  HOW does Defender AV behave?
                         Schedule, exclusions, cloud protection settings
```

---

## 5. Firewall Policies

Configured in **Endpoint Security → Firewall**.
Controls Windows Defender Firewall settings across network profiles.

### Three Network Profiles

```
Domain profile      →  When device is connected to corporate domain network
Private profile     →  When device is connected to trusted private network
Public profile      →  When device is on untrusted public network (coffee shop)
```

> ⚠️ **Exam tip:** Public profile should always have the strictest settings
> — block all inbound connections, notify when app is blocked

### Key Firewall Settings

```
Enable/disable firewall per profile
Block all inbound connections (Public profile — recommended)
Allow/block specific applications
Inbound and outbound rules
Notify user when app is blocked
Log dropped packets (for security investigation)
```

---

## 6. Attack Surface Reduction (ASR)

### Core Concept

ASR rules block specific techniques that malware commonly uses to execute
and spread — reducing the "attack surface" available to threat actors.

```
Traditional AV:   Detects malware AFTER it executes
ASR rules:        Blocks attack techniques BEFORE malware can execute
```

### Key ASR Rules — Exam Relevant

| Rule | What it blocks | Attack it prevents |
|---|---|---|
| Block Office macros from spawning child processes | Macro → PowerShell/cmd.exe | Macro-based malware delivery |
| Block executable content from email | Attachments running as executables | Phishing email payloads |
| Block credential stealing from LSASS | Memory dumping from LSASS process | Credential theft (Mimikatz) |
| Block untrusted/unsigned processes from USB | Autorun from removable media | USB-based malware |
| Block Office apps from creating executable content | Office → EXE creation | Living-off-the-land attacks |
| Block JavaScript/VBScript from launching downloads | Script-based downloaders | Drive-by downloads |

### ASR Rule Modes

```
Audit mode    →  Log when rule would have triggered — no blocking
               Used for testing impact before enabling
               Identifies legitimate apps that might be affected

Block mode    →  Actively blocks the behaviour
               Production setting once audit confirms no false positives

Warn mode     →  Blocks AND shows user a warning with option to unblock
               For rules where user context may be legitimate
```

> ⚠️ **Exam pattern:** Always test ASR rules in **audit mode** first
> before switching to **block mode** — prevents disrupting legitimate workflows

### The Ransomware Scenario

```
Attack chain:
1. User opens malicious Word document from email attachment
2. Macro inside document runs
3. Macro spawns PowerShell child process
4. PowerShell downloads ransomware payload
5. Ransomware encrypts device

ASR intervention:
Step 3 → "Block Office macros from spawning child processes" fires
         PowerShell spawn blocked — attack chain broken
         Ransomware never downloaded or executed
```

---

## 7. Disk Encryption Policies

Configured in **Endpoint Security → Disk Encryption**.

### BitLocker vs FileVault

| | BitLocker | FileVault |
|---|---|---|
| **Platform** | Windows | macOS |
| **Configured in** | Endpoint Security → Disk Encryption | Endpoint Security → Disk Encryption |
| **Recovery key stored** | Entra ID + Intune | Intune |
| **Compliance policy check** | Is BitLocker enabled? | Is FileVault enabled? |

### BitLocker — Key Configuration Settings

```
Encryption method
    ├── XTS-AES 128-bit (default)
    └── XTS-AES 256-bit (higher security — regulated industries)

Startup authentication
    ├── TPM only (transparent to user — no PIN)
    ├── TPM + PIN (user enters PIN at every startup)
    └── TPM + startup key (USB key required)

Recovery options
    ├── Recovery key escrow to Entra ID (mandatory for Intune management)
    ├── Recovery password complexity
    └── Allow/block data recovery agent

Fixed drive encryption
    └── Require encryption on non-OS drives
```

### Compliance Policy vs Disk Encryption Policy

```
Compliance policy (IS it encrypted?)
    └── Rule: "BitLocker required = Yes"
    └── Device passes or fails — compliant or non-compliant
    └── Does NOT configure HOW BitLocker works

Disk encryption policy (HOW is it encrypted?)
    └── Sets encryption method (XTS-AES 128 or 256)
    └── Configures startup PIN requirement
    └── Defines recovery key escrow settings
    └── Compliance policy is insufficient for this level of control
```

> ⚠️ **Exam distinction — most commonly tested:**
> Compliance policy = checks IF encrypted
> Disk encryption policy = configures HOW it's encrypted

---

## 8. RBAC in Endpoint Security

### Why It Matters

Not all IT staff should have access to all Intune functions.
RBAC ensures least privilege for administrators themselves.

```
IT admin team       →  Enrollment, configuration profiles, remote actions
Security team       →  Endpoint security policies only
Helpdesk            →  Remote Help, device sync, read-only access
Compliance officer  →  Compliance reports, read-only
```

### How RBAC Works in Intune

```
Built-in roles     →  Pre-defined by Microsoft (Endpoint Security Manager,
                       Help Desk Operator, Read Only Operator, etc.)

Custom roles       →  IT creates roles with specific permission sets
                       Tailored to organisation's team structure

Scope tags         →  Limit which devices an admin can manage
                       e.g. Security team can only see UK devices
```

### The Security Analyst Scenario

```
Requirement: Security analyst can manage antivirus and firewall policies
             but cannot touch enrollment or configuration profiles

Solution:    Assign "Endpoint Security Manager" built-in role
             OR create custom role with endpoint security permissions only
             Scope to relevant device groups
```

---

## 9. Exam Scenario Practice

### Scenario 1
> MDE detects active malware and assigns a device a High risk score. The
> compliance policy requires risk below Medium. What happens automatically?

**Answer:** Three-component chain:
1. **MDE** detects threat → assigns High risk score
2. **Compliance policy** marks device non-compliant (exceeds Medium threshold)
3. **Conditional Access** blocks device from accessing company resources
Access restored automatically when threat is remediated and risk score drops.

---

### Scenario 2
> A company wants Microsoft's recommended security settings on 500 Windows
> devices without manually configuring each security area. What feature — and
> what is the recommended approach for organisation-specific customisation?

**Answer:** Deploy **Windows Security Baseline** — Microsoft's pre-configured
security settings based on research and benchmarks. For organisation-specific
customisation, layer **Settings Catalog** configuration profiles on top.
The most restrictive setting wins if any conflicts arise.

---

### Scenario 3
> A ransomware attack used a Word document macro to spawn PowerShell and
> download a payload. Which policy type and rule would have prevented it?

**Answer:** **Attack Surface Reduction (ASR) policy** — rule: **"Block Office
macros from spawning child processes"**. Fires when the macro attempts to
launch PowerShell — breaking the attack chain before the payload downloads.
Rule should be tested in **audit mode** before enabling **block mode**.

---

### Scenario 4
> IT needs Windows laptops to use XTS-AES 256-bit encryption with a startup
> PIN. Compliance policies already require BitLocker. Is the compliance policy
> sufficient?

**Answer:** No — compliance policy only checks **IF** BitLocker is enabled.
A **disk encryption policy** is required to configure **HOW** BitLocker works:
encryption method (XTS-AES 256-bit) and startup authentication (TPM + PIN).

---

### Scenario 5
> A security analyst needs to create antivirus and firewall policies in Intune
> but must not have access to enrollment settings or configuration profiles.

**Answer:** **RBAC** — assign the "Endpoint Security Manager" built-in role
or create a custom role scoped to endpoint security permissions only.
The analyst can manage security policies without accessing other Intune areas.

---

### Scenario 6
> IT is planning to enable an ASR rule that blocks Office macros from spawning
> child processes. They're concerned it might affect a legitimate finance app
> that uses Excel macros. What should they do first?

**Answer:** Enable the rule in **audit mode** first. Audit mode logs when the
rule would have triggered without actually blocking anything. IT reviews the
logs to identify any legitimate apps affected, creates exclusions if needed,
then switches to **block mode** once confident there are no false positives.

---

## 10. Key Terms Glossary

| Term | Plain English Definition |
|---|---|
| **MDE** | Microsoft Defender for Endpoint — EDR solution that detects, investigates and responds to threats |
| **EDR** | Endpoint Detection and Response — proactive threat detection beyond traditional AV |
| **Device risk score** | MDE's assessment of a device's threat level (Clear/Low/Medium/High) |
| **MDE connector** | Integration point between MDE and Intune — enables risk signals in compliance |
| **Security baseline** | Pre-configured bundle of Microsoft-recommended security settings |
| **Conflict rule** | When baseline and config profile conflict — most restrictive setting wins |
| **Antivirus policy** | Endpoint security policy controlling Defender AV scan behaviour |
| **Firewall policy** | Endpoint security policy controlling Windows Defender Firewall |
| **Domain profile** | Firewall profile active when connected to corporate network |
| **Public profile** | Firewall profile active on untrusted networks — strictest settings |
| **ASR** | Attack Surface Reduction — rules that block specific malware attack techniques |
| **Audit mode** | ASR setting that logs rule triggers without blocking — used for testing |
| **Block mode** | ASR setting that actively blocks the behaviour — production setting |
| **LSASS** | Local Security Authority Subsystem Service — process targeted for credential theft |
| **Disk encryption policy** | Endpoint security policy configuring HOW BitLocker/FileVault encrypts |
| **XTS-AES** | Encryption algorithm used by BitLocker (128-bit standard, 256-bit high security) |
| **FileVault** | macOS disk encryption — equivalent of BitLocker for Apple devices |
| **RBAC** | Role-Based Access Control — limits which Intune areas each admin can access |
| **Scope tag** | RBAC mechanism limiting which devices an admin role can manage |
| **Endpoint Security node** | Dedicated Intune section for security policies — separate from config profiles |

---

## Quick Revision Card

```
MDE (Defender for Endpoint)
├── EDR — proactive detection, investigation, automated response
├── Risk scores: Clear / Low / Medium / High
├── Chain: MDE risk → compliance non-compliant → CA blocks access
└── Onboarded via Intune policy — no manual agent install

SECURITY BASELINES
├── Pre-configured Microsoft-recommended settings
├── Versioned — update as threat landscape evolves
├── Conflict rule: most restrictive setting wins
└── Layered approach: baseline → Settings Catalog customisation

ASR RULES
├── Block attack techniques before malware executes
├── Key rule: Block Office macros from spawning child processes
├── Test in audit mode first → then block mode
└── Prevents: macro malware, ransomware, credential theft, USB attacks

DISK ENCRYPTION
├── Compliance policy  →  checks IF BitLocker is enabled
├── Disk encryption policy  →  configures HOW (XTS-AES, PIN, escrow)
├── Windows: BitLocker · macOS: FileVault
└── Recovery keys escrowed to Entra ID + Intune

ENDPOINT SECURITY NODE
├── Antivirus, Firewall, ASR, Disk Encryption all live here
├── Separate from config profiles — clarity + RBAC
└── RBAC: Endpoint Security Manager role for security teams

EXAM TRIGGERS
├── "Automatically block when threat detected"  →  MDE + compliance + CA
├── "Quick security setup for all devices"      →  Security baseline
├── "Macro-based ransomware prevention"         →  ASR block macro rule
├── "Configure encryption method/PIN"           →  Disk encryption policy
└── "Security team access only"                 →  RBAC scoped role
```

---

*MD-102 Study Notes | Domain 4: Protect Devices*
*Last updated: 2026*
