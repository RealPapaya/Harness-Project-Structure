# Failure Diagnosis — top failure modes per harness, with fixes
<!-- 中文摘要：三個 harness「最漏 token、最易失焦、最易出錯」的前三名與弱模型可照做的修法。
     其他檔案以代號引用（D=共通, C=Claude, X=Codex, H=Hermes）。按需讀，不常載。 -->

Read this when: a session feels wasteful, keeps drifting, or ships wrong results.
Each fix is written so a small model can follow it literally.

## Shared failure modes (all three harnesses)

### D1. Token leak — raw dumps in the main conversation
Symptom: whole files, full test logs, or full diffs pasted into the main session.
Fix, exactly:
- Never bring more than ~30 lines of any file/log into the main conversation.
- Delegate bulk reading/scanning (see dispatch.md). Delegates return a conclusion
  plus `path:line` references only. Long output goes to a file on disk; return the path.
- When you must look yourself, filter first: `rg <pattern>` with a head limit,
  `Get-Content file -TotalCount 30`, `npm test 2>&1 | Select-Object -Last 30`.

### D2. Focus drift after compaction or long sessions
Symptom: after context compaction or ~30+ turns, the model re-plans, repeats work,
or silently changes the goal.
Fix, exactly:
- At task start, write goal + acceptance criteria to a scratch file
  (`TASK.md` in the project, or the harness's todo/plan tool).
- After any compaction/summary notice, re-read that file BEFORE the next action.
- One session = one task for cheap models. Finish or hand off; never stack a second task.

### D3. Self-verification — "looks done" treated as done
Symptom: the model writes a change, reads its own diff, declares success.
Fix, exactly:
- "Done" requires evidence per judgment.md §Done. Minimum: tests/build actually run
  with output, OR a fresh-context agent read-back, OR the app actually launched.
- The agent that wrote a change never signs off alone on anything user-visible.

## Claude Code specific

### C1. Per-project memory silos
Auto-memory lives in `~/.claude/projects/<slug>/memory/` — lessons saved there never
reach other projects or the other two harnesses.
Fix: machine-global lessons go to `{{HOME}}\.agents\institution\lessons.md`;
leave only a one-line pointer in the project MEMORY.md.

### C2. Subagents spawned without an explicit model
An Agent call without `model:` inherits the (expensive) main-session model.
Fix: always pass `model` explicitly — `haiku` for search/bulk-read, `sonnet` for
implementation, `opus` only via the escalation ladder in dispatch.md.

### C3. Effort left on high
`/effort high` is saved as this machine's default; the harness itself warns it
"burns fastest — medium handles most tasks."
Fix: run `/effort medium` at session start unless the task is genuinely hard
(judgment.md §Escalate defines "genuinely hard").

## Codex CLI specific

### X1. No persistent memory at all
The `memories` feature flag is experimental/OFF on this install (verified 2026-07-05).
Anything not written to a file is lost when the session ends.
Fix: every lesson or decision worth keeping goes to `institution\lessons.md`
(or the project AGENTS.md if project-specific) before the session ends.

### X2. Deprecated approval policy
`~/.codex/config.toml` has `approval_policy = "on-failure"`, which Codex marks
DEPRECATED (commands run first, ask on failure).
Fix: ask the user before changing config; recommended value is `on-request`.
Until changed, prefer `codex exec` with narrow prompts for anything risky.

### X3. Config defaults to the most expensive settings
`~/.codex/config.toml` sets `model = "gpt-5.5"` (top tier) plus
`model_reasoning_effort = "high"` and `plan_mode_reasoning_effort = "high"` —
every session started without flags pays top rates even for trivial work.
Fix: always pass the model explicitly — `-m gpt-5.4-mini` for bulk/simple,
`-m gpt-5.4` for normal work. Optionally lower effort with
`-c model_reasoning_effort="medium"` (未實測 — only "high" is verified on this
install; if "medium" errors, drop the flag and rely on the cheaper model).

## Hermes specific

### H1. Default model is paid Opus via OpenRouter
`config.yaml` default is `anthropic/claude-opus-4.6` billed per token — every casual
message costs real money.
Fix: set default to the cheap tier in dispatch.md §Hermes (exact model id: 待使用者填寫),
escalate manually only per the ladder.

### H2. Staleness — Hermes is rarely used
Instructions and memory rot between uses.
Fix: on each Hermes use, read institution INDEX.md first and check its "Last updated"
line; trust the institution over anything Hermes remembers locally.

### H3. Silent memory auto-writes
`memory.write_approval: false` — Hermes writes its own MEMORY.md/USER.md unreviewed
(limits 2200/1375 chars).
Fix: treat Hermes local memory as cache, never as source of truth; anything durable
must also be written to `institution\lessons.md`.
