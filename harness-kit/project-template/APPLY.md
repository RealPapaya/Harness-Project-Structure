# APPLY — wire one project into the institution
<!-- 中文摘要：給任何 AI agent 照做的「套用到一個專案」交辦包。新專案直接套範本；
     既有專案採合併不覆蓋，原檔一律備份。 -->

You are an AI agent executing this. `<PROJECT>` = the absolute path of the
target project (the user tells you). `<KIT>` = this kit's absolute path.
`<HOME>` = the user's home directory. Replace `{{HOME}}` / `{{PROJECT_NAME}}` /
`{{...}}` placeholders with real values in every file you install. Use your
file-Edit tool, never PowerShell `Get-Content | Set-Content` (corrupts non-ASCII).

## Case A — project has NO CLAUDE.md and NO AGENTS.md
1. Copy the three template files (`AGENTS.md`, `CLAUDE.md`, `LESSONS.md`) from
   `<KIT>\project-template\` into `<PROJECT>`.
2. Fill the "Project specifics" slots in AGENTS.md by INSPECTING the repo
   (package.json / Makefile / README give build-test-run commands; find the
   shared/core files; pick two representative source files as style anchors).
   A slot you cannot determine: write `待使用者填寫`, never guess.

## Case B - project already has CLAUDE.md (and maybe AGENTS.md)
1. For every existing file you will touch, apply the Git-aware backup rule
   from global `maintenance.md` section 1: skip `.bak` only when Git can
   restore the tracked file; back up untracked/ignored/non-Git files as
   `<name>.bak-YYYYMMDD` in place.
2. Install `LESSONS.md` from the template (skip if one already exists).
3. AGENTS.md:
   - If missing: copy the template AGENTS.md in, fill its slots using the
     EXISTING CLAUDE.md's content (it usually already documents build/test
     commands and conventions). Add at the bottom:
     `Legacy project docs: CLAUDE.md in this directory (kept as-is).`
   - If present: add the template's "Global rules" line + "Session protocol"
     block at the TOP; keep all existing content below; stay ≤150 lines
     (overflow: move detail into docs/ and leave pointers).
4. CLAUDE.md: add these 2 lines at the TOP; keep everything else unchanged:
   ```
   Global rules: read {{HOME}}\.agents\institution\core-rules.md first.
   Session protocol + project commands: read AGENTS.md in this directory.
   ```
   (Replace `{{HOME}}` with the real home path BEFORE pasting — this snippet
   goes into an existing file, so the global replace step won't catch it.)

## Verify (both cases)
1. Read back each installed/modified file: no `{{...}}` placeholders remain
   (except explicit 待使用者填寫 slots); routing lines point to real paths
   (Test-Path each).
2. Run the project's test command from AGENTS.md once. If it fails on a clean
   checkout, record that in LESSONS.md (first lesson!) rather than "fixing" it.
3. Report per the contract: files changed with backup paths or
   Git-protected/no-backup notes, unresolved slots, and test-command result.
   No file dumps.
