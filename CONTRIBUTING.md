# Contributing to TerrainOS

Thanks for your interest in contributing. TerrainOS is a community-driven project and we welcome contributions of all kinds — code, documentation, testing, and bug reports.

---

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Branch Strategy](#branch-strategy)
- [Getting Started](#getting-started)
- [Development Safety](#development-safety)
- [Submitting Changes](#submitting-changes)
- [Coding Standards](#coding-standards)
- [Component Ownership](#component-ownership)

---

## Code of Conduct

By contributing you agree to follow our [Code of Conduct](CODE_OF_CONDUCT.md). Be respectful, constructive, and direct.

---

## Branch Strategy

We use **Git Flow**:

| Branch | Purpose |
|--------|---------|
| `main` | Stable releases only. Direct pushes are blocked. |
| `develop` | Integration branch. All features merge here first. |
| `feature/<name>` | New features — branch off `develop` |
| `fix/<name>` | Bug fixes — branch off `develop` |
| `hotfix/<name>` | Critical fixes — branch off `main`, merge into both `main` and `develop` |
| `release/<version>` | Release preparation — branch off `develop` |

**Always branch from `develop`, never from `main`.**

```bash
git checkout develop
git pull origin develop
git checkout -b feature/contour-wallpaper
```

---

## Getting Started

### Prerequisites

- Windows 10 or 11
- MSVC (Visual Studio Build Tools 2022) **or** MinGW-w64
- CMake 3.20+
- Git

### Clone

```bash
git clone git@github.com:andreasfilimon/TerrainOS.git
cd TerrainOS
git checkout develop
```

### Build

```bat
# MSVC
cl /O2 /W4 contour/main.c contour/d2d_renderer.c /Fe:contour.exe ^
   /link d2d1.lib dwrite.lib user32.lib gdi32.lib shell32.lib ole32.lib

# MinGW
gcc -O2 -Wall contour/main.c contour/d2d_renderer.c -o contour.exe ^
    -ld2d1 -ldwrite -luser32 -lgdi32 -lshell32 -lole32
```

---

## Development Safety

> **This project replaces the Windows shell. Improper testing can leave your system in an unbootable state.**

### Rules

1. **Never develop or test on your primary Windows installation.**
2. Use a **second partition, second SSD, or a VM** for testing.
3. Always have `baseline/unregister_shell.bat` accessible from a USB stick or Safe Mode.
4. Test Safe Mode recovery (`F8` / `Shift+Restart`) before registering the shell.
5. The shell **must** implement a crash handler that falls back to `explorer.exe`.

### Emergency Recovery

```bat
# From Safe Mode or recovery environment:
baseline\unregister_shell.bat

# Manual fallback:
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Shell /t REG_SZ /d "explorer.exe" /f
```

---

## Submitting Changes

### Issues First

For non-trivial changes, open an issue before starting work. This avoids duplicate effort and ensures the change aligns with the project direction.

### Pull Request Process

1. Fork the repository
2. Branch off `develop`: `git checkout -b feature/your-feature`
3. Make your changes
4. Test on a non-primary Windows installation
5. Commit with clear messages (see below)
6. Push and open a PR targeting `develop`
7. Fill out the PR template completely
8. Wait for review — at least one approval required

### Commit Messages

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
feat(contour): add wallpaper rendering via Direct2D
fix(datum): handle network timeout on boot
docs(legend): add boot sequence diagram
refactor(d2d_renderer): extract helper for brush creation
```

**Types:** `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `perf`

---

## Coding Standards

- **Language:** C17 (`/std:c17` MSVC, `-std=c17` GCC)
- **Style:** K&R indentation, 4 spaces (no tabs)
- **Naming:** `snake_case` for functions and variables, `SCREAMING_SNAKE_CASE` for macros
- **Headers:** include guards with `TERRAIN_<MODULE>_H`
- **Error handling:** always check return values from Win32 API calls
- **No dynamic allocation** in hot paths — prefer stack allocation
- **No CRT dependencies** where avoidable — use Win32 equivalents

### Example

```c
HRESULT d2d_renderer_init(D2DRenderer* renderer, HWND hwnd) {
    HRESULT hr;
    D2D1_SIZE_U size = { 0 };
    RECT rc;

    if (!GetClientRect(hwnd, &rc)) {
        return E_FAIL;
    }

    size.width  = rc.right  - rc.left;
    size.height = rc.bottom - rc.top;

    hr = ID2D1Factory_CreateHwndRenderTarget(
        renderer->factory,
        &d2d_render_props,
        &(D2D1_HWND_RENDER_TARGET_PROPERTIES){ .hwnd = hwnd, .pixelSize = size },
        &renderer->render_target
    );

    return hr;
}
```

---

## Component Ownership

| Component | Folder | Lead |
|-----------|--------|------|
| Shell/Desktop | `contour/` | @andreasfilimon |
| Boot-Updater | `datum/` | open |
| Driver Manager | `bearing/` | open |
| Installer | `baseline/` | open |
| ISO Builder | `survey/` | open |
| Documentation | `legend/` | open |

---

## Questions?

Open a [Discussion](https://github.com/andreasfilimon/TerrainOS/discussions) — not an issue.
