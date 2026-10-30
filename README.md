# LS3D Launcher (Flatpak)
[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

Inoffizielles Flatpak, das die Windows-Version von **WBS LearnSpace 3D** unter Linux mit [Wine](https://www.winehq.org/) und [DXVK](https://github.com/doitsujin/dxvk) startet. Es ist der Nachfolger der Distro-Scripte aus [ls3dInstall](https://github.com/Calisto1994/ls3dInstall) und läuft auf jeder Distribution mit Flatpak, ohne sudo und ohne Systempakete.

> Dieses Projekt steht in keiner Verbindung zur WBS Training AG. LearnSpace 3D ist proprietäre Software und **nicht** im Flatpak enthalten. Der offizielle Installer wird beim ersten Start von den WBS-Servern geladen.

## Installation

Über das Flatpak-Repository (erfordert das Flathub-Remote für Runtime und Wine):

```bash
flatpak install --user https://novusidea.github.io/ls3d-flatpak/de.novusidea.LS3DLauncher.flatpakref
```

Oder als Bundle aus den [Releases](https://github.com/novusidea/ls3d-flatpak/releases):

```bash
flatpak install --user LS3DLauncher.flatpak
```

## Benutzung

- Beim **ersten Start** wird das Wine-Prefix angelegt, DXVK eingerichtet und der WBS-Installer heruntergeladen und ausgeführt. Wenn eine NVIDIA-GPU vorhanden ist, fragt die App nach NVIDIA Render Offload.
- Die **Bildrate** ist auf 60 FPS begrenzt. Mit `FPS_LIMIT=0` in `~/.var/app/de.novusidea.LS3DLauncher/config/ls3d/settings` lässt sich die Begrenzung aufheben.
- **Starter-Update:** Der Starter von 2023 aus dem WBS-Installer kann sich nicht selbst aktualisieren, weil er der Umleitung auf den Update-Mirror nicht folgt. Deshalb lädt die App vor dem Start die aktuelle Starter-Version selbst vom Backend.
- **ls3d://-Links** im Browser öffnen LearnSpace 3D direkt mit dem richtigen Backend-Server.
- **Start aus dem Anwendungsmenü:** Die App fragt nach dem Backend-Server und merkt sich den zuletzt verwendeten.
- **NVIDIA Render Offload** ändern: über das Kontextmenü des Starters (Einstellungen) oder mit `flatpak run de.novusidea.LS3DLauncher --settings`.
- **Installation zurücksetzen:** `flatpak run de.novusidea.LS3DLauncher --reset`

## Deinstallation

```bash
flatpak uninstall --delete-data de.novusidea.LS3DLauncher
```

Damit werden die App, das Wine-Prefix und alle Einstellungen entfernt (`~/.var/app/de.novusidea.LS3DLauncher`).

## Unterschiede zu ls3dInstall

| ls3dInstall | Flatpak |
|---|---|
| Wine, winetricks, zenity, wget per sudo installiert | Wine-BaseApp `org.winehq.Wine` und Freedesktop-Runtime, keine Systempakete |
| `winetricks dxvk` lädt DXVK zur Laufzeit | DXVK ist mit Prüfsumme im Paket enthalten |
| optionales 60-FPS-Limit über MangoHud | 60-FPS-Limit standardmäßig aktiv, über DXVK (`dxgi.maxFrameRate`) |
| Prefix `~/.local/share/wineprefixes/ls3d` | `~/.var/app/de.novusidea.LS3DLauncher/data/wine` |
| eigener Uninstaller | `flatpak uninstall --delete-data` |

## Selbst bauen

```bash
flatpak install --user flathub org.flatpak.Builder
flatpak run org.flatpak.Builder --user --install --install-deps-from=flathub \
    --force-clean build-dir de.novusidea.LS3DLauncher.yml
```

Prüfen:

```bash
flatpak run --command=flatpak-builder-lint org.flatpak.Builder manifest de.novusidea.LS3DLauncher.yml
appstreamcli validate data/de.novusidea.LS3DLauncher.metainfo.xml
desktop-file-validate data/de.novusidea.LS3DLauncher.desktop
```

## Veröffentlichung

Ein Tag `v*` startet die GitHub-Action. Sie baut das Bundle, hängt es an das Release an und veröffentlicht ein GPG-signiertes Flatpak-Repository auf GitHub Pages. Dafür werden diese Repository-Secrets gebraucht:

- `GPG_PRIVATE_KEY` (ASCII-armored, z. B. `gpg --armor --export-secret-keys <ID>`)
- `GPG_PASSPHRASE`

Außerdem muss unter *Settings → Pages* als Quelle „GitHub Actions“ eingestellt sein.

Flathub nimmt Wine-Pakete von Windows-Programmen nur an, wenn der Hersteller sie selbst einreicht. Deshalb wird dieses Paket über ein eigenes Repository verteilt.
