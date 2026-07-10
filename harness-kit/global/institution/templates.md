# Task Brief Templates
<!-- 中文摘要：五種任務型態的交辦範本（搜尋/實作/重構/研究/審查），填空即用。
     每份含驗收條件與回報格式。底部說明各 harness 怎麼把範本派出去。 -->

Rules: fill every {slot}; delete no section. The delegate cannot ask questions —
briefs must be self-contained with absolute paths. Report contract of
dispatch.md §3 applies on top of every template.

## T1. Search / locate
```
GOAL: Find {what} in {scope: dir/repo, absolute path}. Needed because {why}.
DO: Search using multiple naming conventions and synonyms for {what}.
    Check at least: {likely locations / patterns}.
ACCEPTANCE: every match listed; if zero matches, state the 3+ patterns and
    directories you tried so absence is provable.
REPORT: one line per match: `path:line — 1-sentence relevance`. Max 30 lines;
    overflow to a file, return the path. End with: what you did NOT search.
```

## T2. Implement
```
GOAL: Implement {feature/fix} in {project, absolute path}. Motivation: {why}.
CONSTRAINTS: follow existing code style; touch only {allowed area}; new logic in
    isolated modules where possible; do not modify {forbidden files}.
ACCEPTANCE: {observable behavior}; {test command} passes; no changes outside
    the allowed area (`git status` clean elsewhere).
REPORT: files changed as `path:line-range — what`; last ~20 lines of the test
    run; anything skipped or uncertain, explicitly listed. Do not paste diffs.
```

## T3. Refactor
```
GOAL: Refactor {target} to {end state}. Motivation: {why}.
CONSTRAINTS: behavior must not change; {test command} is the safety net —
    run BEFORE (record baseline) and AFTER.
ACCEPTANCE: before/after test results identical; {structural goal} achieved
    (e.g. "no file >300 lines", "module X no longer imports Y").
REPORT: baseline vs after test summary; files changed as `path — what moved
    where`; any behavior difference found = STOP and report, do not adapt tests.
```

## T4. Research
```
GOAL: Answer: {question}. It informs {decision}.
DO: consult multiple independent sources; prefer primary docs over blogs;
    note version/date of everything (this machine: verify against local
    installs where possible before trusting the web).
ACCEPTANCE: every load-bearing claim carries a source you actually opened;
    unverifiable claims are marked UNVERIFIED, never guessed.
REPORT: answer in ≤10 lines, then numbered sources. Full notes to a file if
    long; return the path. End with confidence: high/medium/low + why.
```

## T5. Review (code, plan, or document)
```
GOAL: Review {artifact, absolute path or diff ref} for {dimensions: correctness /
    security / performance / clarity}. It will be used for {purpose}.
DO: adversarial stance — try to find what is WRONG, not to approve.
    For code: trace at least the {N} riskiest paths by hand.
ACCEPTANCE: each finding has `path:line`, severity (blocker/major/minor), and a
    concrete fix suggestion; zero findings requires stating what you checked.
REPORT: findings list sorted by severity, max 30 lines (overflow to file).
    No restating the artifact. End with verdict: ship / fix-then-ship / rethink.
```

## Dispatching a template, per harness
- **Claude Code**: Agent tool; template text as the prompt; set `model:` per the
  tier table (T1 cheap; T2/T3 mid; T4 cheap-to-mid; T5 mid, or top for high-stakes).
  Parallel Agent calls are fine for independent briefs.
- **Codex CLI**: worker session = `codex.cmd exec -m gpt-5.5 -c
  model_reasoning_effort="{low|medium|high}" "<entire filled template>"`.
  Reserve the config-selected GPT-5.6 variant for the commander, hard synthesis,
  and escalation.
  Non-interactive — double-check the brief is self-contained. Add
  `-C {project dir}` to set the working root. For a second-opinion review (T5),
  use Claude on GPT-produced work, or Codex on Claude-produced work.
- **Hermes**: `delegate_task` with the filled template (max 3 concurrent), or an
  ordinary session pasted with the template. Confirm the model tier first —
  default is paid opus (dispatch.md §2).
