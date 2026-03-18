# 🔬 Lab 01 — Setting Up Windows Autopilot

**Objective:** Configure Windows Autopilot for user-driven Entra join deployment  
**Time:** ~45 minutes  
**Prerequisites:** Microsoft 365 trial tenant, a test device or VM

---

## What You'll Learn
- How to register a device with Autopilot
- How to create a deployment profile
- How to assign the profile and test the flow
- What the OOBE experience looks like end-to-end

---

## Step 1 — Get Your Device Hardware Hash

On the target device, run the following in an elevated PowerShell session:

```powershell
Install-Script -Name Get-WindowsAutoPilotInfo -Force
Get-WindowsAutoPilotInfo -OutputFile C:\AutopilotHWID.csv
```

This generates a CSV with the hardware hash needed to register the device.

---

## Step 2 — Import the Device into Intune

1. Open [Intune admin center](https://intune.microsoft.com)
2. Navigate to **Devices → Enroll devices → Windows enrollment → Devices**
3. Click **Import** and upload `AutopilotHWID.csv`
4. Wait for the sync to complete (can take 5–15 minutes)

> **Exam tip:** You can also import via Microsoft 365 Admin Center or via a reseller/OEM for new devices. The exam tests which method is appropriate for which scenario.

---

## Step 3 — Create a Deployment Profile

1. **Devices → Enroll devices → Windows enrollment → Deployment profiles**
2. Click **Create profile → Windows PC**
3. Configure:
   - **Deployment mode:** User-driven
   - **Join to Azure AD as:** Azure AD joined
   - **OOBE settings:** Hide privacy settings, hide EULA, auto-configure keyboard
4. Assign to a test group containing your device

---

## Step 4 — Create an Enrolment Status Page (ESP)

1. **Devices → Enroll devices → Windows enrollment → Enrollment Status Page**
2. Create a new profile
3. Set **Show app and profile installation progress** to **Yes**
4. Add apps you want tracked (e.g. Company Portal)
5. Assign to the same test group

---

## Step 5 — Test the Deployment

Reset the test device (or create a fresh VM snapshot) and power on. You should see:

- Microsoft logo on boot
- Your organisation's branded sign-in page (if configured)
- User prompted to sign in with their M365 credentials
- ESP progress page showing app installation
- Desktop after completion

---

## Observations & Notes

*(Add your own notes here as you complete the lab)*

| What I observed | Why it happened |
|----------------|----------------|
| | |

---

## Common Issues

| Problem | Fix |
|---------|-----|
| Device not appearing after import | Wait 15 mins, then sync manually in Intune |
| OOBE shows generic Windows setup | Profile not assigned or device not in group |
| ESP stuck on "Identifying" | Device not registered with Autopilot |
| Sign-in fails | User not licenced for Intune |

---

## References
- [Microsoft Docs — Autopilot user-driven mode](https://learn.microsoft.com/en-us/autopilot/user-driven)
- [Get-WindowsAutoPilotInfo script](https://www.powershellgallery.com/packages/Get-WindowsAutoPilotInfo)
