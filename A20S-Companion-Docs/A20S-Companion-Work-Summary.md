# A20S Companion — Work Summary

**Date:** 20 Oct 2026  **Phone:** Samsung Galaxy A20s (SM-A207F, Android 11)  **PC:** TICS25 (Windows 11)
**Project folder (PC):** `C:\Users\User\A20S-Companion`

> **Tanglish summary:** Unga Galaxy A20s ippo USB cable vazhiya PC-oda companion-aa work aagudhu.
> **A20S Companion** app (home screen icon) vechu PC stats paakkalaam, shortcuts anuppalaam, mouse/keyboard-a control pannalaam, PC screen-a paakkalaam.
> VLC, Brave, YouTube, Moonlight-um adhe app-la irundhu open pannalaam. Indha PC-la cable pottadhume thaana connect aagum.
> Vera PC-ku `A20S-Companion-Setup.zip` vechu oru thadava setup pannunga. Unga pazhaya apps (Brave, YouTube, music apps) edhaiyum touch pannala.

---

## 1. What is on the phone now

| App | Source | Purpose |
|---|---|---|
| **A20S Companion** (`com.a20s.companion` v1.3) | Built from source in this project | One dashboard for everything; home-screen icon |
| **VLC** 3.7.1 (`org.videolan.vlc`) | videolan.org, SHA-256 and signature verified | Media player |
| **Moonlight** 12.2 (`com.limelight`) | GitHub, digest and signer (Limelight Team) verified | Low-latency PC screen stream |
| Brave, YouTube, Samsung Music, Youtify, Calculator, carrier app | Already there | **Not touched** |

Installer APKs were deleted after each install was verified. Google Play Store was not used. The phone was not rooted, reset or flashed.

## 2. How to use (daily)

1. Connect the phone to the PC with the USB cable.
2. Tap **A20S Companion** on the phone home screen.
3. The status pill shows **"TICS25 · USB"** (green) when connected and **"Windows PC Offline"** (red) when not.

| Button | Needs PC? | What it does |
|---|---|---|
| PC Performance | Yes | Live CPU, RAM, disk, network, battery |
| Windows Shortcuts | Yes | Media keys, volume, VS Code / Terminal / Task Mgr / Files, Desktop, Alt+Tab, Lock PC (tap twice) |
| Mouse & Keyboard | Yes | Touchpad (tap = click, 2-finger tap = right-click, 2-finger drag = scroll), type text, keys |
| Remote Desktop | Yes | See the PC screen at about 12–15 fps; tap = click, hold = right-click |
| Moonlight | Yes (USB tethering + Sunshine) | Low-latency PC screen (hardware decode) |
| USB tethering | – | Opens the phone's tethering switch |
| VLC / Brave / YouTube | No | Opens the phone app |
| Settings | No | Status, re-check, Add to Home screen, Stop PC companion, help |

**Moonlight steps:** USB tethering ON (button in the app) → on the PC run `windows\scripts\start-sunshine.ps1` → app → Moonlight → **A20S-PC** → Desktop. Afterwards run `stop-sunshine.ps1` and turn tethering off.

## 3. PC side (TICS25)

- **Auto-start:** the companion server starts at Windows sign-in (Task Scheduler: *A20S Companion (USB phone link)*). When the cable is plugged in, the USB link (`adb reverse`) is set up automatically.
  - Remove: `windows\scripts\remove-autostart.ps1`
- **Manual:** Desktop shortcuts **Start A20S Companion** / **Stop A20S Companion**.
- **Sunshine** 2026.914 (signed MSI, GitHub). Its Windows service is set to *Manual* and stopped; Sunshine runs only when you start it. The firewall allows it **only on the phone's USB-tether adapter**, which was checked: blocked from Wi-Fi/LAN.
- The USB-tether adapter metric is set to 9999, so the PC never sends its internet through the phone.

## 4. Use with another PC

1. Copy `C:\Users\User\A20S-Companion\dist\A20S-Companion-Setup.zip` to that PC and unzip it.
2. Double-click **Setup.cmd**. It asks Y/N before each step:
   - installs Python, ADB and (optionally) ffmpeg via winget if they are missing
   - copies files to `%LOCALAPPDATA%\A20S-Companion` (no admin needed)
   - sets up auto-start
   - pairs the phone
3. On the phone, tap **Allow** on "Allow USB debugging?" and tick *Always allow*.
4. From then on, plugging in the cable connects. The app keeps a separate key for each PC and shows the PC name.

Remove from a PC with **Uninstall.cmd**.
*Not yet tried on a second PC.*

## 5. Security choices

- The PC server listens on **127.0.0.1 only**. The phone reaches it only through the USB cable (`adb reverse`).
- Every action needs a pairing token. Commands come from a fixed allowlist, with no arbitrary shell. Key presses are allowlisted and text is limited to 500 characters.
- The phone app has only the INTERNET permission. Cleartext traffic is allowed only to 127.0.0.1. It has no background service.
- Remote Desktop screen capture runs only while that screen is open and stops 5 seconds after.
- Claude Code's safety checks blocked two design ideas, and neither was used:
  - exposing the server on the USB-tether network (this would have removed the ADB dependency)
  - a "click Allow on the PC" pairing dialog

## 6. Test results

| Test | Result |
|---|---|
| VLC install + playback | PASS |
| App: offline / online status (no false "connected") | PASS |
| App: back navigation, home-screen icon | PASS |
| VLC / Brave / YouTube / Moonlight / tethering buttons | PASS |
| PC Performance, Vol + shortcut | PASS |
| Remote Desktop view (12–15 fps) | PASS. Tap-to-click not confirmed on the phone. |
| Mouse/keyboard (server path) | PASS. Not exercised from the phone. |
| Cable re-plug → auto reconnect | PASS |
| Multi-PC token logic (on this PC) | PASS |
| Sunshine + Moonlight over USB tethering | PASS (paired, streaming, hardware decode; latency under ~0.3 s vs 2.3–2.8 s for the VLC stream) |
| Kali Linux (Termux) | **Not installed** |
| Second monitor "Extend" (VDD driver) | **Not installed yet** (approved; ready to do) |
| **Production mode (without USB debugging)** | **FAIL**: PC Performance, Shortcuts, Mouse & Keyboard and Remote Desktop need USB debugging. Moonlight works without it. |

## 7. Known issues

- **cnss-daemon (Samsung/Qualcomm Wi-Fi system service)** has been stuck in an error loop using about 60–70% of one CPU core since the phone restart. A Wi-Fi off/on did not fix it. This was not caused by the project, but it makes the phone warmer and slower. **Next step: another restart (needs your OK).**
- VLC once ran at 100% CPU in the background for about 18 minutes after being opened. It was force-stopped (data kept) and did not happen again.
- A leftover test token "fakepc" is stored in the app. It is harmless.

## 8. Things you need to do

- [ ] Brave → ⋮ → Downloads → remove the pending `live.mjpg` (53 MB, from an earlier test).
- [ ] Close the empty "Untitled – Notepad" window that the test opened. Your other Notepad document was not touched.
- [ ] Check that the ChatGPT window is fine (one test click landed on its text area).
- [ ] Keep Developer Options / USB debugging **ON** for now. The PC features need it.

## 9. Pending decisions

1. Restart the phone to fix cnss-daemon?
2. Install VDD so Moonlight can work as a real **extended second monitor**?
3. Install Kali Linux (Termux from F-Droid + NetHunter Rootless)?
4. Allow the USB-tether server design, so the PC features work **without** USB debugging?

## 10. Where things are

| What | Path |
|---|---|
| Full test log | `C:\Users\User\A20S-Companion\reports\03-install-log.md` |
| Feasibility research | `...\reports\02-feasibility.md` |
| Security notes | `...\security\` |
| Phone app source + build | `...\android\companion-app\` (`build.ps1`) |
| PC server | `...\windows\dashboard\server.py` |
| PC scripts | `...\windows\scripts\` |
| Other-PC setup | `...\dist\A20S-Companion-Setup.zip` |
| This document | PC: `Desktop\A20S-Companion-Docs\` · Phone: `Documents/A20S-Companion/` |
