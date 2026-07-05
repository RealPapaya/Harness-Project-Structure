# Letter to Future Sessions
<!-- 中文摘要：創始 session（Fable 5, 2026-07-05）留下的交接信：三件使用者沒問但最重要的事、
     這套制度最可能的退化方式與預防、以及創始者自認信心最低的產出。讀一次即可。 -->

Written by the founding session (Fable 5, one-time access, 2026-07-05). Everything
here was true that day; re-verify anything load-bearing before relying on it.

## Three things Morris didn't ask about, but matter most

1. **The institution only works if it actually loads.** Claude Code auto-loads
   `~/.claude/CLAUDE.md` (mechanism verified). Codex loading `~/.codex/AGENTS.md`
   — VERIFIED 2026-07-05: a `codex exec` probe in an empty directory replied
   with the institution paths, which only exist in the global AGENTS.md.
   Hermes loads SOUL.md per message (per its own boilerplate — the one leg
   still unconfirmed by direct test).

2. **The scarce resource is specification, not implementation.** Cheap models
   implement well when acceptance criteria are explicit; they fail when asked to
   decide what "good" means. So spend the strong-model minutes (and Morris's own
   attention) writing briefs and acceptance criteria (templates.md), and let
   cheap tiers execute. A precise brief to a weak model beats a vague brief to a
   strong one, and costs less.

3. **Cross-family review is this machine's highest-leverage quality tool.**
   Claude-family and GPT-family models miss different things. For anything
   user-facing or irreversible, a Codex T5 review of Claude's work (or vice
   versa) is cheap insurance already wired up (lessons.md #cross-validate).
   Use it more than feels necessary.

## Most likely ways this institution decays — and the countermeasures

1. **Index drift** (the #1 risk): rules accrete in CLAUDE.md / AGENTS.md / SOUL.md
   because writing there is one step shorter than writing here. Three copies then
   diverge and the "single source of truth" quietly dies.
   Countermeasures already in place: hard 150-line caps, "if adding a rule here,
   stop" footer in each index, drift-check brief in maintenance.md §4. Run it.

2. **Memory non-writeback**: Claude keeps learning into per-project silos, Codex
   learns nothing (no memory), Hermes self-writes unreviewed notes — and
   lessons.md starves. Countermeasure: maintenance.md §2 routing rules; the tell
   is lessons.md's last-entry date falling months behind actual work.

3. **Fact rot**: model ids, CLI flags, and prices in these files were verified
   2026-07-05 and will silently go stale. Countermeasure: dated "verified" stamps
   everywhere; when a documented fact fails in practice, fix the institution file
   in the same session, not just the immediate problem.

## Founding session's lowest-confidence outputs (honesty clause)

- ~~Codex global AGENTS.md path~~ — RESOLVED: verified loading 2026-07-05
  (see item 1 above).
- **Codex multi_agent**: the feature flag reads stable/true but I never saw its
  invocation surface; dispatch.md §2 deliberately routes around it via
  `codex exec`. If native subagents turn out to be usable, update dispatch.md.
- **Hermes specifics**: home-dir location (`AppData\Local\hermes` here vs `~/.hermes`
  in its source), whether the SOUL.md routing block survives app updates, and
  `delegate_task` behavior — all lightly verified at best, because Morris rarely
  uses Hermes. Acceptable risk; the institution treats Hermes as lowest priority.
- **Effort parameter values**: Codex `model_reasoning_effort` only "high" is
  verified on this install; Claude `/effort` levels observed: medium/high/ultracode.
  Others = convention.
- **Opus 4.8 rerouting quota**: whether safety-rerouted requests consume the
  Fable window's quota could not be verified locally. 建議到 claude.ai 的 usage
  頁實測。

## Open items at founding (none blocking)
- Hermes cheap-tier model id: 待使用者填寫 in core-rules.md §2 + dispatch.md §2.
- Codex `approval_policy` still deprecated `on-failure`; recommended `on-request`,
  change requires user sign-off (maintenance.md §1).
- Project instruction files not yet carrying the 2-line routing snippet
  (INDEX.md has it; paste when next working in each project).
