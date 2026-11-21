<div align="center">

# 💳 Retro Game Card: Installer

### RetroArch data packages and the ready-to-flash installer card for [Retro Game Card](https://github.com/retrogc/retrogc-launcher)

[![Release](https://img.shields.io/github/v/release/retrogc/retrogc-installer?style=for-the-badge&color=5CE1B7&labelColor=0A0C12)](https://github.com/retrogc/retrogc-installer/releases/latest)
[![Launcher](https://img.shields.io/badge/Launcher-retrogc--launcher-5CE1B7?style=for-the-badge&labelColor=0A0C12)](https://github.com/retrogc/retrogc-launcher)
[![RetroArch](https://img.shields.io/badge/Powered%20by-RetroArch-5CE1B7?style=flat-square)](https://www.retroarch.com/)

</div>

---

The [Retro Game Card launcher](https://github.com/retrogc/retrogc-launcher) runs games through
RetroArch. This repository provides everything it needs to get RetroArch and its emulator cores
installed on Windows and Linux:

- 📦 the **data packages**: RetroArch plus every libretro core, one zip per system;
- 💳 the **installer card**: a USB stick image that installs the launcher **and** the data, offline;
- 📄 [`retrogc-data.rgc`](retrogc-data.rgc): the **manifest** the launcher reads to download the data.

> [!NOTE]
> 🔲 **MiSTer FPGA** and 🟦 **Recalbox** don't need anything from this repository: they run game
> cards with their own background script ([MiSTer](https://github.com/retrogc/retrogc-launcher#-mister-fpga),
> [Recalbox](https://github.com/retrogc/retrogc-launcher#-recalbox)). Recalbox starts the games with
> its own RetroArch and cores, and simply ignores an installer card that is plugged in.

## 📥 Downloads

Every [**release**](https://github.com/retrogc/retrogc-installer/releases/latest) is named after the
RetroArch version it ships (e.g. `1.22.2`) and contains:

| File | What it is |
|---|---|
| 💳 `retrogc-installer-<version>.img.xz` | **Installer card** image (exFAT, labelled `RETROGC`, ≈ 1.5 GB): both launchers + both data packages |
| 🪟 `retrogc-data-<version>-windows.zip` | RetroArch + cores for Windows x64 |
| 🐧 `retrogc-data-<version>-linux.zip` | RetroArch + cores for Linux x64 |

## 🚀 Using the installer card

The simplest way to set up a computer:

1. **Download** `retrogc-installer-<version>.img.xz` from the
   [latest release](https://github.com/retrogc/retrogc-installer/releases/latest).
2. **Flash** it to a USB stick of **2 GB or more** with [**balenaEtcher**](https://etcher.balena.io/)
   (Windows, Linux, macOS), or with
   [`retrogc-image.sh --restore`](https://github.com/retrogc/retrogc-launcher/blob/main/Platforms/Linux/retrogc-image.sh)
   on Linux. ⚠️ This erases the stick.
3. **Run** `retrogc-launcher.exe` (Windows) or `retrogc-launcher` (Linux) **from the stick** and click
   **Install**. The launcher installs itself with a menu shortcut, installs RetroArch and its cores,
   then restarts from the installed copy. No internet connection needed.
4. **Play**: remove the stick and insert a [game card](https://github.com/retrogc/retrogc-launcher#-the-rgc-card-format).

> [!TIP]
> Keep the stick: insert it into a computer where the launcher is already installed and it offers
> to **update** the launcher and the data.

> [!NOTE]
> 🐧 On Linux, if double-clicking `retrogc-launcher` doesn't start it, right-click it →
> **Run as a Program**, or run `/media/$USER/RETROGC/retrogc-launcher` from a terminal.

📖 How installer cards work: see
[Installer cards](https://github.com/retrogc/retrogc-launcher#-installer-cards) in the launcher README.

## 🌐 Online install (no installer card)

Started without data, the launcher opens its data installer, reads
[`retrogc-data.rgc`](retrogc-data.rgc) from this repository and downloads the right zip from the
releases. The zip is extracted to:

| 🐧 Linux | 🪟 Windows |
|---|---|
| `~/.local/share/retrogc/data/<version>/` | `%LOCALAPPDATA%\retrogc\data\<version>\` |

For an offline machine without an installer card, download the zip and `retrogc-data.rgc`, put the
manifest next to the zip with its `url` changed to the zip file name, and pick it in the launcher's
installer (<kbd>F12</kbd> → **RetroArch data package** → **Install…** → **Local file**).

## 📄 The manifest

```json
{
  "default": "1.22.2",
  "versions": [
    {
      "version": "1.22.2",
      "platforms": {
        "win-x64":   { "data": [ { "name": "retrogc-data", "file": "retrogc-data-1.22.2-windows.zip", "url": "https://github.com/retrogc/retrogc-installer/releases/download/1.22.2/retrogc-data-1.22.2-windows.zip" } ] },
        "linux-x64": { "data": [ { "name": "retrogc-data", "file": "retrogc-data-1.22.2-linux.zip",   "url": "https://github.com/retrogc/retrogc-installer/releases/download/1.22.2/retrogc-data-1.22.2-linux.zip" } ] }
      }
    }
  ]
}
```

| Field | Description |
|---|---|
| `default` | Version the launcher installs. Must match one of `versions`. |
| `versions[].version` | RetroArch version, also the install folder name. |
| `platforms` | `win-x64` and `linux-x64`. |
| `data[].name` | `retrogc-data` for the data package (on an installer card, `retrogc-launcher` entries also give the launcher version). |
| `data[].file` | Archive file name. |
| `data[].url` | Download URL, or a path relative to the manifest for a local install. |

## 🛠️ Making a release

Releases are built by the [**Build Release**](.github/workflows/release.yml) workflow
(*Actions → Build Release → Run workflow*), given a RetroArch version such as `1.22.2`. It:

1. downloads RetroArch and its cores for Linux and Windows from the
   [libretro buildbot](https://buildbot.libretro.com/stable/) and packs them as
   `retrogc-data-<version>-{linux,windows}.zip`;
2. builds the installer card with
   [`retrogc-image.sh --build-installer`](https://github.com/retrogc/retrogc-launcher#%EF%B8%8F-building-your-own-installer-card-optional),
   using these two zips and the **latest launcher release**;
3. publishes the GitHub release `retrogc-installer v<version>` (tag `<version>`) with the three files.

> [!IMPORTANT]
> The workflow doesn't touch [`retrogc-data.rgc`](retrogc-data.rgc). Once the release is out, add the
> new version to `versions` and set `default` to it, so the launcher's online install picks it up.

> [!NOTE]
> The installer card bundles the launcher release that was the latest **when the workflow ran**.
> After a new launcher release, run the workflow again to refresh the card.
