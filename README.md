# RecentTab

A Total Commander WFX (File System) plugin that shows recently modified
files as a virtual panel - similar to macOS Finder's "Recents" view.

<img width="1918" height="871" alt="2026-08-19_194513" src="https://github.com/user-attachments/assets/a3b07277-50fc-4f7b-a5dd-58d3426f92c4" />


[RecentTab Total Commander Forum](https://www.ghisler.ch/board/viewtopic.php?p=487769#p487769)
## What it can do

Open `\\RecentTab\` in Total Commander and get a flat, chronological
list of every file you've actually touched recently, pulled together
from across your whole system - powered by a direct connection to
[Everything](https://www.voidtools.com) (voidtools), not a background
service or a slow manual scan. Every file in the list is the real thing
at its real location - open it, edit it, copy it, move it, delete it,
right from the list, nothing virtual or staged. Recording itself is a
simple on/off switch you control from the mouse, right in the panel,
with its own live runtime counter. The panel itself stays down to two
entries at the top - `! REC` for that switch, and `! menu` next to it
for everything else (reset, refresh, and the search below) - so the
list of actual files never has to compete with a row of buttons for
space.

At a glance:

- **Recording** - simple on/off switch, survives restarts, its own
  runtime counter
- **Search** - jump to any past time range, not just "recent"
- **Six color themes** - light/dark, or automatic by time of day
- **Auto-Refresh** - the panel updates itself on a schedule, no manual
  `Strg+R` needed
- **Lost files** - notices when something you were tracking
  disappears, and can search for where it went
- **Extra columns** - change type, relative time, source folder,
  session number, already-opened, locked status
- **Brightness sliders** - fine-tune any theme's contrast without
  picking new colors
- **Bundled fonts** - JetBrains Mono and Fira Code ship with it, no
  separate install
- **Custom icons** - every panel entry's icon is swappable
- **Sort order reset** - one click undoes an accidental column-click
  sort

This list grows as the plugin does - see Advanced configuration below
for how each of these actually works, and Settings reference /
Columns reference near the end for the exact ini keys.

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
- A file changed in the last second or two, while recording is still
  actively running, can occasionally take one extra refresh to show up
  - Everything's own live index needs a brief moment to catch up.
  Pausing (or waiting a little longer) always resolves it.

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
RootButtons=          ; bring Reset;Refresh;Search;AutoRefresh to the root, instead of just inside "! menu"
UseSearch=1            ; 0 = hide the search entirely, no way to reach it
ShowLiveClock=1        ; 0 = stop the Alt+Enter title bar clock from ticking
NoColors=0             ; 1 = plain system colors everywhere instead of any theme
FontBrightness=0       ; -3 to +3, fine-tune the loaded theme's text brightness
BackgroundBrightness=0 ; -3 to +3, fine-tune the loaded theme's background brightness
AutoRefresh=0          ; 1 = re-read the panel automatically instead of needing Strg+R
AutoRefreshIntervalSec=600  ; only relevant if AutoRefresh=1 - minimum enforced is 3s
;AutoRefreshMaxIdleMin=0     ; 0 = disabled; otherwise skip auto-refresh once the system's been unused this long
;QuietHoursStart=22:00       ; no auto-refresh between these two times (e.g. overnight)
;QuietHoursEnd=07:00
;AutoRefreshSkipOnBattery=0  ; 1 = skip auto-refresh while running on battery power
MonoFont=fonts\JetBrainsMono-Regular.ttf   ; the search window's field font - see "Bundled fonts" below
MonoFontName=JetBrains Mono

[Theme]
Name=basic            ; basic (default) / gruvbox / everforest / solarized / custom
Mode=dark             ; dark (default) / light
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

### Filtering by file type

`OnlyExtensions=` is global (applies across all watched folders at
once):

```ini
[Settings]
;OnlyExtensions=docx;pdf;jpg
```

Accepts `exe`, `*.exe`, or `.exe` for each entry - however you
naturally type it. If set at all, it takes absolute priority - only
files matching one of those show up, overriding every
`ExcludeExtensions=` (global or per-folder) entirely while it's active.

`ExcludeExtensions=` itself lives per watched folder instead - a
single global exclude list couldn't tell two differently-purposed
folders apart:

```ini
[Watched:Projects]
Path=D:\Projects
ExcludeExtensions=exe;dll
```

### Color themes

Four built-in preset families - `basic` (default), `gruvbox`,
`everforest`, `solarized` - each available in both a dark and light
variant via `Mode=`:

```ini
[Theme]
Name=basic
Mode=dark             ; dark (default) / light
```

(Dracula and Monokai aren't offered as presets here - neither has an
official light counterpart to pair with the other three; use `custom`
below for a Dracula/Monokai-like look instead.) Or set `Name=custom`
and supply your own:

```ini
[Theme]
Name=custom
Background=#2b2a27
Foreground=#ede0ce
Heading=#d68f41
Green=#39b81f
Accent2=#00a8c6
Yellow=#ebb626
Accent4=#d63131
Muted=#7a7267
```

Any color left blank falls back to the matching basic value, so a
partial custom theme never looks broken.

Instead of a fixed `Mode=`, light/dark can also follow the clock:

```ini
[Theme]
TimeBasedMode=0        ; 1 = ignore Mode= above, decide by hour instead
LightStartHour=6
DarkStartHour=18
```

The defaults mean light from 6:00 to 17:59, dark the rest of the time.
Checked fresh every time a window opens, so it switches live - no
restart needed.

`NoColors=1` in `[Settings]` overrides every theme above entirely -
plain system colors instead, everywhere (Alt+Enter dialog, search
window, and Menu.exe once it exists). Every role that carries meaning
through color also says so in its own text (warnings spell out
"WARNING:", for instance), so nothing becomes unreadable with colors
off - just less visually distinct at a glance.

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

### Extra columns

Six optional columns, each off by default - turn on only the ones you
actually want, then add them to the panel via Total Commander's own
Shift+F1 "Configure custom columns":

```ini
[Settings]
;ShowChangeType=0
;ShowRelativeTime=0
;ShowSourceFolder=0
;ShowSession=0
;ShowOpened=0
;ShowLocked=0
```

![SourceFolder, RelativeTime and Session columns in the panel](screenshots/extra-columns.png)

**`ShowChangeType`** - the plugin already decides internally whether a
file's *modification* or its *creation* is what actually justified
showing it (see the note on `dm:`/`dc:` above - handles the archive
case where an old file gets extracted with its original modified date
intact, but is brand new *here*). This column just makes that existing
decision visible ("Changed" / "New") instead of keeping it purely
internal.

**`ShowRelativeTime`** - a plain date takes a moment to parse at a
glance. "5 minutes ago" doesn't.

**`ShowSourceFolder`** - only useful once you've got more than one
`[Watched:...]` block. Without it, figuring out which rule caught a
given file means reading the full path yourself.

**`ShowSession`** - which recording session (pause/resume cycle) a
file came from - handy for telling "everything from this morning"
apart from "everything from just now" without doing the math on
timestamps yourself.

**`ShowOpened`** - marks files you've already opened through this
panel before with an "x" - handy when scanning several matches for
the one you haven't looked at yet. Important limit: this only knows
about files opened *through RecentTab specifically* - it can't tell
you whether a file was actually edited afterward in whatever program
opened it, since that happens completely outside the plugin's view
(and Windows itself doesn't reliably track this either). Controlled
separately via `OpenedTracking=session` (forgotten on TC restart,
the default) or `permanent` (remembered across restarts, in its own
small `RecentTab_opened.txt` file).

**`ShowLocked`** - "Access", "Locked", or "Not found" - is another
program currently holding the file open? Checked on a background
thread, so the panel itself never waits on it - the column just stays
blank for a moment until the check finishes, usually well before the
next repaint. The check genuinely opens each listed file to test it,
which touches the file's last-access time and can wake antivirus or
cloud sync - worth knowing before turning this on. Local drives are
always checked; network drives, removable media, and optical drives
are off by default (each an individual round-trip or a drive that may
need to spin up first) and re-enabled individually via
`AllowDriveNetwork=`, `AllowDriveRemovable=`, `AllowDriveCDRom=`.
Cloud placeholder files (OneDrive Files On-Demand and similar) are
skipped outright - never touched, so browsing never triggers a
download.

### Custom icons

All ten panel entries ship with their own matching icon as an active
default already (see `icons\` and the `[Icons]` section of
`RecentTab_example.ini`) - the same orange-ring, blue-symbol family
throughout, each shape reflecting what its entry does. Every one is a
plain override in `[Icons]`, pointing at your own `.ico`/`.exe`/`.dll`
instead if you'd rather - a relative path like `icons\age.ico`
resolves against this ini's own folder:

```ini
[Icons]
AgeIcon=icons\age.ico
```

See Settings reference → Paths for the complete list of all ten keys.

### Search - find something in your own history, fast

The normal list shows everything recorded, but scrolling back far
enough gets old. **Search** is a quick, targeted search **within your
own recording history** - not Everything's whole index, just what
you've actually recorded, in your configured folders. No switching
windows, no detour through another app - results land right back in
`\\RecentTab\`, same as the normal list.

![The search window](screenshots/search-window.png)

Find it under `! menu` → `! Search`. A small window opens with
two fields, **FROM** and **TO** - the input is more forgiving than it
looks:

- A **colon** makes your input a time: `17:00`
- A **period, dash, or slash** makes it a date: `04.08.2026` (day
  first - the 4th of August, not April)
- Both together (space-separated) give an exact point in time:
  `04.08.2026 17:00`
- No date given? Assumed to be **today**.
- No time given? FROM defaults to the start of the day, TO to the end
  of it - a date-only TO covers the whole day.
- Leave TO empty for "**until now**".

`! Back to recent files` takes you back to the normal view.
`UseSearch=0` in `[Settings]` hides the search entirely, if you'd
rather not have it available at all.

### The Alt+Enter dialog

Opens a themed, read-only stats window from `! REC`, `! menu`, or any
of the entries inside it: recording status with a live-ticking clock
in the title bar (`ShowLiveClock=0` to turn that off), which theme and
language are currently loaded, your full recording history, lifetime
usage (survives Reset), whether Everything's IPC connection is
currently up and which version it's running, watched folders (flagged
if one doesn't actually exist on disk), any `[Icons]` override that
points at a file that couldn't actually be loaded, and config/state
file paths. Sizes itself to its actual content rather than a fixed
size - grows for long paths, scrolls for long lists.

### Auto-Refresh - the panel updates itself, if you want it to

Off by default. Turn it on and the panel re-reads itself on a timer,
without needing `Strg+R`:

```ini
[Settings]
AutoRefresh=0
AutoRefreshIntervalSec=600
```

Also toggleable at runtime from `! menu` → `! Auto-Refresh: ...` -
pressing Enter on it cycles Off → 1 → 5 → 10 → 30 minutes → Off, no
ini edit or restart needed. That menu toggle only affects the current
session; the ini value is just what it starts at when TC loads the
plugin.

A few things it deliberately checks before firing, so it doesn't do
anything surprising in the background:

- Only fires while the active panel is actually showing RecentTab -
  never re-reads a panel you're not looking at
- `AutoRefreshMaxIdleMin=` - skips it once the system's been unused
  this long (mouse/keyboard idle, not just TC idle)
- `QuietHoursStart=`/`QuietHoursEnd=` - a time window with no
  auto-refresh at all, e.g. overnight
- `AutoRefreshSkipOnBattery=1` - skips it while running on battery

The Alt+Enter dialog shows what actually happened on the last tick -
not just "it ran," but whether the data query itself succeeded, found
changes, found nothing new, or failed outright. The short version: a plain tick counter would show that something was *attempted*, not whether it actually worked - so it deliberately doesn't use one.

### Brightness sliders - nudge a theme without picking new colors

```ini
[Settings]
FontBrightness=0        ; -3 to +3
BackgroundBrightness=0  ; -3 to +3
```

Seven fixed steps each, not a free number - `FontBrightness` shifts
every text color, `BackgroundBrightness` shifts only the background
(a narrower range, since it's the largest surface and more sensitive
to contrast loss). If a particular combination of the two would leave
too little contrast between text and background, both are silently
ignored and a warning appears in the Alt+Enter dialog instead of a
barely-readable result. Fixed, pre-checked steps beat a free number here for the same reason the theme presets themselves are curated rather than arbitrary.

### Bundled fonts

The search window's FROM/TO fields use a monospace font, loaded
privately for this process only - no system-wide install, no admin
rights needed, looks the same regardless of what's installed on the
machine it runs on. Two fonts ship in the `fonts\` folder (both SIL
Open Font License, free to redistribute): JetBrains Mono (the
default) and Fira Code.

```ini
[Settings]
MonoFont=fonts\JetBrainsMono-Regular.ttf
MonoFontName=JetBrains Mono
;MonoFont=fonts\FiraCode-Regular.ttf
;MonoFontName=Fira Code
```

Any other compatible monospace `.ttf` works too - both lines need to
match the file you're pointing at: `MonoFont=` is the file path,
`MonoFontName=` is the font's own internal family name, which isn't
always the same as the file name. A wrong `MonoFontName=` doesn't
error or crash - Windows just silently substitutes another font, and
you only notice from the look. To find the right name for a font
file you're not sure about: right-click it in Explorer → Preview (or
just double-click it) - the preview window's title shows the real
family name.

To use Cascadia Code instead (already installed with Windows, nothing
to download): leave `MonoFont=` blank and set
`MonoFontName=Cascadia Code`.

### Lost files - what disappeared, and when

Off by default. Turn it on and RecentTab starts noticing when
something it was tracking disappears - deleted, moved outside a
watched folder, or renamed all look identical from here, so this never
claims to know which happened, just that something did:

```ini
[Settings]
TrackLostFiles=0
LostFilesTracking=session
```

Shows up as `! Lost` inside `! menu`, with the count visible in TC's
own Size column. Each entry remembers whatever was last known about it
- name, size, dates, which watched folder, which recording session -
and opening one shows a small info window with all of it, plus a
"Search again" button that asks Everything for the exact filename
system-wide, no folder restriction, in case it just moved rather than
vanished. This is deliberately not a live/instant detector - it only
knows something's missing between one refresh and the next, never the
exact moment. Cleared together with the recording history on Reset,
not separately. See `notes/lost-files/` for the fuller reasoning,
including why a couple of more ambitious approaches (a live background
watcher, reading the Recycle Bin) were considered and set aside.

## Settings reference

Every setting that exists, in one place - grouped the same way as
`RecentTab_example.ini` itself (function switches, colors, paths), so
this list and that file always match. The narrative sections above
explain the *why*; this is the *what*, kept intentionally terse. New
settings get one line added here, nothing else rewritten.

**Function switches**

| Setting | What it does |
|---|---|
| `ConfirmReset` | Ask "really reset?" before clearing history |
| `SortDescending` | Newest-first (1) or oldest-first (0) |
| `DebugLogging` | Write `RecentTab_debug.log` - off before sharing a build |
| `Language` | UI language, matches a section in `RecentTab_lang.ini` |
| `IncludeDefaultFolders` | Add your own `[Watched:...]` blocks to the six defaults instead of replacing them |
| `RootButtons` | Which utility entries sit at the root instead of inside `! menu` |
| `UseSearch` | 0 hides `! Search` entirely |
| `ShowLiveClock` | Ticking clock in the Alt+Enter title bar |
| `OnlyExtensions` | Global allowlist of extensions - overrides every exclude while set |
| `ShowChangeType` / `ShowRelativeTime` / `ShowSourceFolder` / `ShowSession` / `ShowOpened` / `ShowLocked` | The six extra columns - see Columns reference below |
| `OpenedTracking` | `session` or `permanent` memory for `ShowOpened` |
| `AllowDriveNetwork` / `AllowDriveRemovable` / `AllowDriveCDRom` | Let `ShowLocked` probe these drive types too |
| `NoColors` | Plain system colors everywhere, no theme |
| `AutoRefresh` | Panel re-reads itself automatically |
| `AutoRefreshIntervalSec` | How often - minimum enforced 3s |
| `AutoRefreshMaxIdleMin` | Skip auto-refresh once the system's been idle this long |
| `QuietHoursStart` / `QuietHoursEnd` | No auto-refresh between these two times (e.g. overnight) |
| `AutoRefreshSkipOnBattery` | Skip auto-refresh while running on battery power |
| `FontBrightness` / `BackgroundBrightness` | Fine-tune the loaded theme, -3 to +3 |
| `MonoFont` / `MonoFontName` | Which bundled (or your own) font the search window uses |
| `TrackLostFiles` | Notice when a tracked file disappears |
| `LostFilesTracking` | `session` or `permanent` memory for the Lost list |
| `LostSearchStrict` | Require exact size+date match for "Search again" to confirm a find |

**Colors** (`[Theme]`)

| Setting | What it does |
|---|---|
| `Name` | `basic` / `gruvbox` / `everforest` / `solarized` / `custom` |
| `Mode` | `dark` or `light` |
| `TimeBasedMode` | Switch Mode automatically by the clock instead |
| `LightStartHour` / `DarkStartHour` | The switch times, if TimeBasedMode=1 |
| `Background` / `Foreground` / `Heading` / `Green` / `Accent2` / `Yellow` / `Accent4` / `Muted` | The eight color roles, only used when `Name=custom` |

**Paths** (machine-specific - not meant to be shared/copied as-is)

| Setting | What it does |
|---|---|
| `Path` (inside `[Watched:Name]`) | The folder itself |
| `Exclude` | Sub-paths to skip inside that folder |
| `ExcludeExtensions` | Extensions to skip inside that folder |
| `RecIcon` / `ResetIcon` / `RefreshIcon` / `MenuIcon` / `AgeIcon` / `BackIcon` / `AutoRefreshIcon` / `SortResetIcon` / `LostIcon` / `FallbackIcon` | Per-entry icon overrides, all optional |

## Columns reference

All six live in `RecentTab.ini`, off by default, added to the panel
afterward via TC's own Shift+F1 "Configure custom columns".

| Column | Setting | Also works inside `! Lost` |
|---|---|---|
| Changed / New | `ShowChangeType=1` | Yes |
| Relative time | `ShowRelativeTime=1` | Yes |
| Source folder | `ShowSourceFolder=1` | Yes |
| Session number | `ShowSession=1` | Yes |
| Already opened | `ShowOpened=1` | Yes |
| Locked status | `ShowLocked=1` | No - the file's already known to be gone, a lock check would only ever say "Not found" |

## Requirements

- Windows 7 or newer, 32-bit or 64-bit Total Commander
- [Everything](https://www.voidtools.com) installed and running - this
  plugin connects to it directly over its own IPC protocol, no
  additional download or setup needed beyond having Everything itself
  running in the background

## Files in this package

- `RecentTab.wfx` / `RecentTab.wfx64` - the plugin (TC picks the right
  one automatically if both are present)
- `RecentTab_example.ini` - a reference copy of every available
  setting, shipped under this name (not `RecentTab.ini`) on purpose:
  extracting an update never overwrites your own configuration this
  way. Copy it, rename the copy to `RecentTab.ini`, and edit that copy -
  the plugin looks for that exact filename, and works fine with sensible
  defaults even before that file exists at all. After an update,
  compare this file against your own `RecentTab.ini` for anything new.
- `RecentTab_lang.ini` - interface text; **must stay in the same
  folder as the plugin file**, since it's looked up relative to its own
  location, not TC's config folder
- `Color_ini_example.ini` - a template for adding another language
- `icons\age.ico`, `icons\menu.ico` - the default icons for `! Search` and
  `! menu`, referenced via `[Icons]` - swap them for your own anytime,
  see Custom icons above
- `RecentTab_state.json` - created automatically on first use, not
  part of this package; remembers your recording history
- `RecentTab_opened.txt` - created automatically only if
  `ShowOpened=1` and `OpenedTracking=permanent`; remembers which files
  you've already opened through the panel

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

First time only: copy `RecentTab_example.ini`, rename the copy to
`RecentTab.ini`, in the same folder. Everything works with plain
defaults even without that step - it just means there's nothing yet to
edit if you want to change something.

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
