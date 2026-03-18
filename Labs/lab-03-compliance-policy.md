# 🔬 Lab 03 — Creating and Testing a Compliance Policy

**Objective:** Create a Windows compliance policy, assign it, and observe how non-compliance is handled  
**Time:** ~30 minutes  
**Prerequisites:** Microsoft 365 trial tenant, enrolled Windows 10/11 test device

---

## What You'll Learn
- How to create a compliance policy in Intune
- What settings are commonly tested in the exam
- How non-compliance is surfaced in the Intune portal
- How compliance feeds into Conditional Access

---

## Step 1 — Create a Compliance Policy

1. **Intune admin center → Devices → Compliance**
2. Click **Create policy → Windows 10 and later**
3. Configure the following settings (these are exam-relevant):

| Setting | Value | Why |
|---------|-------|-----|
| Minimum OS version | 10.0.19041 (20H2) | Tests OS currency |
| BitLocker required | Yes | Common enterprise requirement |
| Secure boot required | Yes | Firmware integrity |
| Firewall required | Yes | Basic security hygiene |
| Antivirus required | Yes | Defender status |
| Password required | Yes | Access control |

4. Set **Actions for noncompliance:**
   - 0 days → Mark device as non-compliant
   - 3 days → Send email notification
   - 7 days → Retire device (leave this off for lab)

5. Assign to your test device group

---

## Step 2 — Trigger Non-Compliance

On your test device, temporarily disable the firewall:

```powershell
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False
```

Wait for the next Intune sync or force one:
```powershell
Start-Process "C:\Program Files (x86)\Microsoft Intune Management Extension\ClientHealthEval.exe"
```

---

## Step 3 — Observe in Intune

- Go to **Devices → your test device → Device compliance**
- You should see the firewall setting flagged as non-compliant
- Note how quickly the status updates

---

## Step 4 — Remediate

Re-enable the firewall and observe the device return to compliant state:

```powershell
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
```

---

## Key Exam Points from This Lab

- Compliance policies don't *fix* issues — they *report* them
- Actions for non-compliance are time-delayed — 0 days is immediate marking only
- A device can be compliant in Intune but still have issues (depends what you check)
- Compliance status feeds into Conditional Access — non-compliant = blocked if CA policy requires compliant device

---

## Observations

*(Add your notes here)*

---

## References
- [Microsoft Docs — Device compliance policies](https://learn.microsoft.com/en-us/mem/intune/protect/device-compliance-get-started)
