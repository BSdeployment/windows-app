<div align="center">

# AppDeck

**A modern, lightweight application launcher for Windows.**
All your favorite apps, organized in one beautiful window — one shortcut away.

![.NET](https://img.shields.io/badge/.NET-10-512BD4)
![Platform](https://img.shields.io/badge/platform-Windows%2010%20%7C%2011-0078D4)
![License](https://img.shields.io/badge/license-MIT-green)

</div>

---

## Overview

Instead of digging through the Start Menu, desktop shortcuts or the taskbar, open AppDeck and see
everything you use every day, grouped into categories you control. Press a global shortcut from anywhere in
Windows, type a few letters, hit **Enter**, and you're in.

AppDeck is built with **C#, .NET, WPF and MVVM**, with a Fluent-inspired design, dark/light themes and a
focus on speed and low resource usage.

> Add a screenshot here: `docs/screenshot.png`

## Features

**Launcher**
- Tile grid organized by categories (collapsible, reorderable, renameable)
- Three tile sizes and two UI densities
- Favorites and recently launched sections
- Live search by name or category — just start typing anywhere in the window
- Full keyboard navigation, focus indicators and tooltips

**Application management**
- Add executables, shortcuts (`.lnk`, `.url`), URI apps (e.g. `ms-settings:`) and Microsoft Store apps (`shell:AppsFolder\…`)
- Custom display name, arguments, working directory and custom icon
- Run as administrator from the context menu
- Drag files into the window to add them; drag tiles to reorder or move them between categories
- Removing an app **only removes the AppDeck entry** — nothing is ever uninstalled or deleted, and Undo is offered
- Missing targets are flagged with a warning badge so you can fix or remove them

**Windows integration**
- System tray icon (Open / Settings / Exit)
- Configurable global keyboard shortcut (default **Ctrl + Alt + Space**)
- Start with Windows, minimize/close to tray, optional compact launcher mode
- Single instance: launching AppDeck again simply shows the running one
- Remembers window size and position

**Reliability**
- Settings and apps stored as JSON in `%LocalAppData%\AppDeck\data.json`
- Safe writes with automatic backup; corrupted files are set aside and the app starts cleanly
- Import / export with validation (apps from another PC are kept and flagged if their paths don't exist)
- Icons extracted off the UI thread and cached on disk

## Getting started

### Requirements
- Windows 10 (1809+) or Windows 11
- [.NET 10 SDK](https://dotnet.microsoft.com/download) (to build from source)

### Build and run

```powershell
git clone https://github.com/YOUR-USERNAME/AppDeck.git
cd AppDeck
dotnet restore
dotnet run -c Release
```

### Publish a standalone build

```powershell
dotnet publish -c Release -r win-x64 --self-contained true `
  -p:PublishSingleFile=true -p:IncludeNativeLibrariesForSelfExtract=true
```

The output is in `bin/Release/net10.0-windows/win-x64/publish/`. Test the result on a clean machine before
distributing it.

### Regenerating the app icon

The logo is drawn in code (`Services/AppIconFactory.cs`). To regenerate the `.ico` used by the executable:

```powershell
dotnet run -- --export-icon AppDeck.ico
```

## Usage

| Action | How |
|---|---|
| Open AppDeck from anywhere | `Ctrl + Alt + Space` (configurable) |
| Search | Start typing, then `Enter` to launch the first result |
| Move between tiles | Arrow keys; `Enter` launches |
| Add an application | `Ctrl + N`, the **+** button, or drag a file into the window |
| Open settings | `Ctrl + ,` or the gear button |
| Focus search | `Ctrl + F` |
| Clear search / hide window | `Esc` |
| Remove selected tile | `Delete` (with Undo) |
| Tile menu | Right-click or the Menu key |

Your data lives in `%LocalAppData%\AppDeck\`:

```
data.json        apps, categories and settings
data.json.bak    automatic backup of the previous save
IconCache\       cached icons
error.log        written only if something goes wrong
```

## Project structure

```
AppDeck/
├── Models/            Plain data classes (apps, categories, settings)
├── ViewModels/        MVVM view models (CommunityToolkit.Mvvm)
├── Views/             XAML windows and the dialog service
├── Services/          Storage, launching, icons, hotkey, tray, theme, startup
├── Infrastructure/    Native interop and value converters
├── Resources/
│   ├── Styles/        Shared control styles (buttons, inputs, toggles, menus…)
│   └── Themes/        Dark and Light color dictionaries
├── App.xaml(.cs)      Startup, single instance and shell integration
└── app.manifest       Per-monitor DPI awareness
```

## Architecture notes

- **MVVM** with `CommunityToolkit.Mvvm`; code-behind is limited to view-only concerns such as drag and drop and window placement.
- **Theming**: all colors live in `Themes/Dark.xaml` and `Themes/Light.xaml` and are consumed through `DynamicResource`, so the theme switches live without restarting.
- **Persistence**: `System.Text.Json`, atomic writes, debounced saving.
- **Icons**: `PrivateExtractIcons` for high-resolution extraction with a PNG cache and a fallback glyph.
- **Global shortcut**: `RegisterHotKey` on a message-only window, so it works while the main window is hidden.

## Roadmap

- [x] Core launcher, categories, search, add / edit / remove
- [x] Tray, global shortcut, start with Windows, compact mode
- [x] Favorites, recents, drag and drop, themes, import / export
- [ ] Installer (Inno Setup or MSIX)
- [ ] Screenshots and GitHub Releases
- [ ] Automated tests for storage and view models
- [ ] Localization (including RTL languages)

## Contributing

Contributions are very welcome — bug reports, feature ideas, documentation and pull requests.

1. Fork the repository and create a branch: `git checkout -b feature/my-change`
2. Make your changes, keeping to the existing MVVM structure and code style (nullable reference types enabled, no new dependencies unless necessary)
3. Make sure the project builds and runs: `dotnet build -c Release`
4. Commit with a clear message and open a pull request describing what changed and why

For larger changes, please open an issue first so we can discuss the approach.
When reporting a bug, include your Windows version and the contents of `%LocalAppData%\AppDeck\error.log` if present.

## License

AppDeck is released under the [MIT License](LICENSE).

## Acknowledgments

- [.NET](https://dotnet.microsoft.com/) and WPF
- [CommunityToolkit.Mvvm](https://github.com/CommunityToolkit/dotnet)
