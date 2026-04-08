# Project: TerrainOS

## Vision
Ein radikal gestripptes Windows 11 mit einer eigenen Shell als Desktop-Ersatz. Kein Explorer, kein Bloat — nur das Minimum das Windows braucht um Spiele nativ auszuführen, plus ein eigenes Frontend in C + Win32/Direct2D.

Das Endprodukt ist eine bootbare ISO die auf echte Hardware installiert werden kann.

## Naming Universe: Kartografie

Alle Komponenten des Projekts werden aus dem Kartografie/Vermessungs-Universum benannt.
Das schafft eine konsistente Markensprache und endlos erweiterbare Namen für neue Features.

### Aktuelle Zuordnung

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

### Verfügbare Namen für zukünftige Komponenten

**Fundamentals:** Meridian, Parallel, Equator, Latitude, Longitude, Coordinate, Scale, Compass, North, South, East, West
**Surveying:** Triangulation, Azimuth, Elevation, Altitude, Declination, Station, Waypoint, Marker
**Terrain:** Ridge, Valley, Summit, Peak, Slope, Plateau, Basin, Canyon, Crest, Saddle, Ledge, Bluff, Mesa, Cliff, Ravine, Gorge, Hollow
**Mapping:** Projection, Mercator, Quadrant, Sector, Zone, Region, Domain, Boundary, Border, Perimeter, Extent
**Lines & Shapes:** Arc, Vector, Polygon, Vertex, Node, Edge, Path, Route, Trace, Outline, Gradient, Isoline
**Navigation:** Heading, Course, Fix, Plot, Chart, Leg, Transit, Passage, Drift, Offset, Origin, Destination, Approach
**Tools:** Theodolite, Sextant, Astrolabe, Protractor, Divider, Plotter, Stylus, Graticule, Reticle, Crosshair, Finder, Scope, Lens
**Rendering:** Hatch, Shade, Tint, Layer, Overlay, Mosaic, Tile, Raster, Resolution, Detail, Inset, Symbol, Glyph, Label, Annotation
**Water:** Shoreline, Coast, Harbor, Channel, Strait, Delta, Tributary, Watershed, Estuary, Inlet, Bay, Cape, Reef, Depth, Fathom, Sounding
**Discovery:** Expedition, Pioneer, Frontier, Outpost, Uncharted, Reconnaissance, Scout, Pathfinder, Trailhead, Landmark, Monument
**Data & Systems:** Topology, Geocode, Geofence, Metadata, Schema, Catalog, Registry, Archive, Gazetteer, Almanac, Atlas
**Orientation:** Zenith, Nadir, Horizon, Cardinal, Polaris, Magnetic, Anchor, Pivot, Focal, Apex

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
- **Build System:** CMake oder einfaches Makefile
- **Kein Framework, kein Runtime, keine Dependencies**

## Entwicklungssetup

- **Primär-OS:** Windows 11 (normales Dual-Boot-System)
- **Projekt-Partition:** Zweite Partition oder zweite SSD für TerrainOS
- **Test-Methode:** Entwicklung auf Haupt-Windows, Test per Dual-Boot
- **Späteres Ziel:** ISO bauen, per USB auf Laptop installieren
- **AI Workflow:** Claude Code (Hauptentwicklung), Codex + Gemini (Review/QA)

## Projektstruktur

```
terrain-os/
├── CLAUDE.md              ← Diese Datei
├── contour/               ← Shell/Desktop
│   ├── main.c             ← Entry Point, Shell-Registrierung
│   ├── desktop.c          ← Desktop-Rendering (Direct2D)
│   ├── panel.c            ← Taskbar/Panel (Uhr, Tray, Systray)
│   ├── launcher.c         ← Game Library Grid (Relief)
│   ├── filemanager.c      ← Minimaler Dateimanager (Traverse)
│   ├── settings.c         ← Audio, Netzwerk, Display (Calibration)
│   ├── wallpaper.c        ← Wallpaper-Rendering
│   ├── d2d_renderer.c     ← Direct2D Wrapper/Helpers
│   ├── win32_helpers.c    ← Win32 API Utilities
│   └── include/
│       ├── contour.h
│       ├── d2d_renderer.h
│       └── config.h
├── datum/                 ← Boot-Updater/Gatekeeper
│   ├── datum.c            ← Boot-Splashscreen + Update Logic
│   ├── updater.c          ← Manifest Check, Download, Install
│   ├── network.c          ← HTTP Client (WinHTTP)
│   └── dism_wrapper.c     ← DISM/pnputil Aufrufe
├── bearing/               ← Treiber Manager (optional)
│   └── bearing.c          ← GPU/Audio Treiber Updates
├── baseline/              ← Installer
│   ├── register_shell.bat ← Shell als Default registrieren
│   ├── unregister_shell.bat ← Zurück zu explorer.exe
│   └── safe_mode_fix.bat  ← Notfall-Recovery
├── survey/                ← ISO Builder
│   ├── strip.ps1          ← PowerShell Script: Windows 11 strippen
│   ├── inject_shell.ps1   ← Shell in ISO einbetten
│   ├── build_iso.ps1      ← Finale ISO bauen
│   └── packages.txt       ← Liste was entfernt/behalten wird
├── update-server/
│   ├── manifest.json      ← Aktuelle Versionen + Hashes
│   └── serve.py           ← Minimaler HTTP Server (Dev/Test)
└── legend/                ← Docs
    ├── architecture.md
    ├── boot-sequence.md
    └── update-protocol.md
```

## Milestones

### M1 — Proof of Concept (Woche 1-2)
- [ ] Ein C + Win32 Fenster das fullscreen startet
- [ ] Direct2D initialisieren, Rechteck + Text rendern
- [ ] Als Shell registrieren (Registry Key ändern)
- [ ] Testen: Reboot → eigenes Fenster statt Explorer
- [ ] Notfall: unregister_shell.bat im Safe Mode

### M2 — Minimale Shell (Woche 3-4)
- [ ] Panel/Taskbar unten: Uhr, Datum
- [ ] Desktop mit Wallpaper
- [ ] Rechtsklick-Kontextmenü (Shutdown, Restart, Explorer starten)
- [ ] Prozesse starten können (.exe ausführen)

### M3 — Game Launcher (Woche 5-6)
- [ ] Ordner scannen nach installierten Spielen
- [ ] Grid-Ansicht mit Game-Icons/Thumbnails
- [ ] Spiel per Klick starten
- [ ] Steam/GOG/Epic Bibliothek erkennen (Registrierungspfade lesen)

### M4 — System Controls (Woche 7-8)
- [ ] Lautstärkeregler (Core Audio API)
- [ ] WLAN-Auswahl (Native WiFi API)
- [ ] Batterie-Anzeige (falls Laptop)
- [ ] Bildschirmhelligkeit
- [ ] System-Tray mit laufenden Prozessen

### M5 — Dateimanager (Woche 9-10)
- [ ] Minimaler Dateibrowser
- [ ] Ordner navigieren, Dateien öffnen
- [ ] Kopieren, Verschieben, Löschen
- [ ] Laufwerke anzeigen

### M6 — Gatekeeper / Boot-Updater (Woche 11-12)
- [ ] Fullscreen Splashscreen beim Boot (Direct2D)
- [ ] Netzwerk-Check (Timeout 5 Sek → Shell direkt)
- [ ] Manifest von Server laden
- [ ] Versionen vergleichen, Updates downloaden
- [ ] Security Patches via DISM installieren
- [ ] GPU-Treiber via pnputil/silent install
- [ ] Shell-Binary swappen
- [ ] Progress Bar anzeigen
- [ ] Reboot falls Kernel-Patch, sonst Shell starten
- [ ] Fehlerfall: Shell trotzdem starten, nächster Boot retry

### M7 — ISO Builder (Woche 13-14)
- [ ] PowerShell Script: Windows 11 ISO mounten
- [ ] Bloat entfernen (Edge, Cortana, Copilot, Store, etc.)
- [ ] Shell + Gatekeeper injizieren
- [ ] Registry-Keys setzen (Shell = datum.exe)
- [ ] Finale ISO bauen mit oscdimg
- [ ] Test: USB-Stick erstellen, auf Laptop booten

### M8 — Polish (Ongoing)
- [ ] Animationen (Fade-In, Slide)
- [ ] Theming-System (Farben, Fonts konfigurierbar)
- [ ] Controller-Support (XInput)
- [ ] Virtual Desktops / Workspaces
- [ ] Benachrichtigungssystem
- [ ] Performance Overlay (FPS, CPU, GPU, RAM)

## Boot-Sequenz (Detail)

```
1. BIOS/UEFI POST
2. NT-Kernel lädt (ntoskrnl.exe)
3. Winlogon startet
4. Shell = "C:\TerrainOS\datum.exe"
5. Gatekeeper:
   a. Fullscreen Direct2D Fenster (Splash mit Logo)
   b. Netzwerk-Check (max 5 Sek warten)
   c. HTTPS GET manifest.json vom Update-Server
   d. Vergleich: lokale Versionen vs. Manifest
   e. Falls Updates:
      - Progress Bar anzeigen
      - Downloads parallel
      - SHA256 verifizieren
      - Patches: dism /online /add-package /packagepath:X.cab
      - Treiber: pnputil /add-driver X.inf /install
      - Shell: copy new contour.exe, swap beim nächsten Start
      - Falls Reboot nötig: automatisch rebooten
   f. Falls keine Updates oder Offline:
      - Shell starten: CreateProcess("C:\TerrainOS\datum.exe")
      - Gatekeeper beendet sich
6. Shell läuft → User sieht Desktop
```

## Update Manifest Format

```json
{
  "version": "2025.04.08",
  "channel": "stable",
  "packages": [
    {
      "name": "shell",
      "version": "0.3.0",
      "url": "https://update.terrainos.dev/packages/shell/shell-0.3.0.exe",
      "hash": "sha256:...",
      "size_mb": 0.2,
      "action": "swap_binary",
      "target": "C:\\TerrainOS\\contour.exe",
      "requires_reboot": false
    },
    {
      "name": "kb5034441-security",
      "version": "1.0",
      "url": "https://update.terrainos.dev/packages/kernel/kb5034441.cab",
      "hash": "sha256:...",
      "size_mb": 45,
      "action": "dism_install",
      "requires_reboot": true
    },
    {
      "name": "nvidia-driver",
      "version": "560.70",
      "url": "https://update.terrainos.dev/packages/drivers/nvidia-560.70-silent.exe",
      "hash": "sha256:...",
      "size_mb": 600,
      "action": "silent_install",
      "args": "-s -noreboot",
      "requires_reboot": true
    }
  ]
}
```

## Windows 11 Strip-Liste

### ENTFERNEN
- Microsoft Edge + WebView (ersetzen durch nichts, Spiele brauchen keinen Browser)
- Cortana, Copilot, Widgets
- OneDrive, Teams, Office Hub
- Store (Spiele werden direkt installiert)
- Feedback Hub, Get Started, Tips
- Maps, Weather, News, People, Skype
- Tablet Mode, Handschrifterkennung
- Drucker/Fax/Scanner Services
- Windows Defender (optional — spart RAM, aber Risiko)
- Accessibility Features (Narrator, Magnifier — optional)
- Telemetrie Services
- Windows Search / Indexer
- Themes, Wallpapers, Sounds, Lock Screen
- Snipping Tool, Paint, Notepad, WordPad (shell hat eigene Tools)

### BEHALTEN
- NT-Kernel (ntoskrnl.exe) + HAL
- Alle Hardware-Treiber
- Win32 Subsystem (win32k.sys)
- DirectX Runtime (d3d11.dll, d3d12.dll, dxgi.dll)
- DXGI, WDDM Treibermodell
- COM/DCOM Subsystem
- Registry
- WASAPI (Audio Stack)
- Netzwerk Stack (TCP/IP, WLAN, DHCP)
- VC++ Redistributables
- .NET Runtime (manche Game Launcher brauchen das)
- Windows Installer (MSI)
- WinSxS (für Security Updates — NICHT entfernen!)
- Crypto Services (für HTTPS)
- Task Scheduler (für Gatekeeper)
- PnP Manager (für Treiber-Updates)
- Windows Firewall (Security Baseline)

## Sicherheitshinweise

### Shell-Entwicklung
- IMMER `unregister_shell.bat` bereithalten
- IMMER Safe Mode als Fallback testen (F8 / Shift+Restart)
- IMMER auf der Test-Partition entwickeln, nie auf dem Haupt-Windows
- Die Shell MUSS graceful crashen — bei unhandled exception: explorer.exe als Fallback starten

### Recovery-Mechanismus in contour.exe
```c
// Top-level exception handler
SetUnhandledExceptionFilter(CrashHandler);

LONG CALLBACK CrashHandler(EXCEPTION_POINTERS* ep) {
    // Log crash
    // Starte explorer.exe als Fallback
    ShellExecute(NULL, "open", "explorer.exe", NULL, NULL, SW_SHOW);
    return EXCEPTION_EXECUTE_HANDLER;
}
```

### Im Gatekeeper
- Timeout für alles (Netzwerk, Downloads, Installs)
- Shell IMMER starten, auch wenn Updates fehlschlagen
- Crash-Counter: wenn Shell 3x hintereinander crasht → explorer.exe starten + Error loggen

## Design-Richtlinien

- **Dunkel.** Schwarzer/dunkelgrauer Hintergrund.
- **Minimal.** Keine überflüssigen Elemente.
- **Schnell.** Jede Aktion < 100ms Reaktionszeit.
- **Konsolenfeeling.** Denk PS5 Home Screen, nicht Windows Desktop.
- **Schrift:** Segoe UI (ist in Windows vorhanden) oder eigene einbetten.
- **Akzentfarbe:** Konfigurierbar, Default: Blau oder Grün.

## Nützliche Win32 API Referenzen

- Shell registrieren: `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Shell`
- Fullscreen: `SetWindowPos(hwnd, HWND_TOPMOST, 0, 0, screenW, screenH, ...)`
- Direct2D Init: `D2D1CreateFactory()` → `CreateHwndRenderTarget()`
- Prozess starten: `CreateProcess()` oder `ShellExecuteEx()`
- Audio: `IMMDeviceEnumerator` → `IAudioEndpointVolume`
- WLAN: `WlanOpenHandle()` → `WlanGetAvailableNetworkList()`
- Batterie: `GetSystemPowerStatus()`
- System Tray: Eigene Implementation (kein Explorer = kein Systray)
- DPI Awareness: `SetProcessDpiAwarenessContext(DPI_AWARENESS_CONTEXT_PER_MONITOR_AWARE_V2)`

## Kommandos für Claude Code

### Kompilieren (MSVC)
```bat
cl /O2 /W4 shell\main.c shell\desktop.c shell\panel.c shell\d2d_renderer.c /Fe:contour.exe /link d2d1.lib dwrite.lib user32.lib gdi32.lib shell32.lib ole32.lib
```

### Kompilieren (MinGW)
```bat
gcc -O2 -Wall contour/main.c contour/desktop.c contour/panel.c contour/d2d_renderer.c -o contour.exe -ld2d1 -ldwrite -luser32 -lgdi32 -lshell32 -lole32
```

### Shell registrieren (als Admin)
```bat
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Shell /t REG_SZ /d "C:\TerrainOS\datum.exe" /f
```

### Shell deregistrieren (Notfall)
```bat
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Shell /t REG_SZ /d "explorer.exe" /f
```

## Offene Entscheidungen

- [x] Name des Projekts → **TerrainOS** (Kartografie-Universum)
- [ ] Eigene Font einbetten oder Segoe UI nutzen?
- [ ] Defender behalten oder entfernen?
- [ ] Browser einbauen (minimal, WebView2) oder komplett weglassen?
- [ ] Controller-Navigation von Anfang an oder später?
- [ ] Open Source oder privat?
