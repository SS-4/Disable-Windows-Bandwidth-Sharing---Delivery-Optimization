# Disable-Windows-Bandwidth-Sharing---Delivery-Optimization
## Disable-WindowsDeliveryOptimization

A one-liner PowerShell script to disable Windows Delivery Optimization P2P upload seeding.

## What is Delivery Optimization?

Delivery Optimization is a Windows feature introduced in Windows 10 that turns your PC into a **peer-to-peer (P2P) seeding node** for Windows Updates, Microsoft Store apps, and driver updates.

When enabled (default setting: `DODownloadMode = 3`), your computer:
- Downloads updates from Microsoft servers **AND** other Windows PCs on the internet
- **Uploads** parts of those updates to other Windows PCs without asking for explicit permission
- Consumes your upload bandwidth continuously

## Why This Is an Abuse by Microsoft

| Issue | Description |
|-------|-------------|
| **No explicit consent** | Enabled by default during Windows installation |
| **Hides the behavior** | Called "Delivery Optimization" — sounds harmless |
| **No clear UI warning** | Settings app doesn't explain "this will use your bandwidth to upload to strangers" |
| **Mobile users affected** | Laptops on metered connections can burn through data caps |
| **No per-session control** | Cannot approve individual uploads |
| **Benefits Microsoft's CDN costs** | Microsoft saves money on content delivery networks by using your bandwidth |

> **In plain English:** Microsoft uses your internet connection and electricity to host their update servers for free, without asking.

## What This Script Does

The script sets `DODownloadMode = 0` in the Windows registry, which:

- ✅ **Disables all P2P uploads** (your PC stops seeding to others)
- ✅ **Keeps Windows Update working** (downloads still come from Microsoft servers via HTTP)
- ✅ **Doesn't break anything** — the Delivery Optimization service still runs, but only for HTTP downloads
- ✅ **Persists across reboots**

### What `DODownloadMode` Values Mean

| Value | Mode | Your PC |
|-------|------|---------|
| 0 | Off | Downloads from Microsoft only. **No uploads.** ← This script |
| 1 | LAN | P2P only on local network |
| 2 | LAN + domain | P2P within your organization |
| 3 | Internet (default) | Downloads AND uploads to anyone on the internet |

## How to Use

### Method 1: One-liner (Copy & Paste)

Open **PowerShell as Administrator**, then paste:

```powershell
New-Item -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\DeliveryOptimization\Config" -Force | Out-Null; Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\DeliveryOptimization\Config" -Name "DODownloadMode" -Value 0 -Type DWord -Force
```


## Revert (Re-enable P2P seeding)

If you want to opt back into sharing your bandwidth with other Windows PCs:

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\DeliveryOptimization\Config" -Name "DODownloadMode" -Value 3 -Force
```


