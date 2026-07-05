# Global instructions — thin index only
<!-- 中文摘要：這是薄索引，規則正本在 {{HOME}}\.agents\institution\。
     不要在此檔累積規則；要加規則就寫進正本，這裡最多加一行路由。 -->

READ FIRST every session:
1. `{{HOME}}\.agents\institution\core-rules.md` — always-on rules
2. `{{HOME}}\.agents\institution\INDEX.md` — map of on-demand files

Then follow the triggers in INDEX.md (dispatch before delegating, coding-rules
before writing code, judgment before claiming done, maintenance before editing
instruction/memory files).

## Claude-Code-specific (this harness only)
- Subagents: use the Agent tool and ALWAYS pass `model:` explicitly —
  `haiku` for search/bulk-read/batch, `sonnet` for implementation (default),
  `opus` only via the escalation ladder in dispatch.md.
- Effort: if high effort is the saved default, run `/effort medium` at session
  start; raise only for genuinely hard tasks (diagnosis.md C3).
- Memory: this harness auto-keeps per-project memory in
  `~/.claude/projects/<slug>/memory/`. Machine-global lessons must ALSO go to
  `{{HOME}}\.agents\institution\lessons.md`, with only a pointer left in the
  project MEMORY.md (see maintenance.md).

## Rules for this file
- Routing only; hard cap 150 lines. If you are about to add a rule here, put it
  in the institution and reference it instead (maintenance.md §4).
