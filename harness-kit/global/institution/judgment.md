# Judgment Rubrics — externalized decision rules
<!-- 中文摘要：把強模型的判斷寫成弱模型可執行的 rubric。五組：何時升級、何時算完成、
     何時停下問使用者、方向錯的訊號、品質底線驗證。每條附一正例一反例。harness 中立。 -->

Apply these mechanically. If a rubric's conditions are met, take the action even
if you "feel" otherwise — the rubric encodes a stronger model's judgment.

## §Escalate — when to move up a model tier
Escalate when ANY of:
- E1. The task requires holding 3+ interacting constraints at once
  (e.g. API contract + backward compat + performance budget).
- E2. You have produced an answer but cannot explain WHY it is correct in two
  sentences that reference evidence.
- E3. The ladder in dispatch.md §4 says so (one cheap failure / two mid failures).
- E4. The output will be hard to reverse or the user will act on it directly.
Do NOT escalate for: volume (more files ≠ harder), tedium, or vague unease with
an easy task — those are cheap-tier work by definition.

✔ Example: "Refactor auth middleware so sessions stay valid across the Cloudflare
  migration while Supabase auth still works" — 3+ interacting constraints → mid
  tier minimum; escalate to top on first real failure.
✘ Counter-example: "Rename `getUser` to `fetchUser` across 40 files" — big volume,
  zero interacting constraints → cheap tier with a verification pass. Escalating
  this wastes budget.

## §Done — when a task is truly complete
ALL must hold:
- D1. Every acceptance criterion from the brief is checked, each with evidence
  (command output, fresh-agent read-back answer, or observed app behavior).
- D2. Verification was performed per dispatch.md §5 by someone other than the writer.
- D3. Nothing was silently skipped — every skipped/failed item is listed in the report.
- D4. Durable artifacts are saved to disk (not only described in conversation).
If any fails, the status is "in progress with blockers", never "done".

✔ Example: "Fixed the reconnect bug. Evidence: `npm test` 34/34 pass (last 10 lines
  attached); fresh session confirmed server.ts:112 handles the timeout branch;
  no skipped cases."
✘ Counter-example: "I've updated the code and it should work now." — no criteria
  checked, no evidence, writer self-certified. This is NOT done; saying "done"
  here is the failure mode diagnosis.md D3.

## §Ask — when to stop and ask the user
Stop and ask before ANY of:
- A1. Deleting or overwriting anything you did not create this session.
- A2. Actions that are hard to reverse (force-push, dropping data, sending
  anything outside the machine, changing harness config files).
- A3. Spending real money (paid APIs, paid model tiers beyond the brief).
- A4. The task has become a taste/ambiguity call: two defensible options whose
  choice depends on preferences the user has not stated. Decomposition and
  verification cannot fix taste — do not fake confidence; present both options
  with a recommendation.
- A5. Scope change: the correct fix requires touching things outside the brief.
Do NOT ask for: choices with an obvious conventional default, facts you can
verify yourself, or permission to do exactly what was already asked.

✔ Example: "The bug fix requires changing the DB schema (outside the brief).
  Options: A) migration now, B) shim + migrate later. Recommend A because…
  Which do you want?" — scope change + defensible fork → ask.
✘ Counter-example: "Should I run the tests now?" after being told to fix and
  verify a bug — verification was already part of the task. Just run them.

## §Wrong-direction — signals to change approach instead of retrying
If TWO or more of these hold, the approach is wrong — do not retry it:
- W1. Two distinct fixes for the same symptom have failed (dispatch.md §4 cap).
- W2. Each fix creates a new error somewhere previously healthy.
- W3. The fix keeps growing: you now need to touch many files unrelated to the
  original symptom.
- W4. You cannot explain why the LAST attempt failed — you are guessing, not
  debugging.
Action: write down (a) what you believe the root cause is and (b) the evidence.
If you can name a genuinely different approach, take it once. Otherwise stop and
report per §Ask with the failure trail.

✔ Example: after two failed CSS fixes for a layout bug, you notice each fix breaks
  a different page (W1+W2) → stop patching; investigate the shared layout
  component instead — root cause, different layer.
✘ Counter-example: third retry of the same idea with small variations, "maybe
  this tweak will do it" — that is W4 exactly; the rubric forbids the third round.

## §Quality-floor — minimum verification by artifact type
- Code change → run the narrowest real check that exercises the change (unit
  tests, build, or launch the app); "it compiles in my head" counts as nothing.
- Config/instruction/doc file → fresh-context read-back against acceptance
  criteria, item by item.
- Batch mechanical change → verify a random sample of 3 instances + count the
  total ("40 files expected, 40 files changed").
- Research/answers → every load-bearing claim has a source you actually opened;
  claims you could not verify are explicitly marked unverified.
- Anything user-facing or irreversible → second opinion (different model family
  if available) before shipping.

✔ Example: batch rename across 40 files → `rg` count shows 0 old-name hits,
  40 files touched, spot-check 3 diffs — pass.
✘ Counter-example: "The refactor is straightforward, so I skipped the tests." —
  straightforwardness is an opinion; the floor is unconditional.
