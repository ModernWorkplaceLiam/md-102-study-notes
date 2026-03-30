# MD-102 Study Notes — Device Configuration Profiles
> Domain 2: Manage and Maintain Devices (30–35% of exam)

---

## Table of Contents
1. [Core Concept — What Is a Configuration Profile?](#1-core-concept--what-is-a-configuration-profile)
2. [The Four Profile Creation Methods](#2-the-four-profile-creation-methods)
3. [Platform-Specific Profiles](#3-platform-specific-profiles)
4. [Targeting Profiles — Groups and Filters](#4-targeting-profiles--groups-and-filters)
5. [Decision Framework — Which Method to Use](#5-decision-framework--which-method-to-use)
6. [Exam Scenario Practice](#6-exam-scenario-practice)
7. [Key Terms Glossary](#7-key-terms-glossary)

---

## 1. Core Concept — What Is a Configuration Profile?

### The One-Sentence Version
A configuration profile is a package of settings that Intune pushes to enrolled
devices — controlling what devices can do, how they behave, and what they can access.

### The Analogy
```
Compliance policy  =  Health check (is the device safe?)
Conditional Access =  The gatekeeper (can the device access resources?)
Configuration profile =  The rulebook (what can the device actually do?)
```

### What Configuration Profiles Control
```
Network settings     →   Wi-Fi credentials, VPN configuration, proxy settings
Security settings    →   Password requirements, encryption, screen lock
Restrictions         →   Block CMD, regedit, Control Panel, USB access
Device features      →   Camera, Bluetooth, app store access
User experience      →   Desktop wallpaper, browser homepage, Start menu layout
Certificates         →   Push trusted root certificates to devices
Email profiles       →   Pre-configure work email accounts on mobile devices
```

> ⚠️ **Key exam distinction:**
> Configuration profiles configure WHAT devices can do.
> Compliance policies check WHETHER devices meet health standards.
> These are separate policies serving different purposes.

---

## 2. The Four Profile Creation Methods

The exam tests your ability to choose the right method for the scenario.
Each method exists because the others don't cover every situation.

### Method 1 — Settings Catalog

```
What it is:     Microsoft's modern, unified settings interface in Intune
Coverage:       Thousands of built-in Windows and platform settings
Best for:       New deployments, any setting that exists in the catalog
Interface:      Search by setting name — fast and intuitive
```

**Real-world example:** Blocking CMD, regedit access for standard users.
Configuring Wi-Fi profiles, VPN, screen lock settings.

> ✅ Always check the Settings Catalog first — it's the preferred method
> for any setting it covers.

---

### Method 2 — Administrative Templates (ADMX)

```
What it is:     Intune's built-in replica of Microsoft's Group Policy ADMX templates
Coverage:       Microsoft Office, Windows, Edge, Internet Explorer settings
Best for:       Organisations migrating from on-premises GPO to Intune
               Replicating existing Group Policy Objects in the cloud
Key point:      These are BUILT-IN Microsoft templates — no upload needed
```

**Real-world example:** An organisation migrating 200 GPOs from on-premises
Active Directory to Intune. Administrative Templates map directly to
familiar GPO settings without rebuilding from scratch.

> ⚠️ **Exam distinction:**
> Administrative Templates = built-in Microsoft GPO settings in Intune
> ADMX imports = third-party vendor templates uploaded by IT
> These are two different things — don't confuse them.

---

### Method 3 — ADMX Imports (Imported ADMX files)

```
What it is:     Third-party application vendor ADMX templates imported into Intune
Coverage:       Any application that publishes ADMX policy templates
Best for:       Managing third-party apps centrally through Intune
Process:        Download ADMX from vendor → upload to Intune →
               settings appear as configurable options
```

**Common examples:**
- Google Chrome — set homepage, disable incognito, control search engine
- Mozilla Firefox — manage updates, restrict settings
- Zoom — control meeting defaults, security settings
- Any enterprise application that ships ADMX templates

**The process:**
```
Step 1  →  Download ADMX file from vendor (e.g. Google Admin resources)
Step 2  →  Upload ADMX file into Intune (Devices → Configuration → Import ADMX)
Step 3  →  ADMX settings appear in Intune as configurable policy options
Step 4  →  Assign profile to devices like any other configuration profile
```

---

### Method 4 — OMA-URI (Custom Profiles)

```
What it is:     Open Mobile Alliance Uniform Resource Identifier
               Industry-wide MDM standard — not Intune-specific
Coverage:       Any Windows setting addressable via the MDM protocol
Best for:       Cutting-edge settings not yet in the Settings Catalog
               Obscure or highly specific settings
               Settings required before Microsoft adds catalog support
```

**When to use OMA-URI:**
```
Setting exists in Settings Catalog?     →  Use Settings Catalog
Setting is a Microsoft GPO?             →  Use Administrative Templates
Setting is a third-party app?           →  Use ADMX import
None of the above?                      →  Use OMA-URI custom profile
```

**OMA-URI format:**
```
./Device/Vendor/MSFT/Policy/Config/[Area]/[SettingName]

Example — disable USB storage:
./Device/Vendor/MSFT/Policy/Config/Storage/RemovableDiskDenyWriteAccess
Value type:  Integer
Value:       1 (enabled) or 0 (disabled)
```

> ⚠️ **Exam tip:**
> OMA-URI is the fallback — the answer when no other method covers the requirement.
> If a scenario says "the setting isn't available in the Settings Catalog"
> the answer is always OMA-URI custom profile.

---

## 3. Platform-Specific Profiles

Each platform requires its own configuration profile.
A Windows profile cannot be assigned to iOS devices and vice versa.

### The Five Platforms

| Platform | Profile types available | Key exam notes |
|---|---|---|
| **Windows** | Settings Catalog, Admin Templates, OMA-URI, ADMX import | Most profile types — most exam coverage |
| **iOS / iPadOS** | Device restrictions, Wi-Fi, VPN, email, certificates | Supervised-only settings require ADE enrollment |
| **Android** | Device restrictions, Wi-Fi, VPN, OEMConfig | Profile type varies by enrollment profile |
| **macOS** | Settings Catalog, shell scripts, custom profiles | Shell scripts unique to macOS |
| **Enterprise multi-session** | Subset of Windows settings | Azure Virtual Desktop only — shared sessions |

### Enterprise Multi-Session — The Exam Trap

```
What it is:     Windows running in Azure Virtual Desktop (AVD)
                Multiple users sharing the same Windows session
Who uses it:    Organisations running virtual desktops in Azure
Why separate:   Standard Windows profiles target one user / one device
                Multi-session needs profiles designed for shared environments
Exam relevance: Exists as a distinct profile platform — must select it
                separately in Intune when targeting AVD devices
```

> ⚠️ **Common exam trap:**
> Candidates apply standard Windows profiles to AVD devices.
> The correct platform selection is "Windows 10 and later (multi-session)"
> for Azure Virtual Desktop workloads.

### iOS Supervised vs Unsupervised Settings

```
All iOS devices     →  Basic restrictions, Wi-Fi, VPN, email, certificates
Supervised only     →  Block App Store, disable iCloud, Single App Mode,
(ADE enrolled)         prevent device name changes, block personal Apple ID
```

---

## 4. Targeting Profiles — Groups and Filters

### Groups — Broad Targeting

Profiles are assigned to Entra ID groups:
```
User groups     →  Profile follows the user across devices
Device groups   →  Profile applies to the device regardless of user
```

### Filters — Precision Targeting Within Groups

Filters apply property-based rules ON TOP of group assignments,
allowing precision targeting without creating additional groups.

```
Without filters:  All devices in "All Laptops" group receive the profile
With filters:     Only devices matching the filter rule receive the profile
```

**Filter properties available:**
```
OSVersion          →  Filter by specific OS version or build number
DeviceName         →  Target specific device naming conventions
Manufacturer       →  Dell, HP, Lenovo, Microsoft etc.
Model              →  Specific device models
EnrollmentProfile  →  Target by how the device was enrolled
IsRooted           →  Target jailbroken / rooted devices
```

**Filter modes:**
```
Include mode  →  Apply profile ONLY to devices where rule matches
Exclude mode  →  Apply to ALL devices EXCEPT where rule matches
```

### Practical Filter Example

```
Requirement:  Push a Windows 11 23H2 specific setting to all Windows 11
              23H2+ devices — not Windows 10 or earlier Windows 11 builds

Group:        All Laptops (contains mixed Windows 10 and 11 devices)

Filter rule:  OSVersion startsWith "10.0.22631"
              (10.0.22631 = Windows 11 23H2 build prefix)

Mode:         Include

Result:       Only Windows 11 23H2+ devices receive the profile
              Windows 10 and earlier Windows 11 builds excluded
              No new group needed
```

> ⚠️ **Windows version reference:**
> Windows 10  =  10.0.19xxx builds
> Windows 11  =  10.0.22xxx builds (22000=21H2, 22621=22H2, 22631=23H2)
> Filter on OSVersion startsWith "10.0.22" to catch all Windows 11 builds

### Filter Scope — More Than Just Config Profiles

Filters work across multiple Intune policy types:
```
✅ Configuration profiles
✅ Compliance policies
✅ App assignments
✅ Update rings
```

---

## 5. Decision Framework — Which Method to Use

### The Four-Question Decision Tree

```
Question 1: Does the setting exist in the Settings Catalog?
└── YES  →  Use Settings Catalog — done

Question 2: Is this a Microsoft GPO setting you're migrating from on-prem?
└── YES  →  Use Administrative Templates — done

Question 3: Is this a third-party application setting?
└── YES  →  Use ADMX import (obtain ADMX file from vendor first) — done

Question 4: None of the above?
└── YES  →  Use OMA-URI custom profile — done
```

### Quick Reference Table

| Scenario | Method |
|---|---|
| Block CMD and regedit for standard users | Settings Catalog |
| Push corporate Wi-Fi credentials | Settings Catalog |
| Migrate 200 existing GPOs to Intune | Administrative Templates |
| Set Chrome homepage and disable incognito | ADMX import (Chrome ADMX from Google) |
| Configure Firefox update policy | ADMX import (Firefox ADMX from Mozilla) |
| Apply a setting not yet in the Settings Catalog | OMA-URI custom profile |
| Target Windows 11 devices only within a mixed group | Settings Catalog + Filter |
| Configure settings for Azure Virtual Desktop | Enterprise multi-session profile |
| Restrict App Store on supervised iPhones | iOS configuration profile |

---

## 6. Exam Scenario Practice

### Scenario 1
> A company is migrating from on-premises AD to cloud-only Intune. They have
> 200 GPOs controlling desktop wallpaper, drive mappings and IE settings.
> The IT manager wants to replicate these in Intune without rebuilding from scratch.

**Answer:** **Administrative Templates** in Intune. These map directly to
Microsoft's ADMX-based Group Policy settings — the most efficient migration
path from on-premises GPO to cloud management.

---

### Scenario 2
> An organisation standardises on Google Chrome. IT wants to enforce a homepage,
> disable incognito mode, and lock the default search engine — all via Intune.

**Answer:** **ADMX import**. IT downloads the Chrome ADMX template file from
Google's administrator resources, uploads it to Intune, then configures
the Chrome-specific settings as a configuration profile.

---

### Scenario 3
> An IT admin needs to configure a specific Windows security setting that was
> released last month. It's not yet available in the Settings Catalog.

**Answer:** **OMA-URI custom profile**. OMA-URI is the industry-standard MDM
protocol that allows configuration of any addressable Windows setting,
including those not yet surfaced in the Settings Catalog UI.

---

### Scenario 4
> A company has 500 laptops in an "All Devices" group — a mix of Windows 10
> and Windows 11 23H2. A new profile should only apply to Windows 11 23H2
> devices without creating a new group.

**Answer:** Assign the profile to "All Devices" group and apply an **Include filter**
with the rule `OSVersion startsWith "10.0.22631"`. Only Windows 11 23H2
devices receive the profile — Windows 10 and earlier Windows 11 builds excluded.

---

### Scenario 5
> An organisation runs Azure Virtual Desktop for remote workers. The IT admin
> tries to assign a standard Windows configuration profile to the AVD machines
> but the settings don't apply correctly.

**Answer:** AVD uses **Enterprise multi-session** Windows — a separate platform
in Intune. The IT admin must create a new configuration profile selecting
"Windows 10 and later (multi-session)" as the platform. Standard Windows
profiles are not designed for shared session environments.

---

## 7. Key Terms Glossary

| Term | Plain English Definition |
|---|---|
| **Configuration profile** | Package of settings pushed from Intune to enrolled devices |
| **Settings Catalog** | Intune's modern unified interface for thousands of built-in settings |
| **Administrative Templates** | Built-in Microsoft GPO-equivalent templates in Intune |
| **ADMX import** | Third-party vendor policy templates uploaded into Intune |
| **OMA-URI** | Open Mobile Alliance URI — industry MDM standard for custom settings |
| **Custom profile** | Intune term for an OMA-URI based configuration profile |
| **Filter** | Property-based rule that refines which devices within a group receive a profile |
| **Include mode** | Filter applies profile only where rule matches |
| **Exclude mode** | Filter applies profile everywhere except where rule matches |
| **OSVersion** | Filter property used to target specific Windows builds |
| **Enterprise multi-session** | Windows platform for Azure Virtual Desktop shared sessions |
| **OEMConfig** | Android standard for device manufacturer-specific configuration |
| **Supervised** | iOS device state (via ADE) enabling deepest management controls |
| **Shell script** | macOS-specific Intune feature — run scripts on managed Macs |
| **User group** | Entra ID group — profile follows user across devices |
| **Device group** | Entra ID group — profile applies to device regardless of user |

---

## Quick Revision Card

```
FOUR PROFILE CREATION METHODS
├── Settings Catalog     →  First choice — thousands of built-in settings
├── Admin Templates      →  GPO migration — Microsoft settings from on-prem
├── ADMX import          →  Third-party apps (Chrome, Firefox, Zoom)
└── OMA-URI              →  Fallback — settings not available anywhere else

FIVE PLATFORMS — each needs its own profile
├── Windows              →  Full range of methods available
├── iOS                  →  Supervised-only settings require ADE
├── Android              →  Profile type varies by enrollment mode
├── macOS                →  Includes shell scripts
└── Enterprise multi-session  →  Azure Virtual Desktop — exam trap

FILTERS — precision targeting within groups
├── Properties: OSVersion, DeviceName, Manufacturer, Model
├── Include mode: apply only where rule matches
├── Exclude mode: apply everywhere except where rule matches
└── Works with: config profiles, compliance, apps, update rings

DECISION TREE
├── In Settings Catalog?     →  Settings Catalog
├── Microsoft GPO migration? →  Administrative Templates
├── Third-party app?         →  ADMX import
└── None of the above?       →  OMA-URI custom profile
```

---

*MD-102 Study Notes | Domain 2: Manage and Maintain Devices | Device Configuration Profiles*
*Last updated: 2026*
