# MD-102 Study Notes — Windows Hello for Business & Windows LAPS
> Domain 1: Prepare Infrastructure for Devices (25–30% of exam)

---

## Table of Contents
1. [The Problem — Why Passwords Are Not Enough](#1-the-problem--why-passwords-are-not-enough)
2. [Windows Hello for Business](#2-windows-hello-for-business)
3. [PIN vs Password — The Critical Distinction](#3-pin-vs-password--the-critical-distinction)
4. [Windows LAPS](#4-windows-laps)
5. [Hello vs LAPS — Compare and Contrast](#5-hello-vs-laps--compare-and-contrast)
6. [Exam Scenario Practice](#6-exam-scenario-practice)
7. [Key Terms Glossary](#7-key-terms-glossary)

---

## 1. The Problem — Why Passwords Are Not Enough

Understanding why these features exist makes them far easier to remember than
memorising them as isolated facts.

### End User Password Weaknesses

```
Password fatigue     →   Users pick weak, memorable passwords
                         Add one special character to meet requirements
                         e.g. Password1! → Password2!

Password reuse       →   Same password across multiple platforms
                         One breach exposes every account

Social engineering   →   Phishing pages capture credentials directly
                         Users cannot always spot fake login pages

Brute force          →   Dictionary words and common patterns
                         Automated tools try millions of combinations
```

### Local Admin Password Weaknesses

```
Shared password      →   Same local admin password on every device
                         Set once at imaging, never changed

Lateral movement     →   Attacker steals one password
                         Uses it on every device in the organisation
                         One breach = entire fleet compromised

No rotation          →   Password unchanged for months or years
                         Extended exposure window if compromised

Insecure storage     →   Passwords stored in spreadsheets, shared docs
                         No audit trail for who accessed what
```

---

## 2. Windows Hello for Business

### Core Concept

Windows Hello for Business **replaces passwords entirely** with device-bound
cryptographic credentials. There is no password to steal, phish, or spray.

### How It Works

```
Step 1 — Registration
User registers their chosen authentication method (PIN, face, fingerprint)
        │
        ▼
Step 2 — Key generation
TPM chip generates a cryptographic key pair:
  Private key  →  locked inside TPM, NEVER leaves the device
  Public key   →  sent to Entra ID for verification
        │
        ▼
Step 3 — Sign in
User presents biometric or PIN
        │
        ▼
Step 4 — Local unlock
Biometric/PIN unlocks the private key stored in TPM
No password is transmitted across the network
        │
        ▼
Step 5 — Verification
Entra ID verifies the public key signature
Access granted — no password exchanged at any point
```

### Three Authentication Methods

| Method | What the user does | Security note |
|---|---|---|
| **PIN** | Enters numeric or alphanumeric code | Device-bound — useless without physical device |
| **Fingerprint** | Touch fingerprint sensor | Stored as mathematical template, not an image |
| **Face recognition** | Look at infrared camera | Works in dark, resistant to photo spoofing |

All three methods unlock the same TPM private key.
The authentication method is just the door handle — the security behind it is identical.

### Why It Defeats Every Password Attack

| Attack type | Why it fails against Hello |
|---|---|
| Phishing | No password to capture — credential never transmitted |
| Password spray | No password exists to spray against |
| Brute force | Private key cannot be extracted from TPM |
| Credential stuffing | Device-bound — stolen credential unusable elsewhere |
| Man-in-the-middle | Nothing interceptable passes over the network |

### Requirements

```
Hardware:   TPM 2.0 chip required
            (same chip required for Self-Deploying Autopilot)
Identity:   Device must be Entra joined or Hybrid Entra joined
Management: Configured via Intune device configuration profile
            or Group Policy (hybrid environments)
```

---

## 3. PIN vs Password — The Critical Distinction

> ⚠️ **Most commonly tested Hello concept on the exam**

### The Exam Phrase to Memorise

> **"A PIN is device-bound. A password is not."**

### Why a Simple PIN Is More Secure Than a Complex Password

```
PASSWORD
├── Transmitted across the network during sign-in
├── Stored on authentication server — can be breached remotely
├── Works from ANY device, anywhere in the world
└── Stolen once → account compromised globally

WINDOWS HELLO PIN
├── Never transmitted — stays on the device
├── Unlocks TPM private key locally — nothing leaves the device
├── Only works on THIS specific device
└── Stolen PIN → useless without the physical device
```

### The Real-World Test

```
Scenario: An attacker steals a user's password via phishing.
Result:   Attacker signs in from another country immediately.
          Full account access from anywhere.

Scenario: An attacker steals a user's Hello PIN.
Result:   Attacker has a number. Without the physical device
          with that specific TPM chip, the PIN is worthless.
          Zero account access.
```

---

## 4. Windows LAPS

### Full Name
**Windows Local Administrator Password Solution**

### The Problem It Solves

In many organisations, every Windows device is imaged with the same local
administrator username and password — set once and never changed.

```
Risk: Lateral movement attack
      Attacker compromises ONE device
      Uses the shared local admin password on EVERY other device
      Entire fleet accessible from a single breach
```

### How LAPS Works

```
Intune deploys LAPS policy to devices
        │
        ▼
Each device generates a unique random local admin password
        │
        ▼
Password stored securely in Microsoft Entra ID
        │
        ▼
Password automatically rotated on a configured schedule
        │
        ▼
Authorised IT staff retrieve password via Entra ID
Role-Based Access Control (RBAC) limits who can retrieve which passwords
```

### Three Things LAPS Does

```
1. UNIQUE PASSWORD PER DEVICE
   Each device has its own randomly generated local admin password
   Breaching one device gives zero advantage on any other
   Lateral movement is eliminated

2. AUTOMATIC ROTATION
   Passwords rotate on a configured schedule (e.g. every 30 days)
   Even if a password is compromised, exposure window is limited
   No manual IT intervention required

3. SECURE CENTRALISED STORAGE
   Passwords stored in Entra ID — not spreadsheets or shared documents
   Full audit trail of who retrieved which password and when
   RBAC controls which IT staff can access which device passwords
```

### Where Passwords Are Stored and Who Can Access Them

```
Storage location:   Microsoft Entra ID
Access control:     RBAC (Role-Based Access Control)
Who can retrieve:   Only IT staff with the correct Entra ID role assigned
Audit trail:        Every retrieval is logged — who, when, which device
```

> ⚠️ **Exam scenario tip:**
> When a question mentions "only authorised personnel should be able to
> retrieve local admin passwords" — the answer is always LAPS + Entra ID + RBAC.

### LAPS Account Configuration

```
LAPS manages a specific named local administrator account on each device.
IT configures which account LAPS manages via Intune policy.
The account name is consistent — only the password is unique per device.
```

---

## 5. Hello vs LAPS — Compare and Contrast

> This comparison is the most commonly tested aspect of both features.

### The One-Sentence Summary for Each

```
Windows Hello for Business
→ Phishing-resistant authentication for END USERS
   bound to a single device via TPM cryptography
   replaces passwords for daily sign-in

Windows LAPS
→ Unique rotating local admin password per device
   for AUTHORISED IT STAFF
   eliminates lateral movement risk from shared admin credentials
```

### Side by Side

| | Windows Hello for Business | Windows LAPS |
|---|---|---|
| **Who it's for** | End users | IT administrators |
| **What it replaces** | User passwords for sign-in | Shared local admin passwords |
| **Problem solved** | Phishing, password spray, credential theft | Lateral movement, shared admin credentials |
| **Authentication method** | PIN, fingerprint, face | Randomly generated password |
| **Stored where** | Private key in TPM chip | Password in Entra ID |
| **Rotation** | N/A — key pair persists | Automatic on schedule |
| **Access control** | Device-bound to the enrolled user | RBAC in Entra ID |
| **Requires TPM** | Yes — TPM 2.0 | No — software managed |

### The Analogy

```
Windows Hello  =  A staff member's personal key fob
                  Unique to them, works only at their door
                  Cannot be copied or used by anyone else

Windows LAPS   =  A master key kept in a locked safe
                  IT can retrieve it when needed
                  Combination changes automatically every 30 days
                  Log kept of every time it was used
```

---

## 6. Exam Scenario Practice

### Scenario 1
> A company wants to eliminate the risk of phishing attacks stealing employee
> credentials. Employees currently sign in with username and password.
> What should be implemented?

**Answer:** **Windows Hello for Business**. Replaces passwords with device-bound
cryptographic credentials — there is no password to phish. Configured via
Intune device configuration profile. Requires TPM 2.0.

---

### Scenario 2
> A security audit reveals all 500 company laptops share the same local
> administrator password set two years ago. The IT manager needs each device
> to have a unique password that rotates automatically and can only be
> retrieved by senior IT staff.

**Answer:** **Windows LAPS**. Generates a unique random password per device,
rotates on schedule, stores passwords in **Entra ID**. Access restricted to
authorised IT staff via **RBAC**.

---

### Scenario 3
> An attacker steals a user's Windows Hello PIN by looking over their shoulder.
> They then try to use it to sign into the user's account from another device.
> What happens?

**Answer:** Nothing — the PIN is **device-bound**. It only unlocks the private
key stored in the TPM chip of the specific enrolled device. Without physical
access to that exact device, the PIN is completely useless.

---

### Scenario 4
> An IT technician needs to log into a laptop locally because it cannot reach
> the network. Which feature allows them to retrieve the local admin password —
> and where do they find it?

**Answer:** **Windows LAPS**. The technician retrieves the device-specific
local admin password from **Microsoft Entra ID**, provided they have the
appropriate RBAC role assigned.

---

### Scenario 5
> A company is deploying Windows Hello for Business via Intune. The IT manager
> asks which hardware requirement must be verified on every device before
> deployment. What is it?

**Answer:** **TPM 2.0** chip. Windows Hello for Business requires TPM 2.0 to
generate and store the cryptographic key pair. Without it, Hello for Business
cannot function.

---

## 7. Key Terms Glossary

| Term | Plain English Definition |
|---|---|
| **Windows Hello for Business** | Passwordless authentication using device-bound cryptographic credentials |
| **LAPS** | Local Administrator Password Solution — unique rotating admin passwords per device |
| **TPM** | Trusted Platform Module — hardware chip that generates and stores cryptographic keys |
| **TPM 2.0** | Required version for both Hello for Business and Self-Deploying Autopilot |
| **Device-bound** | Credential only works on the specific device it was created on |
| **Private key** | Cryptographic key locked inside TPM — never leaves the device |
| **Public key** | Paired key sent to Entra ID for verification during sign-in |
| **Key pair** | Private key (on device) + public key (in Entra ID) working together |
| **Lateral movement** | Attack technique where one stolen credential is used across multiple devices |
| **Password rotation** | Automatic replacement of a password on a schedule — limits exposure window |
| **RBAC** | Role-Based Access Control — limits who can perform which actions in Entra ID |
| **Phishing resistant** | Authentication method that cannot be captured via fake login pages |
| **Passwordless** | Sign-in without a traditional password — uses biometrics or PIN + TPM |
| **Biometric** | Authentication using a physical characteristic — fingerprint or face |

---

## Quick Revision Card

```
WINDOWS HELLO FOR BUSINESS
├── Replaces passwords for END USERS
├── Three methods: PIN / fingerprint / face — all TPM-backed
├── Private key locked in TPM — never transmitted
├── Device-bound: PIN stolen = useless without physical device
├── Defeats: phishing, password spray, credential stuffing
├── Requires: TPM 2.0, Entra joined device
└── Exam phrase: "A PIN is device-bound. A password is not."

WINDOWS LAPS
├── Solves shared LOCAL ADMIN password risk
├── Unique random password per device
├── Automatic rotation on schedule
├── Passwords stored in Entra ID
├── Access controlled by RBAC
├── Defeats: lateral movement attacks
└── Exam phrase: "One breach = one device. Not the whole fleet."

HELLO vs LAPS AT A GLANCE
├── Hello  = end users, daily sign-in, replaces passwords
├── LAPS   = IT admins, local admin access, rotating credentials
└── Both   = improve security posture, configured via Intune policy
```

---

*MD-102 Study Notes | Domain 1: Prepare Infrastructure | Windows Hello & LAPS*
*Last updated: 2026*
