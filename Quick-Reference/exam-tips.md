# 🎯 Exam Tips & Strategy — MD-102

Personal exam strategy notes and tips collected during study and from community feedback.

---

## Before the Exam

- [ ] Complete all Microsoft Learn modules for MD-102
- [ ] Complete at least 2 full practice exams in certification mode
- [ ] Score 85%+ consistently on practice exams before booking
- [ ] Set up a free Microsoft 365 trial tenant for hands-on practice
- [ ] Review all items in Quick-Reference/key-concepts.md

---

## Exam Strategy

**Time management:** 120 minutes for approximately 40–60 questions. That's roughly 2 minutes per question. Flag uncertain questions and return — don't spend more than 3 minutes on any single question first pass.

**Read carefully:** Microsoft exam questions are precise. Words like "minimum", "most appropriate", "first", and "without affecting" all narrow the answer significantly. Train yourself to identify the constraint in every question.

**Eliminate wrong answers:** Even if you're unsure of the correct answer, you can often eliminate 1–2 options that are clearly wrong. This significantly improves your odds.

**Real-world vs exam:** The exam sometimes has a "correct" answer that reflects Microsoft best practice rather than what most organisations actually do. When in doubt, choose the most cloud-native, Intune-native option.

---

## Topic Areas to Focus On

### High priority (appear frequently)
- Windows Autopilot — all deployment modes
- Intune enrolment methods and when to use each
- Compliance policies vs configuration profiles — know the difference cold
- Win32 app packaging and detection rules
- Windows Update rings — all settings and what they do
- Defender for Endpoint integration with Intune

### Medium priority
- Co-management with SCCM (workload sliding scale)
- Endpoint analytics
- Microsoft 365 Apps deployment and update channels
- App protection policies (MAM)

### Lower priority but don't skip
- Scope tags and RBAC
- Filters vs groups for assignment
- Bulk enrolment / provisioning packages

---

## Things That Trip People Up

**Compliance vs Configuration** — This comes up in multiple questions. Compliance *assesses*, Configuration *applies settings*. These are different blades in Intune.

**Autopilot pre-provisioning** — Know what happens in the technician phase vs the user phase. Pre-provisioning = White Glove = technician stage + user stage.

**MAM without MDM** — App protection policies (MAM) work on completely unmanaged devices. The exam will test whether you know this boundary.

**Update ring deferral limits** — Quality updates max 30 days, feature updates max 365 days. These specific numbers appear in exam questions.

**Detection rules** — Exit 0 = detected. Any other exit = not detected. This is opposite to how most scripts treat errors — easy to get confused under pressure.

---

## Practice Test Recommendations

| Resource | Type | Notes |
|----------|------|-------|
| Microsoft Learn | Free | Official — do these first |
| MeasureUp | Paid | Closest to real exam style |
| Whizlabs | Paid | Good volume of questions |
| ExamTopics | Free | Community-sourced — verify answers independently |

> **Important:** Don't just memorise practice test answers. If you get a question wrong, understand *why* before moving on. The real exam will test the concept differently.

---

## What I Wish I'd Known Earlier

*(This section will be updated after I sit the exam)*

---

## Post-Exam Notes

*(To be completed after exam attempt)*

- Date attempted:
- Score:
- Passed/Failed:
- Strongest domains:
- Weakest domains:
- What I'd study differently:
