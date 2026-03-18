# ⚡ Key Concepts Quick Reference — MD-102

A fast-reference cheat sheet for the most exam-critical concepts. Designed for last-minute review before your exam.

---

## Enrolment Methods

| Method | Use Case | Key Point |
|--------|---------|-----------|
| Windows Autopilot | New device, OEM or corporate-wiped | Zero-touch, user-driven or pre-provisioned |
| Bulk enrolment | Shared/kiosk devices, no user affinity | Uses provisioning package (PPKG) |
| BYOD (user-driven) | Personal devices, Company Portal | Entra registered, not joined |
| Co-management | Existing SCCM estate moving to cloud | Dual management, workload sliding scale |
| Group Policy enrolment | Hybrid Entra joined, auto-enrolment via GPO | Requires Hybrid Entra join first |

---

## Device Join Types

| Type | Azure AD State | Who owns it? | Best For |
|------|---------------|-------------|----------|
| Entra Registered | Registered | User (BYOD) | Personal devices, BYOD |
| Entra Joined | Joined | Organisation | Cloud-only corporate devices |
| Hybrid Entra Joined | Joined + on-prem AD | Organisation | Existing on-prem AD estate |

---

## Win32 App Exit Codes

| Code | Meaning |
|------|---------|
| `0` | Success |
| `1707` | Success (MSI) |
| `3010` | Success — reboot required |
| `1641` | Success — reboot initiated |
| `1618` | Another install already running |
| `1603` | Fatal error during install |

---

## Compliance vs Configuration

| Concept | Purpose | Action if failed |
|---------|---------|-----------------|
| **Compliance policy** | Checks if device meets requirements | Marks non-compliant, triggers CA block |
| **Configuration profile** | Pushes settings to device | Applies setting, reports conflict |

> **Exam gotcha:** Compliance policies *assess* — they don't *configure*. Configuration profiles *configure*. Don't confuse the two.

---

## Update Ring Settings — Know These

| Setting | What it controls |
|---------|----------------|
| Servicing channel | Which Windows channel (GA, Beta) |
| Deferral — quality updates | Days to delay security updates (max 30) |
| Deferral — feature updates | Days to delay feature updates (max 365) |
| Active hours | When device won't restart |
| Deadline | Forces restart after N days |
| Grace period | Days after deadline before forced restart |

---

## Autopilot Deployment Modes

| Mode | User involvement | Pre-provisioned? |
|------|----------------|-----------------|
| User-driven (Entra) | User completes OOBE | Optional |
| User-driven (Hybrid) | User completes OOBE, needs line-of-sight to DC | Optional |
| Pre-provisioning (White Glove) | Technician pre-stages, user completes | Yes |
| Self-deploying | No user interaction — kiosk/shared | No user affinity |

---

## Defender for Endpoint Integration

- Connect Intune and MDE in **both** portals (Intune + MDE admin center)
- MDE risk score can feed into Intune compliance policy
- Use **Security baselines** in Intune for hardening — pre-built CIS/MSFT recommended settings
- ASR rules deployed via Intune endpoint security policies — not configuration profiles

---

## Exam Gotchas — Watch These

- **Settings Catalog vs Templates** — Microsoft is moving everything to Settings Catalog. Exam may test both.
- **Required vs Available** — Required = force install. Available = Company Portal self-service. Know the difference.
- **MAM without MDM** — App protection policies work even on unmanaged devices. Exam tests this boundary.
- **Scope tags** — Used for RBAC delegation, not for targeting policy assignment.
- **Filters** — Used to refine assignment scope within a group, not to replace groups.
- **Supersedence vs Dependency** — Supersedence = replace old version. Dependency = install X before Y.
