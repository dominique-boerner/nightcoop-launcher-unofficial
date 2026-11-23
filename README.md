<p align="center">
  <img src="docs/logo.png" alt="" width="96">
</p>

<h1 align="center">NightCoop Launcher</h1>

<p align="center">
  <b>An unofficial launcher for the <a href="https://nightcoop.net">NightCoop</a> co-op mod for Cyberpunk 2077.</b><br>
  One button to install or update the mod, back up your saves and start the game.
</p>

<p align="center">
  <a href="../../releases/latest"><img alt="Latest release" src="https://img.shields.io/github/v/release/dominique-boerner/nightcoop-launcher-unofficial?color=fcee0a&labelColor=0a0a0b"></a>
  <a href="../../releases"><img alt="Downloads" src="https://img.shields.io/github/downloads/dominique-boerner/nightcoop-launcher-unofficial/total?color=fcee0a&labelColor=0a0a0b"></a>
  <a href="LICENSE"><img alt="License: MIT" src="https://img.shields.io/badge/license-MIT-fcee0a?labelColor=0a0a0b"></a>
  <img alt="Platform: Windows" src="https://img.shields.io/badge/platform-Windows-fcee0a?labelColor=0a0a0b">
  <a href="../../releases/latest"><img alt="VirusTotal: releases are scanned" src="https://img.shields.io/badge/VirusTotal-scanned-fcee0a?labelColor=0a0a0b"></a>
</p>

<p align="center">
  <a href="../../releases/latest"><b>Download</b></a> ·
  <a href="#what-it-does">Features</a> ·
  <a href="#build">Build</a> ·
  <a href="#add-a-language">Translate</a>
</p>

<p align="center">
  <img src="docs/screenshots/play.png" alt="The Play page: the mod is up to date and ready to play" width="900">
</p>

> [!IMPORTANT]
> This is a fan project. It is **not** made by, affiliated with or endorsed by the NightCoop team or CD PROJEKT RED.
> The real project lives at **<https://nightcoop.net>**.

## Unofficial, and with full respect

NightCoop is the work of its developers, and all credit for the mod belongs to them. I have nothing but respect
for what they have built. This launcher only downloads the builds they publish on their own site, exactly as
their [download page](https://nightcoop.net/download.html) offers them, and changes nothing about the mod.

If you enjoy the mod, please support its developers through the links on their site. If you are part of the
NightCoop team and would rather this launcher worked differently or did not exist, please open an issue.

## Why this exists

I play NightCoop with friends. Everyone in a session needs the same build, so every release meant each of us
downloading the archive, unpacking it into the game folder and hoping nobody was a version behind. I wanted that
to be one button, so all of us can simply play.

The launcher was developed with the help of [Claude](https://claude.com/claude-code), Anthropic's AI assistant.

## Get started

1. Download `NightCoopLauncher.exe` from the [latest release](../../releases/latest).
2. Run it. It is a single file and needs no installation and no .NET runtime.
3. Press **Install**. Once the mod is current, the same button becomes **Play**.

You need a licensed copy of Cyberpunk 2077 (Steam, GOG or Epic) in a version the current NightCoop build
supports. Windows may warn about an unknown publisher because the file is not code-signed.

Releases after v1.1.0 are uploaded to [VirusTotal](https://www.virustotal.com) when they are built. The link to
the report is in the release notes, next to the file's SHA-256, so you can check that the report belongs to the
file you downloaded.

## What it does

### Updates you can follow

The launcher asks nightcoop.net for the latest build, picks the archive made for your game version and installs
it in five visible steps. Every download is verified against the SHA-256 published by NightCoop before the game
folder is touched, and if an installation fails halfway the previous mod files are put back.

Release notes, optional automatic updates and a version history with rollback are on the same page.

<p align="center">
  <img src="docs/screenshots/updates.png" alt="The Updates page: a pending update with its five steps and the release notes" width="900">
</p>

### Backups of your saves

Saves are backed up before every update, and before every game start if you switch that on. Any backup can be
restored, exported or deleted. Restoring first backs up the saves being replaced, so it can be undone.

<p align="center">
  <img src="docs/screenshots/backups.png" alt="The Backups page: a list of save backups with restore, export and delete" width="900">
</p>

### And the rest

| | |
| --- | --- |
| **Finds your game** | Detects Cyberpunk 2077 in Steam, GOG and Epic; any other folder can be chosen by hand |
| **Checks the installation** | Compares the installed mod files with what was installed and repairs them on request |
| **Shows the protocol number** | Everyone in a session needs the same one, so it is on the first page |
| **Speaks two languages** | English and German, switchable in the settings; more can be [added](#add-a-language) |
| **Updates itself** | Offers new launcher versions from this repository's releases and replaces itself with one click, after verifying the download's checksum |

## Build

Requires the .NET 9 SDK on Windows.

```powershell
dotnet test
dotnet publish src\NightCoopLauncher.App -c Release -r win-x64 --self-contained true `
  -p:PublishSingleFile=true -p:IncludeNativeLibrariesForSelfExtract=true `
  -p:EnableCompressionInSingleFile=true -p:DebugType=none -o dist
```

| Project | Contents |
| --- | --- |
| `src/NightCoopLauncher.Core` | Release list, game detection, installation, file verification, save backups; no user interface |
| `src/NightCoopLauncher.App` | WPF interface (MVVM), language dictionaries, fonts |
| `tests/` | xUnit tests of the logic and of the flows behind the interface |

## Release

A release is created by pushing a version tag:

```powershell
git tag v1.0.0
git push origin v1.0.0
```

The `Release` workflow then runs the tests, builds `NightCoopLauncher.exe` with that version and publishes a
GitHub release with the file attached. A tag with a suffix, such as `v1.1.0-beta.1`, becomes a pre-release.

Before publishing, the workflow uploads the file to VirusTotal and adds the link to the report to the release
notes. This needs a [VirusTotal API key](https://docs.virustotal.com/reference/authentication) in the repository
secret `VT_API_KEY`. Without the key the release is published as before, just without that link.

## Add a language

Each language is one dictionary in `src/NightCoopLauncher.App/Languages/<code>.json`. Copy `en.json`, name it
after the language code (for example `fr.json`), set `language.name` to the name shown in the settings and
translate the values. After a build the language appears in the settings. Keys missing from a dictionary fall
back to English, and a test checks that all dictionaries carry the same keys.

## Where things are stored

| What | Where |
| --- | --- |
| Settings | `%LOCALAPPDATA%\NightCoopLauncher\settings.json` |
| Save backups | `%LOCALAPPDATA%\NightCoopLauncher\SaveBackups`, unless changed in the settings |
| Installed build | `NightCoop.updater.json` in the game folder |

Backups made before game starts are limited to the number set on the Backups page. Manual backups and backups
made before updates stay until you delete them.

Setting the environment variable `NIGHTCOOP_LAUNCHER_DATA` to a folder makes the launcher keep its settings and
backups there instead.

## Credits

- [NightCoop](https://nightcoop.net) by its developers.
- Fonts: Russo One and IBM Plex, both under the SIL Open Font License (see `src/NightCoopLauncher.App/Fonts`).

## License

The launcher's code is released under the [MIT License](LICENSE).

The license covers this launcher only. It does not cover the NightCoop mod, which is not part of this repository
and remains the work of its developers, nor the bundled fonts, which keep their own license. It grants no rights
to the names NightCoop or Cyberpunk 2077. Cyberpunk 2077 is a trademark of CD PROJEKT S.A.
