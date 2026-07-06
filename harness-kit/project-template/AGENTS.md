# Project instructions — {{PROJECT_NAME}}
<!-- 中文摘要：本專案的單一指令正本（Claude/Codex/Hermes 三邊都會讀到這份或被路由到這份）。
     上半部是固定協議（別改），下半部是本專案填空區。 -->

Global rules: read `{{HOME}}\.agents\institution\core-rules.md` first.
Everything below is project-specific and adds to (never overrides) those rules.
If a harness-level global index was not read yet, read it before continuing.

## Session protocol (fixed — do not edit)
1. **Before any work**: read `LESSONS.md` in this directory. It contains this
   project's known traps; repeating a documented mistake is the one
   unacceptable failure.
2. **Before writing code**: apply institution `coding-rules.md` — surgical
   minimal diffs (§3), new behavior in new modules (§4), match neighboring
   style (§5), verifiable criteria before code (§6).
3. **Plan review gate** (coding-rules.md §7): a plan touching >3 files, any
   shared/core module, anything hard to reverse — or whose author's confidence
   is not high — gets reviewed by a DIFFERENT model family (templates.md T5)
   BEFORE implementation. The author never green-lights their own plan.
4. **Before claiming done**: institution judgment.md §Done — evidence, verified
   by someone other than the writer (dispatch.md §5).
5. **After any mistake, surprise, or confirmed win**: append it to `LESSONS.md`
   NOW (format inside), in the same session, and tell the user what was
   recorded. Machine-global lessons also go to
   `{{HOME}}\.agents\institution\lessons.md`.

## Project specifics (fill in; keep each line true or delete it)
- What this project is: {{ONE_LINE_DESCRIPTION}}
- Build: `{{BUILD_COMMAND}}`
- Test: `{{TEST_COMMAND}}` (this is the §6 safety net — keep it current)
- Run locally: `{{RUN_COMMAND}}`
- Module convention: new features go in `{{MODULES_DIR}}` as standalone modules;
  shared files ({{SHARED_FILES}}) receive only registration/injection lines.
- Do NOT touch without asking: {{FORBIDDEN_PATHS}}
- Style anchors (read these before writing code): {{TWO_REPRESENTATIVE_FILES}}

## Rules for this file
Keep ≤150 lines. Durable knowledge goes to LESSONS.md (traps) or the
institution (universal rules) — not here.
