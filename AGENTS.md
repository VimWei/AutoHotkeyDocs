# AGENTS.md — AutoHotkey v2 Help Docs (Query Guide)

Official AutoHotkey v2 documentation, served as GitHub Pages static HTML. Registry slug `ahk`, docType `gh-pages`.

## Architecture

- `docs/*.htm` at root (24 files) — main topic pages: Concepts, Language, Variables, Hotkeys, Hotstrings, Objects, FAQ, Tutorial, etc.
- `docs/lib/*.htm` (~356 files) — one file per built-in function, command, or directive; filename IS the function name
- `docs/lib/index.htm` — alphabetical function index linking to every lib page
- `docs/howto/*.htm` (6 files) — beginner tutorials on hotkeys, send keys, run programs, window management
- `docs/misc/*.htm` (27 files) — reference tables: RegEx quick ref, WinTitle, Styles, ControlID, Colors, Remap, etc.
- `docs/scripts/*.ahk` (18 files) — example scripts; read directly, not converted
- `docs/static/` — CSS, JS, images, fonts; stripped by converter
- `docs/static/source/*.js` — data files driving search index/TOC; NOT documentation content
- `docs/search.htm`, `docs/404.htm` — JS-only pages with no static content
- `compile_chm.ahk`, `Project.hhp` — CHM build tooling; ignored

## Conversion

Run from the `dev-docs` project root:

```
uv run python scripts/html2md.py ahk
```

Output goes to `%TEMP%/dev-docs/ahk/`. The `GhPagesAdapter`:
- Strips sidebar/toc/nav `<div>` elements (AHK uses JS-generated sidebars)
- Removes `<header>`, `<footer>`, `<nav>`, all `<script>`, `<style>`, `<meta>`, `<link>` tags
- Removes static asset `<img>` tags (logo, icons)
- Rewrites `<a href="*.htm">` → `*.md`; skips `mailto:`, `#anchor`, and `http(s)://` links

Always convert before querying raw HTML.

## How to find topics

### By filename pattern

Lib files are named exactly after the function/command they document. The filename IS the lookup key.

| User asks about | Look for files matching |
|---|---|
| a specific function/command (e.g. MsgBox, Send, Run) | `docs/lib/<FunctionName>.htm` — filename matches function exactly |
| all available functions / function index | `docs/lib/index.htm` |
| hotkeys / keyboard shortcuts / hotkey modifiers | `docs/Hotkeys.htm`, `docs/lib/Hotkey.htm`, `docs/lib/HotIf.htm` |
| hotstrings / auto-replace / text expansion | `docs/Hotstrings.htm`, `docs/lib/Hotstring.htm` |
| sending keystrokes / Send / SendInput / key names | `docs/lib/Send.htm`, `docs/KeyList.htm`, `docs/howto/SendKeys.htm` |
| variables / expressions / operators / built-in vars | `docs/Variables.htm`, `docs/Language.htm` |
| objects / arrays / maps / properties | `docs/Objects.htm`, `docs/lib/Object.htm`, `docs/lib/Array.htm`, `docs/lib/Map.htm` |
| GUI / windows / controls | `docs/lib/Gui.htm`, `docs/lib/GuiControl.htm`, `docs/lib/GuiControls.htm`, `docs/lib/Win*.htm`, `docs/lib/Control*.htm` |
| COM / DllCall / external APIs | `docs/lib/Com*.htm`, `docs/lib/DllCall.htm` |
| loops / For / While / Loop | `docs/lib/Loop.htm`, `docs/lib/LoopFiles.htm`, `docs/lib/LoopParse.htm`, `docs/lib/LoopRead.htm`, `docs/lib/LoopReg.htm`, `docs/lib/For.htm`, `docs/lib/While.htm` |
| regex / RegExMatch / RegExReplace | `docs/misc/RegEx-QuickRef.htm`, `docs/lib/RegExMatch.htm`, `docs/lib/RegExReplace.htm` |
| file operations / FileRead / FileCopy / FileExist | `docs/lib/File*.htm`, `docs/lib/Dir*.htm` |
| control flow / If / Else / Switch / Try | `docs/lib/If.htm`, `docs/lib/Else.htm`, `docs/lib/Switch.htm`, `docs/lib/Try.htm` |
| menus / tray / Menu | `docs/lib/Menu.htm`, `docs/lib/MenuFromHandle.htm`, `docs/lib/TraySetIcon.htm` |
| processes / Run / Shutdown | `docs/lib/Process*.htm`, `docs/lib/Run.htm`, `docs/lib/Shutdown.htm` |
| INI / registry / settings | `docs/lib/Ini*.htm`, `docs/lib/Reg*.htm` |
| tutorial / getting started / how to | `docs/Tutorial.htm`, `docs/Program.htm`, `docs/howto/*.htm` |
| language syntax / comments / strings | `docs/Language.htm`, `docs/Concepts.htm` |
| changes / changelog / v1 differences | `docs/ChangeLog.htm`, `docs/v2-changes.htm`, `docs/v1-changes.htm`, `docs/Compat.htm` |

### By metadata (after conversion)

AHK pages have `<meta name="description">` but no `<meta name="keywords">` tags. The converter strips all `<meta>` tags, so descriptions are not present in converted MD. The `docs/static/source/data_search.js` file contains a full-text search index but is a JS data structure, not grep-friendly. For broad searches, grep the converted MD output or use the `data_index.js` for function-name lookups.

### Navigation

- `docs/index.htm` is the landing page and TOC — it links to all major topic areas (getting started, scripts, keyboard/mouse, misc)
- `docs/lib/index.htm` is the alphabetical function index listing all ~350 functions with descriptions
- Most pages do not have explicit "Top"/"Next"/"Previous" navigation links (GitHub Pages style)

## Key constraints

- Charset is UTF-8 — handled automatically by the converter.
- Link rewriting converts `.htm` → `.md` only when the href suffix is exactly `.htm`. Links with fragment anchors (e.g. `Concepts.htm#variables`) are **not** rewritten because they don't end with `.htm`. These remain pointing to `.htm` in the converted output.
- `docs/search.htm` is a JS-only search client page; the converter reduces it to a single line. Not useful for documentation lookup.
- `docs/static/source/data_index.js` and `data_toc.js` are the source of truth for the function index and TOC structure. They can be read directly for cross-referencing function names if unsure.
- Each `docs/lib/*.htm` documents exactly one function/command/directive — the filename is the function name. No aliases or redirects exist.
