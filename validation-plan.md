# Validation Plan — Preserve custom clip title when editing content by default

**Upstream issue:** sabrogden/Ditto#1167
**Branch:** `havra/preserve-custom-clip-title`
**Change:** `src/Options.cpp` — default of `CGetSetOptions::GetUpdateDescWhenSavingClip()` changed from `TRUE` to `FALSE` (registry key `UpdateDescWhenSaving`). Users who never opt in no longer lose a manually set description/title when editing a clip's content. The EditWnd checkbox "Update clip description on save?" still lets users opt in to regeneration per-edit and persists their choice.

## Observable checks

| # | Check | User actions | Expected result | Evidence |
|---|-------|--------------|-----------------|----------|
| 1 | **Baseline: app launches** | Launch the built `CP_Main.exe` | Ditto tray icon appears; Quick Paste window opens on hotkey without crash | Screenshot of Quick Paste window open |
| 2 | **Baseline: string capture appears in Quick Paste UI** | Copy a distinctive text string (e.g. `DITTO-VAL-2026`) from Notepad; open Quick Paste | New entry at top of list whose description starts with the copied text | Screenshot of list entry |
| 3 | **Baseline: image capture appears in Quick Paste UI** | Copy an image (e.g. via Snipping Tool or PrintScreen); open Quick Paste | New thumbnail entry appears at top of list | Screenshot of thumbnail |
| 4 | **Change-specific: custom title preserved on edit (default path)** | a) Ensure no prior `UpdateDescWhenSaving` registry value exists (fresh/never-touched install state). b) Copy text `Alpha original content`. c) Set its description/title to a custom string (e.g. via clip properties/Edit, set description to `CUSTOM-TITLE-XYZ`). d) Open the clip in Edit (content editing), change the text to `Beta edited content`, save. e) Reopen Quick Paste / view entry | Description/title still reads `CUSTOM-TITLE-XYZ`; it is NOT overwritten with the start of `Beta edited content` | Screenshot of the entry showing preserved custom title after edit+save |
| 5 | **Change-specific: checkbox still regenerates description when checked** | a) Open the same clip in Edit. b) Confirm the "Update clip description on save?" checkbox is checked. c) Change content to `Gamma regenerated`, save | Description updates to the start of `Gamma regenerated` (opt-in regeneration still works) | Screenshot of entry with regenerated description |
| 6 | **Option persistence round-trip** | In the Edit window, uncheck "Update clip description on save?", save an edit | Choice persists: description stays custom after save; reopening Edit shows the checkbox state matching the saved setting | Screenshot of Edit window checkbox + resulting entry |

## Notes / pass criteria

- Checks 1–3 must pass before checks 4–6 are meaningful (baseline health).
- Check 4 is the core regression check for issue #1167. Pass requires the description to be byte-identical to the custom string the user set.
- Check 5 guards against breaking the existing opt-in behavior that the task explicitly requires to keep working.
- All evidence: screenshots captured by the validator subagent into `validation/` alongside `validation-results.json` and `validation-report.html`.
