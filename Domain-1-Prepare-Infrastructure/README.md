# Domain 1 — Prepare the Infrastructure for Device Deployment

![Domain Weight](https://img.shields.io/badge/Exam%20Weight-25–30%25-0078D4?style=flat-square&logo=microsoft&logoColor=white)
![Status](https://img.shields.io/badge/Status-In%20Progress-amber?style=flat-square)

> **MD-102: Endpoint Administrator Associate**  
> Domain 1 covers the foundational knowledge for getting devices into Intune — understanding join types, choosing the right enrollment method, and planning deployment at scale.

---

## 📂 Notes in This Domain

| File | Topic | Status |
|------|-------|--------|
| [MD-102-Enrollment-Methods.md](./MD-102-Enrollment-Methods.md) | Enrollment methods across Windows, iOS, Android — all Autopilot modes, ADE, bulk enrollment, and decision frameworks | ✅ Complete |

*More notes will be added as study progresses through this domain.*

---

## 🗺️ What Domain 1 Covers

Domain 1 is about everything that happens **before** a device is actively managed — the identity, the enrollment method, and the deployment approach. Get this domain solid and you have the foundation for everything else in the exam.

The four key areas are:

**Entra ID and device identity** — Understanding the three join types (Entra Joined, Hybrid Entra Joined, Entra Registered) and when each applies. A common exam trap is conflating joining with enrollment — they are separate actions.

**Windows enrollment methods** — Autopilot (all four modes), Company Portal for existing devices, and Group Policy auto-enrollment for hybrid environments. Knowing *when* to use each mode is tested heavily.

**iOS and Android enrollment** — ADE via Apple Business Manager for iOS, and the four Android enrollment profiles (Fully Managed, Dedicated Device, Corporate Work Profile, BYOD Work Profile). The supervised vs unsupervised iOS distinction is a frequent exam scenario topic.

**Bulk enrollment** — Provisioning packages for Windows, Apple Configurator 2 for iOS, and enrollment tokens/QR codes for Android. The exam tests what these tools *do* vs what Intune does once enrollment is established.

---

## ⚡ Domain 1 Key Facts — Quick Revision

### The most important distinction in this domain
> **Joining ≠ Enrolling.** A device can have an Entra ID identity (joined) without being under Intune management (enrolled). Uploading a hardware hash registers a device for future Autopilot — it does not enroll the device or push any policies.

### Autopilot modes at a glance

| Mode | Has a user? | IT touches device? | Needs TPM 2.0? |
|------|------------|-------------------|----------------|
| User-Driven | Yes | No | No |
| Self-Deploying | No | No | **Yes** |
| Pre-Provisioning (White Glove) | Yes | Yes (technician phase) | No |
| Existing Devices | Yes | Via SCCM | No |

### Android profiles at a glance

| Profile | Owns device | Has user | Personal apps | IT controls |
|---------|------------|----------|---------------|-------------|
| Fully Managed | Company | Yes | No | Whole device |
| Dedicated Device | Company | No | No | Whole device |
| Corp. Work Profile | Company | Yes | Yes | Work side only |
| BYOD Work Profile | Employee | Yes | Yes | Work container |

### iOS: ADE vs Company Portal

| | ADE (via ABM) | Company Portal |
|---|---|---|
| Device ownership | Company | Personal (BYOD) |
| Supervised mode | Yes — automatic | No |
| User can remove management | **No** — profile locked | Yes |
| App Store can be blocked | Yes | No |

---

## 🔗 Official Microsoft Learn Resources

| Resource | Link |
|----------|------|
| Plan device deployment | [learn.microsoft.com](https://learn.microsoft.com/en-us/mem/intune/fundamentals/deployment-plan-setup) |
| Windows Autopilot overview | [learn.microsoft.com](https://learn.microsoft.com/en-us/autopilot/windows-autopilot) |
| iOS/iPadOS enrollment guide | [learn.microsoft.com](https://learn.microsoft.com/en-us/mem/intune/enrollment/ios-enroll) |
| Android enrollment guide | [learn.microsoft.com](https://learn.microsoft.com/en-us/mem/intune/enrollment/android-enroll) |

---

## ✅ Domain 1 Study Checklist

- [ ] Explain the difference between Entra Joined, Hybrid Entra Joined, and Entra Registered from memory
- [ ] Describe all four Autopilot deployment modes and when to use each
- [ ] Explain why Self-Deploying mode requires TPM 2.0
- [ ] Describe the two phases of White Glove (Pre-Provisioning) Autopilot
- [ ] Explain the difference between uploading a hardware hash and enrolling via Company Portal
- [ ] Name all four Android enrollment profiles and their key differences
- [ ] Explain what ADE supervised mode enables that Company Portal cannot
- [ ] Describe what a provisioning package does — and what it does NOT do
- [ ] Work through the decision framework scenarios in the notes without referring to the answers

---

*Part of [md-102-study-notes](https://github.com/ModernWorkplaceLiam/md-102-study-notes) by [@ModernWorkplaceLiam](https://github.com/ModernWorkplaceLiam)*
