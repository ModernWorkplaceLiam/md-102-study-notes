# 🔬 Lab 02 — Win32 App Packaging and Deployment

**Objective:** Package a real application as a Win32 app and deploy via Intune  
**Time:** ~60 minutes  
**Prerequisites:** Microsoft 365 trial tenant, Windows 10/11 VM, IntuneWinAppUtil.exe

---

## What You'll Learn
- How to package an EXE/MSI using IntuneWinAppUtil
- How to write install, uninstall, and detection scripts
- How to upload and configure in Intune
- How to troubleshoot using IME logs

---

## The App We'll Package

**7-Zip 24.x (x64 MSI)** — a simple, free, and reliably silent MSI. Perfect for lab practice.

Download: https://www.7-zip.org/download.html  
Get the x64 MSI build.

---

## Step 1 — Prepare Your Source Folder

Create a folder: `C:\Packaging\7-zip\`

Copy into it:
- `7z-x64.msi` (renamed from the downloaded file)
- `install.ps1` (from the [intune-win32-packaging repo](https://github.com/ModernWorkplaceLiam/intune-win32-packaging/tree/main/apps/7-zip))
- `uninstall.ps1`
- `detection.ps1`

---

## Step 2 — Package with IntuneWinAppUtil

Download [IntuneWinAppUtil.exe](https://github.com/microsoft/Microsoft-Win32-Content-Prep-Tool) and run:

```powershell
IntuneWinAppUtil.exe -c C:\Packaging\7-zip -s 7z-x64.msi -o C:\Output
```

You should see `7z-x64.intunewin` in `C:\Output`.

---

## Step 3 — Upload to Intune

1. **Intune admin center → Apps → Windows → Add → Windows app (Win32)**
2. Upload the `.intunewin` file
3. Fill in app information
4. Set install/uninstall commands (see install.ps1 notes)
5. Configure detection rule using detection.ps1
6. Assign to a test group as **Required**

---

## Step 4 — Monitor the Deployment

On the target device, check:
```
C:\ProgramData\Microsoft\IntuneManagementExtension\Logs\IntuneManagementExtension.log
C:\ProgramData\Microsoft\IntuneManagementExtension\Logs\AgentExecutor.log
```

In Intune, check **Apps → your app → Device install status**.

---

## Observations

*(Add your notes here)*

---

## References
- [intune-win32-packaging repo](https://github.com/ModernWorkplaceLiam/intune-win32-packaging)
- [Microsoft Docs — Win32 app management](https://learn.microsoft.com/en-us/mem/intune/apps/apps-win32-app-management)
