# RecentTab - Second build: real Everything integration

This build adds the actual Everything query on top of the first
skeleton. Still missing: JSON persistence (session start currently just
means "since this DLL was loaded"), the INI, Windows Recent-Items
integration for media, and reacting to Enter on the two pseudo-entries.

## What you should see

- `Recording: OFF` (size column now shows the real file count instead of 0)
- `Reset history`
- Below that: real files that were modified since you loaded the plugin
  (i.e. since TC started, or since the plugin was last reloaded) - try
  editing/saving a file somewhere, then refresh the tab (F5) to see it
  appear
- **Folders should never appear in the list** - if one does, that's
  exactly the bug we were trying to prevent; send me the log
- Moving/copying a large batch of files (your 5555-photos scenario) into
  a folder is a good stress test now - the folder itself should stay
  invisible, only the actual files should show up

## Where the log file is

`RecentTab_debug.log`, next to whichever .wfx/.wfx64 file Total Commander
loaded. Now also logs: whether Everything's DLL loaded successfully, the
exact query string sent to Everything, how many results came back, and
timing for both the DLL load and the query itself.


## What to send me if something looks wrong

- The full contents of `RecentTab_debug.log`
- Whether TC crashed completely, just the panel looked wrong, or
  something else
- Which build you tested (32-bit `RecentTab.wfx` or 64-bit `RecentTab.wfx64`)
- Your Total Commander version (Help - About Total Commander)

## A note on the header

`fsplugin.h` (the official Total Commander WFX SDK header, v2.1) is now
bundled directly in `src/` - you already provided it, so this build
uses your real copy instead of me trying to reconstruct it from memory.
While checking it against what I'd written, I actually caught a real
bug: `FsInit` must return `int`, not `void` - fixed now. Good thing to
have gotten from the source instead of guessing.

## Prerequisite for this build: Everything needs to be findable

Everything (installed or portable) needs to be running. The plugin
tries `Everything64.dll` (64-bit build) or `Everything32.dll` (32-bit
build) via a plain `LoadLibraryW` call, which succeeds if that DLL is
either already on the system search path, or copied next to
`RecentTab.wfx`/`RecentTab.wfx64` itself. If Everything is installed
normally, its own install folder is usually enough; for Everything
Portable, you may need to copy the matching DLL next to the plugin.
The log will clearly say `Everything DLL load result: FAILED` if this
isn't sorted out yet - not a crash, just falls back to pseudo-entries
only.

## Performance: what the log tells you

Every `FsFindFirstW` call now logs its own real elapsed time, e.g.:

```
[17:44:56.102] [TIMING] FsFindFirstW took 0.847 ms
```

Plus cumulative process CPU time at the start and end of each panel
interaction:

```
[17:44:56.101] [CPU] FsFindFirstW start - TC process total CPU so far: kernel=140.0 ms, user=310.0 ms
[17:44:59.980] [CPU] FsFindClose end - TC process total CPU so far: kernel=141.0 ms, user=312.0 ms
```

The kernel/user numbers are **cumulative for the whole TC process since
it started**, not just our plugin - so what actually matters is the
*difference* between two consecutive log lines around a `RecentTab`
interaction, not the absolute numbers. In the example above, roughly
2 ms of extra CPU time was attributable to opening/closing the panel
once - that's the kind of number that answers "how much load does this
generate" honestly, once measured on your real machine.

This build's numbers will be close to zero, since there's no Everything
query yet - just two hardcoded strings. Once we add the real Everything
integration, the same instrumentation carries over automatically, and
that's when the numbers actually become meaningful. Worth testing again
at that point, with your real (possibly large) Everything index.

## Known limitation of this debug setup

The crash handler (`SetUnhandledExceptionFilter`) catches unhandled
Windows-level exceptions (access violations etc.) system-wide for the
process - but if Total Commander itself has its own crash handler
installed already, ours might not always get the first look. If TC
crashes with no corresponding log entry, that's useful information
too - it tells us the crash likely didn't originate in our code.
