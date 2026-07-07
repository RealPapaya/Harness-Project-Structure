# Planning Playbook — how a decision-maker turns a request into a buildable plan
<!-- 中文摘要：終局決策者的思考迴路。coding-rules 管「怎麼寫 code」，這份管「code 還不存在之前，
     計畫怎麼被做出來」：偵察找錨點 → 先定資料合約 → 證據導向評分 → step→verify → 跨家族外審 →
     留下可複製軌跡。規劃新功能、或任務形狀未知時必讀。本檔永遠專案無關；
     實例＝各專案 repo 裡由 P7 留下的 PLAN 檔，不進正本。 -->

Purpose: coding-rules.md governs how code gets written; this file governs how a
plan gets made BEFORE any code exists. Work the phases in order; each has a hard
exit condition. A weaker model that satisfies every exit condition produces a
strong-model-quality plan — that is the point of writing them down.

This file is project-agnostic by design: nothing below names any project, and
nothing project-specific may ever be added here. Concrete instances of this
loop are the PLAN files that P7 leaves behind inside each project's own repo —
when planning in a repo that already has one, read it first: it is a worked
example AND your ready-made pattern anchor.

## P1. Frame — write the decision record before any tool call
Open the plan file FIRST and write:
- Goal in one sentence, plus why the user wants it (the why steers trade-offs).
- In scope / out of scope — two explicit lists.
- Assumptions, each on its own line marked `[assumption]` (coding-rules §1:
  silent assumptions are the most expensive planning failure).
Exit: a reader could reconstruct the request from your frame alone, without
seeing the original prompt.

## P2. Recon — evidence before opinion
1. Load the rule stack in order: institution core-rules → the target project's
   AGENTS.md / CLAUDE.md → its LESSONS.md. Rules read after designing are wasted.
2. Find the PATTERN ANCHOR: the closest already-shipped feature in the target
   codebase with the same shape as the new one. Never design from a blank page —
   an anchor turns "design a system" into "instantiate a proven wiring".
3. Trace the anchor end-to-end and record the chain as `path:line`:
   producer module → data contract/schema → consumer → registration point.
4. Record the binding constraints you found: dependency policy, style anchors,
   which shared files accept only injection lines, test entry points.
Exit: you can name the anchor and its full wiring chain with `path:line`.
If NO anchor exists, say so in the plan — that alone drops confidence to
"not high" and triggers the coding-rules §7 review gate.

## P3. Contract first — define the data before the internals
Write the exact output shape (dict / JSON / interface) the new module will
produce — field names, types, value ranges — before designing any algorithm.
- The contract is what every other file consumes; internals stay swappable.
- Degrade gracefully: absence of the new data must not break existing
  consumers. Copy the anchor's idiom (optional fields, try/except at the
  injection point, `|| default` on the reader side).
Exit: the contract is pasted into the plan and every consumer in the file-touch
list reads only fields that exist in it.

## P4. Rubric rules — for any evaluative feature (score / health / rating / rank)
- Signals must be cheaply observable from artifacts you already have (files,
  configs, git metadata). No signal that needs a network call or an LLM in v1.
- Every dimension carries EVIDENCE — a list of `{path, signal}` the user can
  click — never a bare number. Unexplainable scores are unfixable scores.
- All weights live in ONE table in ONE file so tuning never touches the pipeline.
- Name calibration targets BEFORE coding: 2–3 known inputs and the score band
  each must land in. A rubric with no calibration targets cannot be wrong,
  therefore cannot be right.
- Define the level bands (e.g. 0–2 ad-hoc … 8–10 self-improving) together with
  the rubric, not in the UI later.

## P5. Exceed the prompt in the plan, never in the diff
List the adjacent wins the same wiring makes cheap (extra dimensions, trends,
recommendations, an API/tool exposure) as OPTIONS, each labeled `v1` or
`defer`, with one line of cost. The user picks; you do not silently build
(coding-rules §2). Ambition belongs in the option list; the diff implements
exactly the approved subset.

## P6. The plan artifact — step → verify pairs
The plan file must contain:
- A file-touch table: every file, new-or-edit, and for SHARED files the exact
  number of injected lines (coding-rules §4: logic in new modules, shared files
  get one-line registrations).
- Steps as `step → verify:` pairs where verify is a runnable command or an
  observable check (coding-rules §6). A step you cannot verify is not a step.
- Risks with mitigations, one line each.
Exit: someone else could implement the plan without asking you anything.

## P7. Gate, then leave the trail
- If the §7 trigger fires (>3 files, shared/core module, hard-to-reverse,
  confidence not high) → cross-family T5 review (templates.md) BEFORE code.
  Log findings and their resolution IN the plan file ("Review log" section);
  accepted-with-reason is a valid resolution, silence is not.
- After implementation: judgment.md §Done evidence, lessons appended in the
  same session, plan file kept in the repo. The kept plan is the next feature's
  pattern anchor — that is how quality compounds.

## Anti-patterns (each voids the phase it belongs to)
✘ Designing the feature, then skimming the codebase for confirmation (P2 backwards).
✘ A score whose breakdown cannot point to the files that caused it (P4).
✘ "I also added X while I was there" — ambition leaked into the diff (P5).
✘ A plan step like "wire it up" with no verify command (P6).
✘ Approving your own plan because the review feels like ceremony (P7).
