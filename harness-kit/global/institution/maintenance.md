# Maintenance & Sync Protocol
<!-- 中文摘要：哪些檔可自行改、哪些要先問；教訓怎麼寫回；多長要精簡；
     三個 harness 的記憶如何回寫同一正本；索引檔防漂移檢查。改任何指令/記憶檔前必讀。 -->

## 1. Edit permissions
May edit WITHOUT asking (weak models included):
- `lessons.md` — append new lessons; consolidate when over the cap (§3).
- `diagnosis.md` — append a newly observed failure mode with its fix.
- Project-level memory (Claude `memory/` dirs, project CLAUDE.md/AGENTS.md bodies).
- `INDEX.md` "Last updated" line, and adding a row to its on-demand table when a
  new institution file is added.

ASK THE USER first:
- `core-rules.md` — any change (it loads into every session on three harnesses).
- The three harness index files beyond adding a single pointer line.
- Harness configs: `~/.codex/config.toml`, Hermes `config.yaml`,
  `~/.claude/settings.json`.
- Deleting or rewriting (not appending) any institution file.
- Anything in judgment.md / dispatch.md that changes a threshold or a tier.

Always: before modifying an EXISTING file here or an index file, copy it to
`<name>.bak-YYYYMMDD` in the same directory first.

## 2. Lesson write-back (all three harnesses → one file)
Machine-global lessons (tooling gotchas, cross-project workflow fixes, model
behavior discoveries) go to `lessons.md`, appended in this format:

```markdown
## <kebab-case-slug>
- What: <the fact in 1-2 lines>
- Why it matters: <cost of not knowing>
- How to apply: <literal instruction a weak model can follow>
- Origin: <harness, date, project>
```

Harness-specific routing:
- **Claude Code**: auto-memory also writes to `~/.claude/projects/<slug>/memory/`.
  If a lesson is machine-global: full text → `lessons.md`; the project memory file
  keeps ONE line: "Promoted to institution lessons.md #<slug>".
- **Codex**: has no memory — write to `lessons.md` directly before session end.
- **Hermes**: its MEMORY.md auto-writes are cache only; anything durable is
  copied to `lessons.md` in the same session.
Project-specific lessons stay in that project's instruction/memory file.

## 3. Size caps and pruning
- Harness index files: ≤150 lines each, routing only.
- Always-load canonical files (`core-rules.md` + `INDEX.md`): ≤500 lines combined.
- `lessons.md`: when it exceeds ~200 lines, consolidate — merge duplicates,
  delete lessons obsoleted by tool changes, move rare-but-keepable detail into a
  dated archive file (`lessons-archive-YYYY.md`) with one pointer line left.
- Any on-demand file exceeding ~150 lines: split by topic and update INDEX.md.

## 4. Anti-drift checks (the institution's main failure risk)
Run this check when: a month has passed (see INDEX.md "Last updated"), OR you
notice a rule stated in a harness file, OR at the start of any session that will
edit instruction files. Cheap-model task; use this brief:

```
GOAL: Drift check on the agent institution.
DO: Read {{HOME}}\.claude\CLAUDE.md, {{HOME}}\.codex\AGENTS.md,
    {{HOME}}\AppData\Local\hermes\SOUL.md, and institution INDEX.md +
    core-rules.md.
ACCEPTANCE: for each index file answer: (1) does it still point to
    {{HOME}}\.agents\institution\ ? (2) is it ≤150 lines?
    (3) does it contain any RULE that also exists (or belongs) in the
    institution? (4) do all referenced institution files exist?
REPORT: per-file PASS/FAIL per item; for each FAIL, the exact offending lines.
```
Fix drift by MOVING content into the institution (never by copying it around),
then re-run the check.

## 5. When the user edits things by hand
Hand edits are authoritative. If a hand edit contradicts an institution rule,
do not "fix" it back — surface the contradiction to the user and ask which
version wins, then make the institution match the answer.

## 6. Master kit sync (portability)
The portable MASTER of this institution lives at
`{{KIT}}\`. Kit copies replace
machine-specific absolute paths with placeholder tokens (defined in the kit's
INSTALL.md); deployed files carry this machine's real paths. That substitution
is the ONLY allowed difference. This deployed directory is a copy.
- Any edit to a deployed institution file must be mirrored to the kit in the
  SAME session (and vice versa), preserving the placeholder difference.
- Drift check between kit and deployment: compare file lists and content
  (ignoring the placeholder-vs-real-path difference); anything else is drift —
  fix by copying the newer content both ways, never by letting them diverge.
- If a third-party skill in any harness's skills directory duplicates content
  that now lives in this institution (notably coding-guideline skills), the
  institution wins on conflict; removing the duplicate skill is recommended.
- New machine setup: run the brief in
  `{{KIT}}\global\INSTALL.md`.
