# LS3D Launcher (Flatpak)
[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

Unofficial Flatpak that runs the Windows version of **WBS LearnSpace 3D** on Linux using [Wine](https://www.winehq.org/) and [DXVK](https://github.com/doitsujin/dxvk). It is the successor of the distro scripts from [ls3dInstall](https://github.com/Calisto1994/ls3dInstall) and runs on any distribution with Flatpak, without sudo and without system packages.

> This project is not affiliated with WBS Training AG. LearnSpace 3D is proprietary software and is **not** included in the Flatpak. The official installer is downloaded from the WBS servers on first launch.

## Installation

From the Flatpak repository (requires the Flathub remote for the runtime and Wine):

```bash
flatpak install --user https://novusidea.github.io/ls3d-flatpak/de.novusidea.LS3DLauncher.flatpakref
```

Or as a bundle from the [Releases](https://github.com/novusidea/ls3d-flatpak/releases):

```bash
flatpak install --user LS3DLauncher.flatpak
```

## Usage

- On **first launch** the Wine prefix is created, DXVK is set up and the WBS installer is downloaded and run. If an NVIDIA GPU is present, the app asks about NVIDIA render offload.
- The **frame rate** is limited to 60 FPS. Set `FPS_LIMIT=0` in `~/.var/app/de.novusidea.LS3DLauncher/config/ls3d/settings` to remove the limit.
- **Starter update:** The 2023 starter from the WBS installer cannot update itself because it does not follow the redirect to the update mirror. The app therefore downloads the current starter version from the backend itself before launching.
- **ls3d:// links** in the browser open LearnSpace 3D directly with the right backend server.
- **Launching from the application menu:** The app asks for the backend server and remembers the last one used.
- Change **NVIDIA render offload** via the launcher's context menu (Settings) or with `flatpak run de.novusidea.LS3DLauncher --settings`.
- **Reset the installation:** `flatpak run de.novusidea.LS3DLauncher --reset`

## Uninstallation

```bash
flatpak uninstall --delete-data de.novusidea.LS3DLauncher
```

This removes the app, the Wine prefix and all settings (`~/.var/app/de.novusidea.LS3DLauncher`).

## Differences from ls3dInstall

| ls3dInstall | Flatpak |
|---|---|
| Wine, winetricks, zenity, wget installed via sudo | Wine BaseApp `org.winehq.Wine` and Freedesktop runtime, no system packages |
| `winetricks dxvk` downloads DXVK at runtime | DXVK is bundled with a checksum |
| optional 60 FPS limit via MangoHud | 60 FPS limit enabled by default, via DXVK (`dxgi.maxFrameRate`) |
| Prefix `~/.local/share/wineprefixes/ls3d` | `~/.var/app/de.novusidea.LS3DLauncher/data/wine` |
| custom uninstaller | `flatpak uninstall --delete-data` |

## Building

```bash
flatpak install --user flathub org.flatpak.Builder
flatpak run org.flatpak.Builder --user --install --install-deps-from=flathub \
    --force-clean build-dir de.novusidea.LS3DLauncher.yml
```

Validation:

```bash
flatpak run --command=flatpak-builder-lint org.flatpak.Builder \
    --exceptions --user-exceptions flatpak-builder-lint-exceptions.json manifest de.novusidea.LS3DLauncher.yml
appstreamcli validate data/de.novusidea.LS3DLauncher.metainfo.xml
desktop-file-validate data/de.novusidea.LS3DLauncher.desktop
```

## Publishing

A `v*` tag triggers the GitHub Action. It builds the bundle, attaches it to the release and publishes a GPG-signed Flatpak repository on GitHub Pages. This requires the following repository secrets:

- `GPG_PRIVATE_KEY` (ASCII-armored, e.g. `gpg --armor --export-secret-keys <ID>`)
- `GPG_PASSPHRASE`

In addition, *Settings → Pages* must have "GitHub Actions" set as the source.

Flathub only accepts Wine packages of Windows programs when they are submitted by the vendor. That is why this package is distributed via its own repository.
