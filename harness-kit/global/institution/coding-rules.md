# Coding Rules — how code gets written, on every harness
<!-- 中文摘要：工匠層。寫程式的六條紀律＋計畫外審關卡：先想再寫、能簡則簡、外科手術式改動、
     模組化鐵則、風格統一、目標驅動。每條附正反例。寫/改/審 code 前必讀。 -->

Trade-off note: these rules bias toward caution over speed. For genuinely trivial
tasks (one-line fix with an obvious test), apply §3 and §6 and skip the ceremony.

## §1. Think before coding — surface assumptions, don't bury them
- State your assumptions explicitly before implementing. If multiple
  interpretations exist, name them — never pick one silently.
- If a simpler approach than the requested one exists, say so before building.
- Interactive session + the choice is taste/irreversible → ask per judgment.md §Ask.
- NON-INTERACTIVE delegate (e.g. a `codex exec` run — it cannot ask anything):
  write every assumption into the report AND pick the most reversible option.

✔ "Assuming 'user list' means paginated API users, not the admin table. If you
  meant admins, say so — the change is isolated in api/users.ts either way."
✘ Silently implementing one interpretation and mentioning nothing. The user
  discovers the mismatch after integration — most expensive possible moment.

## §2. Simplicity first — minimum code that solves the stated problem
- No features beyond what was asked. No abstractions for single-use code.
- No "flexibility"/"configurability" nobody requested. No error handling for
  impossible states.
- The test: "Would a senior engineer call this overcomplicated?" If yes, rewrite.
  If you wrote 200 lines and 50 would do, the 50 is the deliverable.

✔ Task "cache the API response": a Map + timestamp check, 15 lines, in the module
  that calls the API.
✘ Same task: a generic CacheProvider interface + TTL strategy classes + config
  file, 220 lines. Three abstractions, one caller. Delete and rewrite.

## §3. Surgical changes — every changed line traces to the request
- Don't "improve" adjacent code, comments, or formatting. Don't refactor what
  isn't broken. Match existing style even where you'd personally differ.
- Notice unrelated dead code → mention it in the report; do NOT delete it.
- Clean up only your own mess: remove imports/vars/functions that YOUR change
  orphaned; leave pre-existing orphans alone unless asked.
- Mechanical and semantic changes ship separately: a rename/reformat never rides
  in the same commit/diff as a logic change (reviewers can't see the logic
  through the noise).

✔ Bug fix touches 3 lines in one function; diff is 3 lines.
✘ Bug fix arrives with 40 reformatted lines, 2 renamed variables, and a "while I
  was here" refactor. The reviewer must now audit 45 lines to trust 3.

## §4. Modularity — new behavior lives in new modules
- New feature/effect/handler = its own file with a narrow, explicit interface.
- Shared files receive ONLY the injection line (registry entry, route line,
  import) — never the logic itself.
- A module that needs to know another module's internals is one module, not two —
  either merge them or narrow the interface.

✔ New vote feature → `voteEvents.ts` exporting one register function; shared
  `events.ts` gains exactly one registration line.
✘ New vote feature spread as case-branches across three shared files. Next
  feature does the same, and the shared files become merge-conflict magnets.

## §5. Style unification — the codebase should read as one author
- Before writing, read 2 neighboring files; copy their naming, comment density,
  error-handling idiom, and import style.
- If the project has a formatter/linter config, run it on exactly the files you
  touched — nothing wider.
- Uncertain between two idioms → grep which one the codebase already uses more;
  follow the majority. Never introduce a third.

✔ Neighbors use `async/await` + early returns → your code does too, even if you
  prefer promise chains.
✘ The one file written in a different style, with a different naming scheme,
  because the delegate had a habit. Habits lose to neighbors.

## §6. Goal-driven execution — verifiable criteria before code
Transform every task into checkable goals BEFORE writing code:
- "Add validation" → "write tests for the invalid inputs, make them pass"
- "Fix the bug" → "write a test that reproduces it, then make it pass"
- "Refactor X" → "tests pass before AND after, diff shows structure-only change"
For multi-step work, state the plan as `step → verify:` pairs:
```
1. [step] → verify: [command / observable check]
2. [step] → verify: [...]
```
Weak criteria ("make it work") are not executable — push back and get real ones,
or derive them and state them as assumptions (§1). Completion itself is judged
by judgment.md §Done — the verify steps here are what feed it evidence.

## §7. Plan review gate — plans get cross-reviewed before implementation
Trigger — a plan meets ANY of:
- touches more than 3 files, or any shared/core module;
- introduces a dependency, schema change, or anything hard to reverse;
- the author's confidence is not high.
Then, BEFORE implementation:
1. Send the plan to a DIFFERENT model family for review (templates.md T5 —
   Claude-family work goes to Codex, and vice versa; see dispatch.md §6).
2. The reviewer answers: simpler alternative? hidden coupling? missing
   verify-step? wrong assumption?
3. Findings are resolved or explicitly accepted (with reason) before code is
   written. The author never green-lights their own plan.

✔ 5-file plan → T5 review catches that two files needn't change if the registry
  pattern (§4) is used → 2-file plan implemented.
✘ "The plan is straightforward, skipping review" on a 6-file change — that
  sentence is the trigger firing, not an exemption.
