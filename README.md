# 🎮 GameStretchUnlock

**Stretches game content to fill the entire display instead of showing letterbox black bars when using RedMagic's "Free Screen" feature.**

[![Android](https://img.shields.io/badge/Android-14%2B-green)](#requirements)
[![LSPosed](https://img.shields.io/badge/LSPosed-API%2082-blue)](#installation)
[![License](https://img.shields.io/badge/License-MIT-yellow)](LICENSE)
[![Tested](https://img.shields.io/badge/Tested%20on-RedMagic%2011%20Pro-red)](#tested-devices)

---

## 📖 What does it do?

RedMagic's Game Space **"Free Screen"** feature lets you play portrait-only games in landscape using custom aspect ratios (4:3, 16:9, 21:9, 32:9). However, when the selected ratio doesn't match the physical screen, **black bars** appear on the sides.

This module ignores the selected ratio and **stretches the content to fill the entire screen**. Characters may look slightly stretched horizontally/vertically, but the game becomes **fullscreen**.

| ❌ Before | ✅ After |
|-----------|---------|
| Black bars + game at 4:3 | **Fullscreen**, content stretched |
| Changing ratio is pointless | Game always fills the screen |

---

## ✨ Features

- 🖼️ **Fullscreen stretch** — Black bars disappear, game fills the screen
- 🎮 **Landscape + portrait auto-detect** — Handles both orientations
- 🤖 **Automatic game list** — Any game with "Free Screen" enabled in Game Space
- ⚡ **Instant + 1s polling** — Quickly catches frame changes
- 🛡️ **Anti-cheat safe** — No hooks into the game process (PUBG, Valorant, etc. safe)
- 🧠 **Aspect validation** — Automatically skips temporary/loading frames

---

## 🧪 Tested devices

| Device | ROM | Android | Status |
|--------|-----|---------|--------|
| RedMagic 11 Pro (NX809J) | REDMAGICOS 11.0.19_EA | 16 | ✅ Fully working |

Tested games:
- ✅ **Venom Survive** — Unity, landscape
- ✅ **PUBG Mobile** — Unreal, landscape
- ✅ **Other Game Space games** — automatically

Tried it on a different device? Let us know via an [issue](../../issues).

---

## 📋 Requirements

- **Root:** Magisk v26+ (or KernelSU / APatch)
- **Framework:** [LSPosed](https://github.com/LSPosed/LSPosed) v1.9+ (API 82)
- **Android:** 14+ (tested on Android 16)
- **Device:** RedMagic/Nubia models with Game Ratio feature

---

## 🚀 Installation

1. Download the latest `GameStretchUnlock-vX.Y.apk` from the [Releases](../../releases) page.
2. Install the APK on your device (via file manager).
3. Open **LSPosed Manager**.
4. Go to **Modules** → find **GameStretchUnlock** → toggle it **ON**.
5. Tap **Scope** → check **two boxes:**
   - ☑️ **System Framework** (android)
   - ☑️ **Game Space** (cn.nubia.gameassist)
   - ❌ **Do NOT check the game** (for anti-cheat safety)
6. **Reboot** (required for system_server hooks).
7. Add the game to Game Space → **Free Screen** → pick any ratio.
8. Launch the game → it should be **stretched to fullscreen**.

---

## ⚙️ Configuration

### Automatic game list

The module automatically detects all games with **"Free Screen" enabled** in Game Space. Just add your game to Free Screen and you're done.

### Manual game addition

For games not auto-detected, add them manually:

```
su -c "settings put global gamestretch_packages 'com.package1,com.package2,'"
```

**Clear the list:**

```
su -c "settings delete global gamestretch_packages"
```

---

## 🔧 Troubleshooting

### Black bars still visible

- Make sure **System Framework** scope is enabled in LSPosed.
- Did you **reboot**? (required for system_server hooks)
- Check logs:

```
su -c "logcat -d | grep GameStretchUnlock | tail -20"
```

Expected: `Letterbox.applySurfaceChanges -> no-op`

### Image shifted / over-zoomed

- A temporary frame may have been captured. Try the latest version.
- Check logs for `ATLA aspect=...` lines (skipped frames).

### Side arrows still visible

- Enable **Game Space** (cn.nubia.gameassist) scope in LSPosed.

### Bootloop

- Boot into **Safe Mode** (hold Volume Down while booting).
- LSPosed will automatically disable the module.
- Or from TWRP/Recovery:

```
rm -rf /data/adb/lspd/
```

### Effect persists after disabling the module

system_server hooks remain in memory. A **REBOOT** is required after toggling off.

---

## 🏗️ How it works (brief)

```
Game Space (Free Screen enabled)
    ↓
system_server (WMS)
    ├─ Letterbox.applySurfaceChanges → no-op       (hide black bars)
    ├─ WindowStateAnimator.prepareSurfaceLocked → after-hook
    │   ├─ ActivityRecord layer → setMatrix(S, 0, 0, SY)
    │   └─ Task container → setWindowCrop(fullscreen)
    └─ 1s polling (catches late frame changes)
    ↓
SurfaceFlinger → stretched fullscreen image
```

**LSPosed scope:** Only `android` + `cn.nubia.gameassist`.
**No hooks into the game process** → anti-cheat safe.

---

## 📄 License

MIT — see [LICENSE](LICENSE)

---

## 🙏 Credits

- [LSPosed](https://github.com/LSPosed/LSPosed) team
- RedMagic / Nubia community

---

**Like it? ⭐ Give it a star!**
