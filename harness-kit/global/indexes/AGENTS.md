# Global instructions — thin index only
<!-- 中文摘要：Codex 全域薄索引，規則正本在 {{HOME}}\.agents\institution\。
     不要在此檔累積規則；要加規則就寫進正本，這裡最多加一行路由。 -->

READ FIRST every session:
1. `{{HOME}}\.agents\institution\core-rules.md` — always-on rules
2. `{{HOME}}\.agents\institution\INDEX.md` — map of on-demand files

Then follow the triggers in INDEX.md (dispatch before delegating, coding-rules
before writing code, judgment before claiming done, maintenance before editing
instruction/memory files).

## Codex-specific (this harness only)
- Model tiers live in core-rules.md §2 (verify with `codex features` /
  models cache on THIS machine before trusting them — they may differ per
  machine/date).
- Multi-agent hard stop: do not spawn subagents unless the user explicitly asks
  for subagents, delegation, or parallel agent work. If spawning is authorized,
  show the agent plan table (task / model / effort / reason) before running.
- Cheap delegation: `codex exec -m <cheap model> "<task brief>"` in a separate
  session. Task briefs use templates.md forms.
- COST WARNING: if config.toml pins an expensive default model or high
  reasoning effort, always pass `-m` explicitly (diagnosis.md X3).
- No persistent memory on this harness — anything worth keeping goes to
  institution `lessons.md` before session end (diagnosis.md X1, maintenance.md §2).
- Do not change `~/.codex/config.toml` without asking the user
  (maintenance.md §1, "ASK THE USER first").

## Rules for this file
- Routing only; hard cap 150 lines. If you are about to add a rule here, put it
  in the institution and reference it instead (maintenance.md §4).
