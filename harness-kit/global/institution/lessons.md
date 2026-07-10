# Lessons — machine-global, cross-harness
<!-- 中文摘要：三個 harness 共用的踩雷教訓。追加格式見 maintenance.md §2；
     超過 200 行要精簡。 -->

## powershell-utf8-roundtrip-corrupts-cjk
- What: On this machine, piping text files through PowerShell `Get-Content |
  Set-Content` (or `Out-File` without explicit encoding) corrupts CJK characters
  and em-dashes.
- Why it matters: silently destroys Chinese comments/strings in source files;
  damage may be committed before anyone notices.
- How to apply: never round-trip source files through PowerShell string cmdlets.
  Use the harness's Edit tool for all text replacement. If corruption already
  happened: `git checkout -- <file>` and re-apply with Edit. When PowerShell must
  write a file other tools will read, pass `-Encoding utf8` explicitly.
- Origin: Claude Code, 2026-06, TWCARDGAME (promoted from project memory).

## bat-file-crlf-gotcha
- What: Generated `.bat` files must be CRLF line endings and the batch section
  must be ASCII-only, or cmd.exe fails in confusing ways.
- Why it matters: LF-only or UTF-8-BOM batch files break at runtime with
  misleading errors.
- How to apply: after generating a .bat, convert LF→CRLF; keep the batch section
  ASCII; if UTF-8 output is needed, do it from an embedded PowerShell block
  inside the .bat (pattern verified on TWCARDGAME.bat).
- Origin: Claude Code, 2026-06, TWCARDGAME (promoted from project memory).

## cross-validate-with-codex
- What: Use the opposite model family for independent review: Claude reviews
  GPT-produced plans/diffs; Codex CLI reviews Claude-produced work. User keywords
  like 對抗檢查 / 交叉驗證 / cross-validate should trigger it.
- Why it matters: same-family models share blind spots; a GPT-family second
  opinion catches errors Claude-family review misses (and vice versa).
- How to apply: send a T5 adversarial brief to the opposite-family CLI. For a
  Claude-produced artifact use `codex.cmd exec "<review brief>"` so it inherits
  the config-selected GPT-5.6 commander; for GPT-produced work use Claude. Keep
  the prompt in English. Filter long output and resolve disagreements instead
  of averaging them away.
- Origin: Claude Code, 2026-06, TWCARDGAME (promoted from project memory).

## install-20260707
- What: Re-synced the deployed institution at {{HOME}}\.agents\institution\
  from the harness-kit master ({{KIT}}).
  Kit was newer on 4 files: core-rules.md + dispatch.md (Workflow-tool tiering),
  INDEX.md (Last-updated 2026-07-06), maintenance.md (Git-aware backup rule).
  Merged the two thin indexes: added the Dynamic-Workflow / Multi-agent hard-stop
  rules from the kit while preserving this machine's specific facts (Codex model
  ids gpt-5.5/5.4/5.4-mini, config.toml defaults, sandbox note; Claude effort note).
  SOUL.md already carried a customized routing block -> left untouched.
- Why it matters: keeps all three harnesses loading the current canon; the merge
  pattern avoids clobbering machine-specific facts the portable kit template cannot hold.
- Verification: 0 placeholders remaining; all 4 updated institution files byte-match
  the kit (placeholders substituted); all 10 files retain the 中文摘要 header
  (no CJK corruption); 3 indexes route to .agents\institution with no placeholders.
  Codex load test run separately.
- Origin: Claude Code, 2026-07-07, Harness Project Structure.

## codex-exec-background-stdin-hang
- What: `codex.cmd exec` launched from a non-tty context (backgrounded task) prints
  "Reading additional input from stdin..." and hangs until timeout waiting for
  stdin EOF. Two adjacent traps: PowerShell 5.1 breaks native-exe args that
  contain embedded double quotes (splits into multiple args), and `--%` is not
  accepted by codex's arg parser.
- Why it matters: a cross-family T5 review silently stalls for the full timeout
  and burns the review round.
- How to apply: build the brief as a single-quoted here-string with NO embedded
  double quotes, then pipe it via stdin with the `-` argument:
  `$brief | codex.cmd exec -m gpt-5.5 -C <workdir> -`
- Origin: Claude Code, 2026-07-07, Harness Project Structure (T5 review of the
  vizcode-harness-scan example plan).

## windows-python-wrong-interpreter-in-claude-code
- What: In Claude Code on this PC, `python`/`python.exe` resolve to interpreters
  that DON'T have the project installed: the Bash tool's PATH puts the hermes
  agent venv first (`...\hermes-agent\venv\Scripts\python.exe`), and the bare
  system `python` (`...\Programs\Python\Python311`) has no project deps/pytest.
  Running `pytest` under either gives `ModuleNotFoundError: No module named
  <pkg>` or `No module named pytest`, which looks like a broken repo but is just
  the wrong interpreter.
- Why it matters: burned ~10 min chasing a phantom "env is broken" before
  realizing the tests were fine — wrong python, not wrong code.
- How to apply: for a project with its own venv, ALWAYS invoke the venv python by
  explicit path — `.\.venv\Scripts\python.exe -m pytest` (PowerShell) — never
  bare `python`. If no venv exists, create one (`<system-py> -m venv .venv;
  .\.venv\Scripts\python -m pip install -e ".[dev]"`) rather than trusting the
  ambient interpreter. `py -0p` lists real installs; `(Get-Command python).Source`
  shows what a bare call actually hits.
- Also: when many files share an identical recent mtime but `git status` is clean,
  it's a `git pull`/checkout (check `git reflog`), NOT a concurrent local editor.
- Origin: Claude Code, 2026-07-09, Solomon Phase 3 writer session.

## codex-5-6-commander-5-5-worker
- What: On this machine, Codex CLI 0.144.1 exposes multiple local GPT-5.6 slugs;
  `gpt-5.6-sol` and `gpt-5.6-terra` were both observed on 2026-07-10. The config
  selects the active variant. The institution pairs that GPT-5.6 commander with
  `gpt-5.5` as the low-to-high-effort worker; GPT-5.4 is retired.
- Why it matters: preserves strong synthesis while preventing scans, batch work,
  and routine implementation from inheriting the most expensive model.
- How to apply: keep the main session on the config-selected GPT-5.6 variant;
  never hardcode a 5.6 suffix in shared dispatch rules. Launch workers with
  `-m gpt-5.5` plus explicit effort. Use Claude for adversarial review of
  GPT-produced artifacts when independent verification is required.
- Origin: Codex, 2026-07-10, Harness Project Structure.

## edge-headless-maplibre-screenshot-artifacts
- What: Edge headless can produce black compositor rectangles outside a MapLibre
  canvas when one page captures several WebGL views, or when the canvas is read
  with `drawImage` immediately before `page.screenshot`. A clean page screenshot
  without canvas read-back renders correctly.
- Why it matters: the artifact looks like a severe CSS/UI regression even though
  the live DOM has correct backgrounds and reports no page errors.
- How to apply: wait for `map.loaded() && map.areTilesLoaded()`, capture the target
  view in a fresh page, and do not read the WebGL canvas before the screenshot.
  Treat a multi-view black rectangle as a harness artifact until a clean-page
  reproduction confirms it.
- Origin: Codex, 2026-07-10, commute-qualifying.
