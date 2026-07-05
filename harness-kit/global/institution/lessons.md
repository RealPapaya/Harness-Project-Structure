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
- What: Codex CLI (`codex.cmd`, v0.142.x) provides independent cross-family
  review of plans and diffs; user keywords like 交叉驗證 / cross-validate should
  trigger it.
- Why it matters: same-family models share blind spots; a GPT-family second
  opinion catches errors Claude-family review misses (and vice versa).
- How to apply: send the plan/diff to Codex per templates.md T5 via
  `codex exec -m gpt-5.4 "<review brief>"`. Keep the prompt in English. Filter
  long output (e.g. `| Select-Object -Last 40`). Treat disagreements as findings
  to resolve, not noise.
- Origin: Claude Code, 2026-06, TWCARDGAME (promoted from project memory).
