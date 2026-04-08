# TerrainOS

> A radically stripped Windows 11 with a custom shell as desktop replacement. No Explorer, no bloat — just the minimum Windows needs to run games natively, plus a custom frontend written in C using Win32 and Direct2D.

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
[![Build](https://github.com/andreasfilimon/TerrainOS/actions/workflows/build.yml/badge.svg)](https://github.com/andreasfilimon/TerrainOS/actions/workflows/build.yml)
[![GitHub Issues](https://img.shields.io/github/issues/andreasfilimon/TerrainOS)](https://github.com/andreasfilimon/TerrainOS/issues)
[![GitHub PRs](https://img.shields.io/github/issues-pr/andreasfilimon/TerrainOS)](https://github.com/andreasfilimon/TerrainOS/pulls)

---

## What is TerrainOS?

TerrainOS replaces the Windows shell entirely. Boot into a PS5-style game launcher instead of a desktop. Everything is built around one goal: **get into a game as fast as possible with zero background noise.**

The end product is a bootable ISO that can be installed on real hardware.

---

## Architecture

```
┌─────────────────────────────────┐
│   Contour (Shell/Desktop)       │  ← Custom Win32 + Direct2D shell
│   Relief (Launcher), Traverse   │
├─────────────────────────────────┤
│   Datum (Boot-Updater)          │  ← Gatekeeper before shell loads
│   Bearing (Driver Manager)      │
├─────────────────────────────────┤
│   DirectX, WASAPI, Win32, COM   │  ← Windows (kept)
├─────────────────────────────────┤
│   NT-Kernel + HAL + Drivers     │  ← Windows (kept)
└─────────────────────────────────┘
```

## Component Map

| Component | Codename | Description |
|-----------|----------|-------------|
| OS (overall) | **Terrain** | The landscape — the entire system |
| Shell/Desktop | **Contour** | The visible surface of the terrain |
| Boot-Updater/Gatekeeper | **Datum** | The reference point everything is measured from |
| Game Launcher | **Relief** | What stands out on the map |
| Driver Manager | **Bearing** | Points in the right direction |
| ISO Builder | **Survey** | Maps and measures the system |
| Theming/Layout | **Grid** | The grid that orders everything |
| File Manager | **Traverse** | Traverses the terrain |
| Docs/Wiki | **Legend** | Explains what's on the map |
| Performance Monitor | **Benchmark** | Measurement point in the field |
| Installer | **Baseline** | The baseline of a survey |
| Settings | **Calibration** | Fine-tuning of the instruments |

---

## Tech Stack

- **Language:** C (C17)
- **Compiler:** MSVC (`cl.exe`) or MinGW (`gcc`)
- **UI Rendering:** Direct2D + DirectWrite (hardware-accelerated)
- **Windowing:** Win32 API (`CreateWindowEx`, `WndProc`)
- **Audio:** Windows Core Audio API (WASAPI)
- **Networking:** Native WiFi API (`wlanapi.h`), WinHTTP
- **Build System:** CMake
- **No framework. No runtime. No dependencies.**

---

## Roadmap

| Milestone | Description | Status |
|-----------|-------------|--------|
| M1 | Proof of Concept — Win32 window + Direct2D + shell registration | Planned |
| M2 | Minimal Shell — panel, wallpaper, context menu | Planned |
| M3 | Game Launcher — scan libraries, grid view, launch | Planned |
| M4 | System Controls — volume, WiFi, battery, tray | Planned |
| M5 | File Manager — browse, copy, move, delete | Planned |
| M6 | Gatekeeper / Boot Updater — splash, manifest, DISM | Planned |
| M7 | ISO Builder — strip Windows, inject shell, oscdimg | Planned |
| M8 | Polish — animations, theming, controller, overlays | Ongoing |

---

## Building

### Prerequisites

- Windows 10/11
- MSVC (via Visual Studio Build Tools) **or** MinGW-w64
- CMake 3.20+

### MSVC

```bat
cl /O2 /W4 contour/main.c contour/desktop.c contour/panel.c contour/d2d_renderer.c ^
   /Fe:contour.exe /link d2d1.lib dwrite.lib user32.lib gdi32.lib shell32.lib ole32.lib
```

### MinGW

```bat
gcc -O2 -Wall contour/main.c contour/desktop.c contour/panel.c contour/d2d_renderer.c ^
    -o contour.exe -ld2d1 -ldwrite -luser32 -lgdi32 -lshell32 -lole32
```

---

## Safety First

> **Never test on your main Windows installation.**

Always keep `baseline/unregister_shell.bat` accessible. The shell must be able to fall back to `explorer.exe` on crash. See [CONTRIBUTING.md](CONTRIBUTING.md) for development safety guidelines.

---

## Contributing

We welcome contributions. Please read [CONTRIBUTING.md](CONTRIBUTING.md) before opening a PR.

- Bug reports: [Issue Tracker](https://github.com/andreasfilimon/TerrainOS/issues)
- Feature proposals: [Discussions](https://github.com/andreasfilimon/TerrainOS/discussions)
- Documentation: `legend/` folder

---

## License

TerrainOS is licensed under the **GNU General Public License v3.0**.
See [LICENSE](LICENSE) for details.
