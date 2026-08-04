# RecentTab

A Total Commander WFX (File System) plugin that shows recently modified
files as a virtual panel - similar to macOS Finder's "Recents" view.

![RecentTab screenshot](https://raw.githubusercontent.com/Native2904/RecentTab/401f106849b2704410123273c37a39fb2bbb4be2/2026-08-03_153508.png)

## What it can do

Open `\\RecentTab\` in Total Commander and get a flat, chronological
list of every file you've actually touched recently, pulled together
from across your whole system - powered by a direct connection to
[Everything](https://www.voidtools.com) (voidtools), not a background
service or a slow manual scan. Every file in the list is the real thing
at its real location - open it, edit it, copy it, move it, delete it,
right from the list, nothing virtual or staged. Recording itself is a
simple on/off switch you control from the mouse, right in the panel,
with its own live runtime counter.

Close the tab, close Total Commander entirely, restart your machine -
doesn't matter. Your recording history lives in its own file next to
the plugin, not in memory, so the next time you open `\\RecentTab\`
everything is exactly where you left it - no re-recording, nothing
lost to an accidental Alt+F4.

Where it looks is entirely up to you. Out of the box it watches the six
usual suspects (Desktop, Documents, Downloads, Pictures, Videos,
Music), correctly following OneDrive redirects if you have them. Want
something narrower or completely different instead - just your project
drive, say? Define your own folder whitelist, and optionally blacklist
specific sub-paths inside any of them (a `node_modules` folder, a
backup cache, whatever clutters your results) - or keep the six
defaults *and* add your own on top, your choice. Cloud-sync placeholder
files that haven't actually been downloaded yet (OneDrive Files On-
Demand and similar) are filtered out automatically, so they don't
clutter the list with things that were never really "used" on this
machine.

Everything else is built to stay out of your way, and to tell you what
it's doing rather than leave you guessing. A dedicated `RealPath`
column always shows a file's true location even though the list itself
spans many folders. Sorting defaults to newest-first (or oldest-first,
your call). Alt+Enter opens a themed stats window - four built-in
color palettes, sizes itself to whatever it actually needs to show -
with a live clock ticking away right in its title bar, and a plain-
English readout of exactly what's currently loaded: which theme,
which language, whether Everything is actually connected right now
and which version it's running, how many queries have gone through
this session, your full recording history, and where every file this
plugin touches actually lives on disk. Nothing to dig for, nothing
hidden behind a settings dialog you have to hunt through - open the
window and it's all just there.

Under the hood, this plugin talks to Everything directly over its own
IPC protocol - no subprocess spawned for every single query, no
command line involved at all. It's noticeably faster than the older
approach this project used to take, and the Alt+Enter window tells you
in plain terms whether that connection is actually up. The whole
interface, by the way, is available in more than one language and easy
to translate further if you want to add your own.

## Known limitations

- Read-only by design: configuration happens by editing
  `RecentTab.ini` directly, no in-app settings UI.
- Only covers files actually modified or newly created - not files
  merely opened/viewed without changing them. Windows itself doesn't
  reliably record *which process* touched a file, only *that* it
  changed, so this is a hard platform limit rather than something a
  future update could add.

---

## Advanced configuration

Everything below has a working default - this section is for anyone
who wants to customize further, not required reading to get started.

```ini
[Settings]
ConfirmReset=1       ; 0 = reset immediately without the "Really reset?" prompt
SortDescending=1     ; 0 = oldest-first instead of newest-first
Language=eng         ; eng (default) / deu / matches a section in RecentTab_lang.ini
DebugLogging=1       ; 0 = stop writing RecentTab_debug.log (real per-call I/O cost while on)
IncludeDefaultFolders=0  ; 1 = add your [Watched:...] blocks to the six defaults instead of replacing them

[Theme]
Name=gruvbox         ; gruvbox (default) / dracula / monokai / everforest / custom
```

### Choosing which folders to watch (whitelist) and what to skip inside them (blacklist)

Add one or more `[Watched:Name]` blocks to define your own list -
once any are present, the six defaults are ignored entirely unless
`IncludeDefaultFolders=1` is set:

```ini
[Watched:Projects]
Path=D:\Projects
Exclude=Backup;node_modules

[Watched:Desktop]
Path=%USERPROFILE%\Desktop
```

`Exclude=` takes semicolon-separated sub-path fragments to skip within
that block's `Path=` - your per-folder blacklist. With
`IncludeDefaultFolders=1`, your custom blocks are added to the six
defaults instead of replacing them (redefine one of the six yourself,
e.g. with your own `Exclude=`, and your version is used instead of the
plain default - it won't appear twice).

### Color themes

Four built-in presets - `gruvbox` (default), `dracula`, `monokai`,
`everforest`. Or set `Name=custom` and supply your own:

```ini
[Theme]
Name=custom
Background=#282828
Foreground=#ebdbb2
Heading=#d3869b
Green=#b8bb26
Accent2=#8ec07c
Yellow=#fabd2f
Accent4=#fe8019
Muted=#928374
```

Any color left blank falls back to the matching gruvbox value, so a
partial custom theme never looks broken.

### Language

Interface text comes from `RecentTab_lang.ini` (same folder rule as
everything else), read with the plugin's own UTF-8-safe parser rather
than Windows' `GetPrivateProfileString` (which mangles non-Latin
scripts through the system's ANSI codepage unless the file has a
UTF-16LE BOM). Ships with `eng` and `deu`; any key left out or blank
falls back to English automatically.

`Color_ini_example.ini` shows a third language (`rus`, Cyrillic) added
to the file - a template to copy a `[xyz]` section from, not loaded
automatically.

### The Alt+Enter dialog

Opens a themed, read-only stats window from any of the three pseudo-
entries: recording status with a live-ticking clock in the title bar,
which theme and language are currently loaded, your full recording
history, lifetime usage (survives Reset), whether Everything's IPC
connection is currently up and which version it's running, watched
folders (flagged if one doesn't actually exist on disk), and config/
state file paths. Sizes itself to its actual content rather than a
fixed size - grows for long paths, scrolls for long lists.

## Requirements

- Windows 7 or newer, 32-bit or 64-bit Total Commander
- [Everything](https://www.voidtools.com) installed and running - this
  plugin connects to it directly over its own IPC protocol, no
  additional download or setup needed beyond having Everything itself
  running in the background

## Files in this package

- `RecentTab.wfx` / `RecentTab.wfx64` - the plugin (TC picks the right
  one automatically if both are present)
- `RecentTab.ini`, `RecentTab_lang.ini` - configuration and interface
  text; **must stay in the same folder as the plugin file**, since it
  looks them up relative to its own location, not TC's config folder
- `Color_ini_example.ini` - a template for adding another language
- `RecentTab_state.json` - created automatically on first use, not
  part of this package; remembers your recording history

## Installation

Double-click `RecentTab.zip` inside Total Commander - it recognizes
the installer inside and offers to install automatically, one click.
(Needs Configuration → Options → Packer → "Treat archives like
directories" enabled, which is the default. If the zip was already
opened to view its contents in the same panel, open/close a different
archive once first, then double-click again.)

Alternatively: extract it, or use the `release` folder from a source
build, and double-click `pluginst.inf` - or add the .wfx manually via
Configuration → Options → Plugins → File system plugins (WFX).

## Building from source

MinGW-w64 required (`build_debug.bat` expects `C:\mingw64`, with an
optional `mingw32` subfolder for a 32-bit build alongside the 64-bit
one). After a successful build, a `release` folder and a ready-to-ship
`RecentTab.zip` are assembled automatically next to the script. See
`TESTING.md` for what to check afterward.

## License

MIT - matching this author's other Total Commander plugins
([XYTags](https://github.com/Native2904), [DescriptEdit](https://github.com/Native2904)).

Author: Björn ([Native2904](https://github.com/Native2904))
