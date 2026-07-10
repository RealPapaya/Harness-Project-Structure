# INSTALL — deploy the institution on this machine
<!-- 中文摘要：給任何 AI agent（弱模型可）照做的安裝交辦包。整個流程：
     複製正本 → 換佔位符 → 裝三份薄索引 → 驗證。全程有驗收條件。 -->

You are an AI agent executing this install. Follow the steps literally.
GOAL: make every future session on this machine (Claude Code / Codex CLI /
Hermes) load the shared institution. Everything is reversible; existing files
are backed up, never overwritten blindly.

Conventions below: `<KIT>` = the absolute path of the `harness-kit` folder this
file lives in. `<HOME>` = the current user's home directory (Windows:
`$env:USERPROFILE`, e.g. `C:\Users\<YourUsername>`; macOS/Linux: `$HOME`). Windows
paths shown; on Unix use `~/.agents/institution/` etc. with forward slashes.

## Step 1 — copy the institution
Copy `<KIT>\global\institution\*` (10 .md files) to `<HOME>\.agents\institution\`
(create the directory). If the target already has files, apply the Git-aware
backup rule from maintenance.md section 1: skip `.bak` only when every
existing target file is Git-tracked; otherwise back the whole target
directory up first (`institution.bak-YYYYMMDD`). Then overwrite; the kit is
the master.

## Step 2 — replace the placeholders
In every copied file, replace:
- every literal `{{HOME}}` with the ACTUAL home path (e.g. `C:\Users\<YourUsername>`);
- every literal `{{KIT}}` with the ACTUAL absolute path of the harness-kit
  folder on this machine (the parent of the `global` folder this file is in).
Preferred method: python (read utf-8 → replace → write utf-8). No python →
use your harness's file-Edit tool per file. NEVER use PowerShell
`Get-Content | Set-Content` (it corrupts non-ASCII text; the files contain
Chinese).
ACCEPTANCE: a search for `{{HOME}}` or `{{KIT}}` across
`<HOME>\.agents\institution\` returns ZERO matches, and spot-opening
core-rules.md shows real paths.

## Step 3 — install the three thin indexes
Templates are in `<KIT>\global\indexes\`.
1. **Claude Code**: target `<HOME>\.claude\CLAUDE.md`.
   - Target missing → copy the template in.
   - Target exists: apply the Git-aware backup rule from maintenance.md
     section 1, then put the template's routing block at the TOP and keep
     any user content below it. Result must stay <=50 lines.
2. **Codex CLI**: target `<HOME>\.codex\AGENTS.md`. Same rules as above.
3. **Hermes** (skip if Hermes is not installed): find SOUL.md — check
   `<HOME>\AppData\Local\hermes\SOUL.md` (Windows) or `<HOME>\.hermes\SOUL.md`.
   Apply the Git-aware backup rule from maintenance.md section 1, then APPEND
   the contents of `<KIT>\global\indexes\SOUL-block.md` to the end. Never
   touch the persona text above the block.
4. **Placeholders**: apply the Step-2 replacement (`{{HOME}}`, `{{KIT}}`) to
   every index file you just installed/modified. This step is mandatory.

## Step 4 — verify (do not skip)
1. Read back each installed file; confirm it points to
   `<HOME>\.agents\institution\` and contains no `{{HOME}}`.
2. Confirm all 10 institution files exist and each still starts with a
   `<!-- 中文摘要` comment (proves no encoding corruption).
3. Codex load test (skip and mark N/A in the report if Codex CLI is not
   installed on this machine): run
   `codex.cmd exec -m gpt-5.5 -c model_reasoning_effort="low" "Reply ONLY with the absolute paths of instruction files loaded into your context, or NONE."`
   If the global AGENTS.md is NOT in the answer, record that in
   `<HOME>\.agents\institution\lessons.md` — project-level AGENTS.md (via
   project-template\APPLY.md) then becomes mandatory for Codex on this machine.
4. Append one line to `<HOME>\.agents\institution\lessons.md`:
   `## install-<YYYYMMDD>` + What/verification results, per maintenance.md §2.

## Report
Return: list of files created/modified (absolute paths), backup paths or
Git-protected/no-backup notes, the three verification results, and anything
skipped per the report contract
(conclusions only, no file dumps).
