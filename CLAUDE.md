# TerrainOS — Claude Code Instructions

## Vision

Ein radikal gestripptes Windows 11 mit einer eigenen Shell als Desktop-Ersatz. Kein Explorer, kein Bloat — nur das Minimum das Windows braucht um Spiele nativ auszuführen, plus ein eigenes Frontend in C + Win32/Direct2D.

Das Endprodukt ist eine bootbare ISO die auf echte Hardware installiert werden kann.

## Naming Universe: Kartografie

| Komponente | Name | Bedeutung |
|---|---|---|
| **OS (Gesamt)** | **Terrain** | Die Landschaft — das gesamte System |
| **Shell/Desktop** | **Contour** | Die sichtbare Oberfläche des Terrains |
| **Boot-Updater/Gatekeeper** | **Datum** | Der Referenzpunkt von dem alles gemessen wird |
| **Game Launcher** | **Relief** | Was auf der Karte hervorsticht |
| **Treiber Manager** | **Bearing** | Gibt die Richtung vor |
| **ISO Builder** | **Survey** | Vermisst und kartiert das System |
| **Theming/Layout** | **Grid** | Das Raster das alles ordnet |
| **Dateimanager** | **Traverse** | Durchquert das Terrain |
| **Docs/Wiki** | **Legend** | Erklärt was auf der Karte zu sehen ist |
| **Performance Monitor** | **Benchmark** | Messpunkt im Gelände |
| **Installer** | **Baseline** | Die Ausgangslinie einer Vermessung |
| **Settings** | **Calibration** | Feinjustierung der Instrumente |

## Architektur

```
┌─────────────────────────────────┐
│   Contour (Shell/Desktop)       │  ← Dieses Projekt
│   Relief (Launcher), Traverse   │
├─────────────────────────────────┤
│   Datum (Boot-Updater)          │  ← Dieses Projekt
│   Bearing (Treiber Manager)     │
├─────────────────────────────────┤
│   DirectX, WASAPI, Win32, COM   │  ← Windows (behalten)
├─────────────────────────────────┤
│   NT-Kernel + HAL + Treiber     │  ← Windows (behalten)
└─────────────────────────────────┘
```

## Tech Stack

- **Sprache:** C (C17)
- **Compiler:** MSVC (cl.exe) oder MinGW
- **UI Rendering:** Direct2D + DirectWrite (hardware-beschleunigt, 2D)
- **Windowing:** Win32 API direkt (CreateWindowEx, WndProc)
- **Audio Control:** Windows Core Audio API (WASAPI Endpunkte)
- **Netzwerk-UI:** Native WiFi API (wlanapi.h)
- **Updates:** HTTPS Manifest + DISM für Patches, pnputil für Treiber
- **Build System:** CMake
- **Kein Framework, kein Runtime, keine Dependencies**

## Branch-Strategie

- `main` — Stable Releases, kein direkter Push
- `develop` — Integration, alle Features landen hier zuerst
- `feature/<name>` — Neue Features, von develop abzweigen
- `fix/<name>` — Bugfixes, von develop abzweigen
- `hotfix/<name>` — Kritische Fixes, von main abzweigen
- `release/<version>` — Release-Vorbereitung

**Immer von `develop` abzweigen.**

## Projektstruktur

```
terrain-os/
├── CLAUDE.md
├── contour/               ← Shell/Desktop
│   ├── main.c
│   ├── desktop.c
│   ├── panel.c
│   ├── launcher.c
│   ├── filemanager.c
│   ├── settings.c
│   ├── wallpaper.c
│   ├── d2d_renderer.c
│   ├── win32_helpers.c
│   └── include/
│       ├── contour.h
│       ├── d2d_renderer.h
│       └── config.h
├── datum/                 ← Boot-Updater/Gatekeeper
│   ├── datum.c
│   ├── updater.c
│   ├── network.c
│   └── dism_wrapper.c
├── bearing/               ← Treiber Manager
│   └── bearing.c
├── baseline/              ← Installer
│   ├── register_shell.bat
│   ├── unregister_shell.bat
│   └── safe_mode_fix.bat
├── survey/                ← ISO Builder
│   ├── strip.ps1
│   ├── inject_shell.ps1
│   ├── build_iso.ps1
│   └── packages.txt
├── update-server/
│   ├── manifest.json
│   └── serve.py
└── legend/                ← Docs
    ├── architecture.md
    ├── boot-sequence.md
    └── update-protocol.md
```

## Kompilieren

### MSVC
```bat
cl /O2 /W4 contour/main.c contour/desktop.c contour/panel.c contour/d2d_renderer.c /Fe:contour.exe /link d2d1.lib dwrite.lib user32.lib gdi32.lib shell32.lib ole32.lib
```

### MinGW
```bat
gcc -O2 -Wall contour/main.c contour/desktop.c contour/panel.c contour/d2d_renderer.c -o contour.exe -ld2d1 -ldwrite -luser32 -lgdi32 -lshell32 -lole32
```

## Shell registrieren

```bat
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Shell /t REG_SZ /d "C:\TerrainOS\datum.exe" /f
```

## Sicherheitsregeln

- IMMER `baseline/unregister_shell.bat` bereithalten
- IMMER auf Test-Partition entwickeln, nie auf dem Haupt-Windows
- Shell MUSS bei Crash auf `explorer.exe` zurückfallen
- Crash-Counter: 3 Crashes → explorer.exe erzwingen

## Design-Richtlinien

- **Dunkel:** Schwarzer/dunkelgrauer Hintergrund
- **Minimal:** Keine überflüssigen Elemente
- **Schnell:** Jede Aktion < 100ms Reaktionszeit
- **Konsolenfeeling:** PS5 Home Screen, nicht Windows Desktop
- **Schrift:** Segoe UI oder eingebettete eigene Font
- **Akzentfarbe:** Konfigurierbar, Default Blau oder Grün
