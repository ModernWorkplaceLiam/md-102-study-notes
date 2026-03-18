# 📖 MD-102 Study Notes — Microsoft Endpoint Administrator

![Progress](https://img.shields.io/badge/Progress-In%20Progress-amber?style=flat-square)
![Exam](https://img.shields.io/badge/Exam-MD--102-0078D4?style=flat-square&logo=microsoft&logoColor=white)
![Status](https://img.shields.io/badge/Status-Actively%20Updating-green?style=flat-square)

Personal study notes, lab walkthroughs, and exam tips for the **Microsoft MD-102: Endpoint Administrator** certification. Written from real-world Intune deployment experience — not just textbook theory.

> ⚠️ **Disclaimer:** These are personal study notes, not official Microsoft documentation. Always refer to [Microsoft Learn](https://learn.microsoft.com) for authoritative content. Notes reflect the exam objectives as of early 2026.

---

## 🎯 About the Exam

| Detail | Info |
|--------|------|
| **Exam** | MD-102: Endpoint Administrator |
| **Certification** | Microsoft 365 Certified: Endpoint Administrator Associate |
| **Level** | Associate |
| **Duration** | 120 minutes |
| **Passing score** | 700 / 1000 |
| **Prerequisites** | None (experience with Intune recommended) |
| **Official guide** | [Microsoft Learn Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/md-102) |

---

## 📊 Exam Domain Weightings

The exam is structured across four key domains, with managing, maintaining, and protecting devices carrying the highest weight at 40–45%, followed by deploying Windows clients at 25–30%, and managing applications and identity/compliance each at 15–20%.

| # | Domain | Weight | Notes Status |
|---|--------|--------|--------------|
| 1 | [Prepare the infrastructure for device deployment](Domain-1-Prepare-Infrastructure/) | 25–30% | 🟡 In progress |
| 2 | [Manage, maintain, and protect devices](Domain-2-Manage-Maintain-Protect/) | 40–45% | 🔴 Not started |
| 3 | [Manage applications](Domain-3-Manage-Applications/) | 15–20% | 🔴 Not started |
| 4 | [Manage identity and compliance](Domain-4-Identity-Compliance/) | 15–20% | 🔴 Not started |

> **Study tip:** Domain 2 is the largest — prioritise it. If you're short on time, a strong Domain 2 combined with solid Domain 1 knowledge covers 65–75% of the exam.

---

## 📁 Folder Structure

```
md-102-study-notes/
├── Domain-1-Prepare-Infrastructure/
│   ├── 1.1-Prepare-Azure-AD.md
│   ├── 1.2-Manage-Identities.md
│   ├── 1.3-Plan-Device-Deployment.md
│   └── 1.4-Configure-Device-Enrollment.md
├── Domain-2-Manage-Maintain-Protect/
│   ├── 2.1-Configure-Profiles-Policies.md
│   ├── 2.2-Manage-Device-Compliance.md
│   ├── 2.3-Monitor-Devices.md
│   ├── 2.4-Manage-Endpoint-Security.md
│   └── 2.5-Manage-Windows-Updates.md
├── Domain-3-Manage-Applications/
│   ├── 3.1-Deploy-Apps-Intune.md
│   ├── 3.2-Win32-App-Deployment.md
│   ├── 3.3-Microsoft-365-Apps.md
│   └── 3.4-App-Protection-Policies.md
├── Domain-4-Identity-Compliance/
│   ├── 4.1-Manage-Identities-Entra.md
│   ├── 4.2-Implement-Compliance-Policies.md
│   └── 4.3-Conditional-Access-Basics.md
├── Labs/
│   ├── lab-01-autopilot-setup.md
│   ├── lab-02-win32-packaging.md
│   └── lab-03-compliance-policy.md
├── Quick-Reference/
│   ├── key-concepts.md
│   └── exam-tips.md
└── README.md
```

---

## 🧭 Study Progress Tracker

### Domain 1 — Prepare the Infrastructure (25–30%)
- [x] 1.1 Prepare Azure AD / Entra ID for device management
- [ ] 1.2 Manage device identities in Entra ID
- [ ] 1.3 Plan and deploy Windows Autopilot
- [ ] 1.4 Configure device enrollment methods

### Domain 2 — Manage, Maintain & Protect Devices (40–45%)
- [ ] 2.1 Configure device profiles and policies
- [ ] 2.2 Manage device compliance
- [ ] 2.3 Monitor devices and generate reports
- [ ] 2.4 Manage endpoint security (Defender for Endpoint)
- [ ] 2.5 Manage Windows updates with Intune

### Domain 3 — Manage Applications (15–20%)
- [ ] 3.1 Deploy apps using Intune
- [ ] 3.2 Win32 app deployment and lifecycle
- [ ] 3.3 Manage Microsoft 365 Apps deployment
- [ ] 3.4 Configure app protection policies (MAM)

### Domain 4 — Manage Identity & Compliance (15–20%)
- [ ] 4.1 Manage identities in Entra ID
- [ ] 4.2 Implement compliance policies
- [ ] 4.3 Configure Conditional Access (basics)

---

## 🔗 Official Study Resources

| Resource | Link |
|----------|------|
| Official exam page | [learn.microsoft.com/md-102](https://learn.microsoft.com/en-us/credentials/certifications/exams/md-102/) |
| Official study guide | [MD-102 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/md-102) |
| Microsoft Learn path | [Endpoint Administrator Learning Path](https://learn.microsoft.com/en-us/training/paths/endpoint-administrator-intune/) |
| Microsoft Learn sandbox | [Free sandbox environments](https://learn.microsoft.com/en-us/training/) |
| John Savill's MD-102 | [YouTube playlist](https://www.youtube.com/@NTFAQGuy) |
| MeasureUp practice tests | [measureup.com](https://www.measureup.com) |

---

## 💡 How I'm Using These Notes

These notes are written **during active study and real-world deployments** — not from reading alone. Where possible I include:

- ✅ What the exam tests vs what you actually configure day-to-day
- ⚠️ Common exam gotchas and trick question areas
- 🔬 Lab steps you can reproduce in a free Microsoft 365 trial tenant
- 💬 Real-world context from enterprise Intune deployments

---

## 🤝 Contributing

Found an error or want to add something? PRs are welcome. Please keep notes concise, accurate, and exam-objective aligned.

---

## 📫 Author

**Liam Biswas** — Modern Workplace Engineer, Birmingham UK  
[![GitHub](https://img.shields.io/badge/-ModernWorkplaceLiam-181717?style=flat-square&logo=github)](https://github.com/ModernWorkplaceLiam)
[![LinkedIn](https://img.shields.io/badge/-LiamBiswas-0A66C2?style=flat-square&logo=linkedin)](https://linkedin.com/in/liambiswas)
[![X](https://img.shields.io/badge/-@MWLiamUK-000000?style=flat-square&logo=x)](https://x.com/MWLiamUK)
