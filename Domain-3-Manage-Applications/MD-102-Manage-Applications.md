# MD-102 Study Notes — Manage Applications
> Domain 3: Manage Applications (15–20% of exam)

---

## Table of Contents
1. [Core Concept — Install vs Manage](#1-core-concept--install-vs-manage)
2. [App Deployment in Intune](#2-app-deployment-in-intune)
3. [Windows App Types](#3-windows-app-types)
4. [Enterprise App Catalog](#4-enterprise-app-catalog)
5. [Microsoft 365 Apps Deployment](#5-microsoft-365-apps-deployment)
6. [Office App Policies](#6-office-app-policies)
7. [App Protection Policies (APP)](#7-app-protection-policies-app)
8. [App Configuration Policies](#8-app-configuration-policies)
9. [Exam Scenario Practice](#9-exam-scenario-practice)
10. [Key Terms Glossary](#10-key-terms-glossary)

---

## 1. Core Concept — Install vs Manage

> **The single most important distinction in Domain 3**

```
INSTALL an app    =  Getting it onto the device
                     IT pushes it via Intune MDM
                     Requires device enrollment

MANAGE an app     =  Controlling what it can do with company data
                     IT applies App Protection Policy
                     Does NOT require device enrollment (MAM)
```

### MDM vs MAM

```
MDM (Mobile Device Management)
    Device is enrolled in Intune
    IT manages the whole device
    Can install apps, push policies, wipe device
    Full control — deepest protection

MAM (Mobile Application Management)
    Device does NOT need to be enrolled
    IT manages only the managed apps
    Controls data within apps — not device settings
    Perfect for BYOD — personal device, managed work apps
```

> ⚠️ **Exam phrase:** "Protect company data without enrolling the device"
> Answer is always: **App Protection Policy (MAM)**

---

## 2. App Deployment in Intune

### Three Deployment Intents

| Intent | What happens | User can remove? | Use case |
|---|---|---|---|
| **Required** | Pushed automatically — installs silently | No | EDR/AV, mandatory tools |
| **Available** | Appears in Company Portal — user installs | Yes | Optional software (Adobe, Citrix) |
| **Uninstall** | Removed automatically from devices | N/A | Decommissioning old apps |

### Required + Enrollment Status Page Connection

```
Required app assigned to device group
        │
        ▼
New device enrolls via Autopilot
        │
        ▼
ESP checks: are all blocking apps installed?
        │
        ├── YES → enrollment completes, user reaches desktop
        └── NO  → ESP holds at setup screen until apps install
```

> ⚠️ **Exam connection:** Required apps can be configured as blocking apps
> in the Enrollment Status Page — device cannot complete setup until installed

### Deployment Targets

```
User groups    →  App follows the user across any device they sign into
Device groups  →  App installs on the device regardless of who logs in
```

### App Types by Platform

| Platform | App types available |
|---|---|
| **Windows** | Win32, MSI, MSIX, Microsoft Store, Enterprise App Catalog |
| **macOS** | PKG, DMG, shell scripts |
| **iOS** | App Store (VPP), custom line-of-business apps |
| **Android** | Google Play (managed), line-of-business apps |

---

## 3. Windows App Types

### Win32 — The Most Powerful Type

```
What it handles:   Complex EXE or MSI installers
                   Any app that needs custom configuration
Required format:   .intunewin (must convert using Win32 Content Prep Tool)
```

**Win32 exclusive features:**

```
Detection rules
    ├── MSI product code (GUID)
    ├── File or folder existence
    ├── Registry key / value
    └── PowerShell script output

Dependencies     →  Install prerequisite apps before this app
Supersedence     →  Replace an older app version with this one
Return codes     →  Define what exit codes mean success/failure
Install context  →  System context (all users) vs user context
```

**The packaging process:**
```
Step 1  →  Gather installer (EXE or MSI)
Step 2  →  Run Win32 Content Prep Tool → produces .intunewin file
Step 3  →  Upload .intunewin to Intune
Step 4  →  Configure install/uninstall commands
Step 5  →  Set detection rules
Step 6  →  Assign to group — deploy
```

> ⚠️ **Exam term:** The tool is called the **Win32 Content Prep Tool**
> The output format is **.intunewin**
> Both names appear in exam questions

### MSI — Simple Deployments

```
What it handles:   Standard MSI packages
Limitations:       Less configuration than Win32
                   No custom detection rules
                   No dependencies/supersedence
When to use:       Simple MSI that installs cleanly without complexity
```

### MSIX — Modern Windows Format

```
What it handles:   Modern containerised Windows apps
Benefits:          Clean install/uninstall, sandboxed
When to use:       Modern apps distributed in MSIX format
```

### Microsoft Store Apps

```
What it handles:   Apps from the Microsoft Store
Management:        Intune purchases and assigns licences
Examples:          Company Portal, Microsoft To Do, Teams
```

---

## 4. Enterprise App Catalog

### What It Is

A library of **pre-packaged Win32 apps** maintained by Microsoft — ready to
deploy without IT needing to package them.

```
WITHOUT Enterprise App Catalog:
IT downloads installer → runs Content Prep Tool → uploads .intunewin
→ configures detection rules → configures install commands → tests

WITH Enterprise App Catalog:
IT selects app from catalog → assigns to group → done
Detection rules, install commands, updates all pre-configured
```

### Common Apps in the Catalog

```
Google Chrome    Zoom           7-Zip
Mozilla Firefox  Notepad++      VLC Media Player
Adobe Reader     Microsoft Edge (various versions)
```

### Automatic Updates

Apps in the Enterprise App Catalog can be configured to **update automatically**
when new versions are published by Microsoft — no IT repackaging needed.

### When the Exam Uses Enterprise App Catalog

```
Trigger phrases:
- "simplify Win32 app deployment"
- "deploy common third-party apps without packaging"
- "automatically keep apps updated"
Answer: Enterprise App Catalog
```

---

## 5. Microsoft 365 Apps Deployment

### Three Tools — Distinct Roles

| Tool | What it does | Where it lives |
|---|---|---|
| **OCT** (Office Customization Tool) | GUI that builds the XML config file | config.office.com |
| **ODT** (Office Deployment Tool) | Command-line tool that uses XML to install | Downloaded from Microsoft |
| **M365 Apps admin centre** | Central dashboard for managing deployed apps | config.office.com |

### OCT → ODT Flow

```
OCT (config.office.com)
    IT configures:
    ├── Which apps to include (Word, Excel, Access, Visio...)
    ├── Architecture (x86 vs x64)
    ├── Language packs (English, French, German...)
    ├── Update channel
    └── Installation preferences
    Produces: configuration XML file
        │
        ▼
ODT (Office Deployment Tool)
    Reads the XML file
    Downloads Microsoft 365 Apps from Microsoft CDN
    Installs with configured settings
    Can be packaged as Win32 app → deployed via Intune
```

> ⚠️ **Exam phrase:** OCT builds the config — ODT uses it

### Update Channels

| Channel | Update frequency | Best for |
|---|---|---|
| **Current Channel** | Monthly — latest features immediately | Users wanting newest features |
| **Monthly Enterprise Channel** | Monthly — one month behind Current | Balance of currency + stability |
| **Semi-Annual Enterprise Channel** | Every 6 months | Regulated industries, stability-first |
| **Semi-Annual Enterprise (Preview)** | 4 months ahead of Semi-Annual | IT pilot testing |

### Deploying M365 Apps via Autopilot

Microsoft 365 Apps can be deployed as part of a Windows Autopilot deployment:
```
White Glove (pre-provisioning)
    IT technician phase installs M365 Apps
    User reaches desktop with Office fully ready
    Uses ODT or Intune built-in M365 Apps deployment
```

### Microsoft 365 Apps Admin Centre

```
URL: config.office.com
Capabilities:
    ├── Update health dashboard — which devices are on which version
    ├── App inventory — Office versions across the fleet
    ├── Security update status
    ├── Office policies (macro settings, add-in control)
    └── OCT access
```

---

## 6. Office App Policies

Configured via **Settings Catalog** in Intune or **Microsoft 365 Apps admin centre**.
Control how Office apps behave after installation.

### Common Policy Settings

```
Macro settings
    ├── Block all macros
    ├── Allow only digitally signed macros
    └── Allow all macros (not recommended)

Add-in management
    ├── Block all add-ins
    ├── Whitelist specific approved add-ins
    └── Block specific add-ins

File handling
    ├── Block opening of specific file types
    └── Force Protected View for external files

Cloud behaviour
    ├── Default save location (OneDrive vs local)
    └── Block saving to personal cloud storage

Telemetry
    └── Control Office usage data sent to Microsoft
```

---

## 7. App Protection Policies (APP)

### Core Concept

App Protection Policies protect company data **inside** managed apps —
without requiring device enrollment.

```
Scenario: Employee uses personal iPhone with Outlook installed from App Store
IT cannot wipe the phone (personal device)
IT CAN apply APP to Outlook → controls company data within the app
```

### Three Setting Categories

#### Category 1 — Data Protection

```
Restrict cut/copy/paste     →  Block paste from Outlook to WhatsApp
                               Allow paste only between managed apps
Save copies of org data     →  Restrict to approved locations only
                               Block save to iCloud / Google Drive
Block screen capture        →  Prevent screenshots of company data
Encrypt org data            →  Encrypt company data within the app
Print restriction           →  Block printing company documents
Block backup                →  Prevent app data backing up to personal cloud
```

#### Category 2 — Access Requirements

```
PIN to open app             →  Require PIN before accessing managed app
Biometric authentication    →  Fingerprint / Face ID to open
PIN complexity              →  Minimum length, character requirements
PIN reset period            →  Force PIN change after X days
Work account login          →  Must sign in with work account
```

#### Category 3 — Conditional Launch

```
Jailbroken/rooted device    →  Block access or wipe app data
Minimum OS version          →  Block if OS is below required version
Minimum app version         →  Block if app version is too old
Offline grace period        →  Block after X days without checking in
Max PIN attempts            →  Wipe app data after X failed attempts
Threat level                →  Block if device threat level too high
```

### APP Selective Wipe

```
What gets wiped:   Company data inside the managed app
                   Work emails, documents, contacts
                   App configuration

What stays:        Personal photos, music, messages
                   Personal apps and their data
                   Everything outside the managed app
```

### APP with vs without Enrollment

```
BYOD / unenrolled device
    APP applies to managed apps only
    No device-level control
    Selective wipe of app data only

Enrolled device (MDM + MAM)
    APP applies to managed apps
    PLUS full device policies from MDM
    Deepest protection — both layers active
```

---

## 8. App Configuration Policies

### Core Concept

Pre-configures app settings so users don't have to set them up manually.

```
App Protection Policy   →  Controls what data CAN DO
App Configuration Policy →  Controls how app IS CONFIGURED
```

### What App Configuration Policies Pre-Populate

```
Outlook
    ├── Email server address
    ├── Work email address (auto-populated)
    ├── Account name
    ├── S/MIME settings
    └── Focused Inbox default

Microsoft Teams
    ├── Default organisation tenant
    └── Meeting join settings

Managed Browser
    ├── Homepage URL
    ├── Bookmarks
    └── Allowed / blocked sites

Any managed app
    └── Key-value pairs supported by the app vendor
```

### Two Types of App Configuration

```
Managed devices     →  Configuration delivered via Intune MDM
                        Device must be enrolled
                        Broader configuration options

Managed apps        →  Configuration delivered via APP channel
                        Works on unenrolled BYOD devices
                        App must support MAM configuration
```

---

## 9. Exam Scenario Practice

### Scenario 1
> IT needs to deploy a complex internal EXE requiring a registry key detection
> rule. Which app type and what must the installer be converted to first?

**Answer:** **Win32** app type. Installer must be converted to **.intunewin**
format using the **Win32 Content Prep Tool** before uploading to Intune.
Configure registry key detection rule to confirm successful installation.

---

### Scenario 2
> A multinational needs M365 Apps with French/German language packs, x64,
> and Semi-Annual Enterprise Channel. Which two tools and what does each do?

**Answer:**
- **OCT** (Office Customization Tool) at config.office.com — builds the XML
  configuration file specifying language packs, architecture, and update channel
- **ODT** (Office Deployment Tool) — reads the XML and installs M365 Apps
  with those exact settings. OCT builds the config — ODT uses it.

---

### Scenario 3
> An employee uses their personal Android phone for work email via Outlook.
> IT wants to block copy/paste to WhatsApp, block saving to Google Drive,
> and require a PIN. No device enrollment.

**Answer:** **App Protection Policy**. No enrollment required — MAM protects
data inside managed apps only. Settings used:
- Data protection: restrict cut/copy/paste, block save to Google Drive
- Access requirements: PIN to open Outlook

---

### Scenario 4
> A user's APP-managed iPhone has 10 failed PIN attempts. Policy wipes after 10.
> What happens to work email — and what happens to personal photos?

**Answer:** **Selective wipe** — company data inside Outlook (emails, contacts,
attachments) is wiped. Personal photos, messages, and all personal app data
are completely untouched.

---

### Scenario 5
> IT wants Outlook pre-configured on enrolled iPhones — users shouldn't have
> to enter server settings or email addresses manually.

**Answer:** **App Configuration Policy**. Pre-populates email server address,
work email address, and account name — user opens Outlook and it's ready to use.

---

### Scenario 6
> A company stops using an old VPN client and switches to a new one. IT needs
> the old client removed from all 400 devices automatically without users
> having to do anything.

**Answer:** Change the deployment intent for the old VPN client to **Uninstall**
assigned to the same device group. Intune silently removes it from all 400 devices.

---

## 10. Key Terms Glossary

| Term | Plain English Definition |
|---|---|
| **MDM** | Mobile Device Management — full device management requiring enrollment |
| **MAM** | Mobile Application Management — app-level management without enrollment |
| **Required** | Deployment intent — app pushed automatically, user cannot remove |
| **Available** | Deployment intent — app appears in Company Portal for self-install |
| **Uninstall** | Deployment intent — app removed automatically from devices |
| **Win32** | Most powerful Windows app type — supports detection rules, dependencies |
| **Win32 Content Prep Tool** | Microsoft tool that converts installers to .intunewin format |
| **.intunewin** | Packaging format required for Win32 app upload to Intune |
| **Detection rule** | Rule that confirms an app is installed — MSI GUID, file, registry, PowerShell |
| **Dependency** | App that must be installed before the target app |
| **Supersedence** | Replacing an older app version with a newer one via Intune |
| **Enterprise App Catalog** | Pre-packaged Win32 apps maintained by Microsoft |
| **OCT** | Office Customization Tool — GUI that generates XML config for ODT |
| **ODT** | Office Deployment Tool — installs M365 Apps using XML config |
| **Semi-Annual Enterprise Channel** | M365 Apps update channel — updates every 6 months |
| **App Protection Policy (APP)** | Policy protecting company data inside managed apps |
| **App Configuration Policy** | Policy pre-configuring app settings for users |
| **Selective wipe** | Removes only company data from managed app — personal data untouched |
| **Conditional launch** | APP settings that trigger actions when conditions aren't met |
| **Data protection** | APP category controlling copy/paste, save, screenshot, backup |
| **Access requirements** | APP category controlling PIN, biometric, work account login |

---

## Quick Revision Card

```
DEPLOYMENT INTENTS
├── Required   →  Auto-pushed, user cannot remove (EDR, AV, mandatory tools)
├── Available  →  Self-service Company Portal (Adobe, Citrix, optional apps)
└── Uninstall  →  Auto-removed (decommissioning old apps)

WINDOWS APP TYPES
├── Win32      →  Complex apps — needs Content Prep Tool → .intunewin
├── MSI        →  Simple packages — less configuration
└── Enterprise App Catalog → pre-packaged Win32 — no packaging needed

M365 APPS DEPLOYMENT
├── OCT  →  GUI config builder at config.office.com (language, arch, channel)
├── ODT  →  Command-line installer that reads OCT's XML
└── Remember: OCT builds config · ODT uses it

APP PROTECTION POLICIES (MAM)
├── No enrollment needed — protects data in managed apps
├── Data protection: copy/paste, save locations, screenshots
├── Access requirements: PIN, biometric, work account
├── Conditional launch: jailbreak block, min OS, max PIN attempts → wipe
└── Selective wipe: company data gone · personal data untouched

APP CONFIGURATION POLICIES
├── Pre-populates app settings (email server, account name)
├── Different from APP — configures HOW app looks, not what data can do
└── Works on enrolled devices AND unenrolled (via MAM channel)
```

---

*MD-102 Study Notes | Domain 3: Manage Applications*
*Last updated: 2026*
