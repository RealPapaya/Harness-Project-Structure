# Model Dispatch Playbook
<!-- 中文摘要：指揮官不下場。怎麼交辦、怎麼選模型、怎麼升降級、怎麼驗收、
     三個 harness 各自怎麼派工。派工前必讀。 -->

## 0. The commander stays out of the trenches
The session talking to the user (the "commander") does not read whole repos, fetch
web pages, run batch edits, or verify its own output. It decomposes, dispatches,
integrates conclusions, and makes only the judgments that would degrade on a
cheaper model. If you catch yourself pasting file contents into the main thread,
stop and dispatch instead (diagnosis.md D1).

## 1. Task brief = three mandatory parts
Use the fill-in forms in templates.md. Every brief contains:
1. **Goal + why** — what to produce and what it will be used for (the "why" lets
   the delegate make small decisions without coming back).
2. **Acceptance criteria** — objectively checkable conditions ("all tests in X
   pass", "every match listed with path:line", "file exists and is ≤N lines").
3. **Report format** — exactly what to return (conclusions + path:line;
   artifacts written to disk with the path returned; length cap).
A brief missing any part is not ready to send.

## 2. Per-harness dispatch (verified 2026-07-05)

### Claude Code
- Mechanism: Agent tool, parallel calls allowed. ALWAYS set `model:` explicitly.
- Tiers: `haiku` = haiku-4-5 (cheap) · `sonnet` = sonnet-4-6 (mid) · `opus` = opus-4-8 (top).
- Effort: `/effort` per session; default medium. Subagents inherit unless overridden.
- Cheap tier is for: repo scans, multi-file reads, log digestion, batch mechanical
  edits, read-back verification. Mid tier: implementation. Top: ladder only (§4).
- **Workflow tool** (multi-agent orchestration script): the SAME tiering applies
  per `agent()` call -- pass `model` / `effort` explicitly on each. Cheap mechanical
  stages (scan, batch-transform, read-back) -- `model:'haiku'`/`effort:'low'`;
  implementation stages -- mid (omit `model` to inherit session); only the hardest
  verify/judge/synthesis stages get top tier. Omitting `model` inherits the session
  model (often top) -- do NOT let simple fan-out stages silently run on the top tier.
  Only author a Workflow when the user explicitly opts in (keyword/ultracode/"use a
  workflow"); otherwise use the Agent tool.

### Codex CLI (0.142.5)
- Models: `gpt-5.5` (top) · `gpt-5.4` (mid) · `gpt-5.4-mini` (cheap). Set with `-m`.
  Config default is `gpt-5.5` — ALWAYS pass `-m` explicitly (diagnosis.md X3).
- Reasoning effort: `-c model_reasoning_effort="low|medium|high"`
  (global config is `high`, and `plan_mode_reasoning_effort` is also `high`;
  `high` is the only value verified on this install — others are documented
  convention, 未實測).
- Subagents: `multi_agent` feature flag is stable/true, but the invocation surface
  is 未實測. The RELIABLE pattern is a separate low-cost session:
  `codex exec -m gpt-5.4-mini "<paste the full task brief>"`.
  `codex exec` is non-interactive: the brief must be fully self-contained
  (include absolute paths; the delegate cannot ask questions).
- No memory (diagnosis.md X1): the brief must contain all context; the report
  must be written to a file if the commander session needs it later.

### Hermes (rarely used)
- Mechanism: `delegate_task` toolset — max 3 concurrent children, depth 1,
  `subagent_auto_approve: false` (user confirms spawns).
- Models: default `anthropic/claude-opus-4.6` (paid per token, OpenRouter).
  Cheap tier: 待使用者填寫 (user chose a non-Claude OpenRouter model; set it in
  Hermes `/model` or config.yaml `model.default`, then record the id here).
- Until the cheap tier is filled in, avoid dispatching bulk work through Hermes at all.

## 3. Report contract (binding on every delegate)
- Return: conclusions, decisions needed from the commander, `path:line` refs.
- Do NOT return: file dumps, full logs, restated instructions.
- Long products (reports, generated code lists, scan results) → write to a file
  under the project (or `%TEMP%` for throwaways) and return the path.
- Hard cap: if the report would exceed ~40 lines, it belongs in a file.

## 4. Escalation / downgrade ladder
- **Cheap fails once** on a subtask → retry once on the MID tier with the failure
  attached (what was tried, exact error text).
- **Mid fails the SAME subtask twice** → escalate to TOP tier, attaching the full
  failure trail: every attempt, every error, current hypothesis. Never escalate
  with just "it didn't work".
- **Top tier solves it** → extract the pattern (what the fix looks like) and
  downgrade back to the cheap tier to batch-apply across remaining instances.
- **Max two retry rounds per approach.** Two failed rounds means the approach is
  wrong, not the model — apply judgment.md §Wrong-direction (change approach or
  stop and ask), do not burn a third round.
- Escalating across harnesses (e.g. Claude → Codex) is for independent second
  opinions (§6), not for "my harness failed so try yours".

## 5. Verification is never self-service
- Docs/config written → a FRESH agent/session reads the file back against the
  acceptance criteria ("read X; does it satisfy: 1)… 2)…; answer per item").
- Code written → tests or the app actually run; paste only the last ~20 lines of
  output as evidence.
- High-stakes judgment (architecture choice, irreversible ops, anything the user
  will act on) → second opinion from a different model family (Claude↔Codex),
  or 2–3 independent answers with a judge pass picking the best.
- The writer never verifies alone. A verification that only says "looks good"
  without per-criterion answers is void — redo it.

## 6. Cross-harness division of labor
- **Claude Code** — primary dev harness: real subagent fleet with per-call model
  choice, skills, MCP, per-project memory. Default home for multi-step work.
- **Codex CLI** — (a) independent second opinions on plans/diffs (different model
  family = real diversity; keep prompts in English, pipe long output through
  filters — see lessons.md #cross-validate); (b) cheap non-interactive batch via
  `codex exec -m gpt-5.4-mini`; (c) its sandbox (workspace-write) suits contained
  command experiments.
- **Hermes** — rarely used. Niche strengths if ever needed: messaging-platform
  integrations, cron jobs, multi-provider model access. Keep its footprint thin;
  do not build workflows that depend on it.
