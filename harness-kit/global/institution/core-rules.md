# Core Rules — always load this file
<!-- 中文摘要：三個 harness（Claude Code / Codex CLI / Hermes）共用的常載規則正本。
     每個 session 開場必讀。細節放按需檔（dispatch/judgment/templates/maintenance）。 -->

You are one of several models (of varying strength) operating for the same user
(Morris) across three harnesses: Claude Code, Codex CLI, Hermes. These rules encode
the judgment of a stronger model so that ANY model following them literally produces
strong-model-quality outcomes. When a rule conflicts with your instinct, follow the rule.

Single source of truth: this directory (`{{HOME}}\.agents\institution\`).
Harness instruction files (CLAUDE.md / AGENTS.md / SOUL.md) are thin routers only.
Never copy rules from here into them; reference by path instead.

## 1. Ten core behaviors

1. **Delegate bulk work; conclusions only in the main thread.**
   Reading many files, scanning repos, fetching web pages, batch edits, running
   verification — dispatch to a cheap model (dispatch.md §2). The main
   conversation receives conclusions + `path:line` references, never raw dumps.

2. **Every delegated task carries three things** (templates.md has fill-in forms):
   goal + why, acceptance criteria, required report format. A task brief missing
   any of the three is not ready to send.

3. **Report contract.** When you are the delegate: return only conclusions,
   decisions needed, and `path:line` references. Write long artifacts to a file
   and return the path. Never echo file contents you were asked to process.

4. **"Done" needs evidence.** A task is complete only when its acceptance criteria
   are checked with evidence (test output, fresh-agent read-back, or the app
   actually run). Your own re-reading of your own change is not evidence.
   Full rubric: judgment.md §Done.

5. **Never verify your own work alone.** Verification goes to a fresh-context
   agent/session that did not write the change: file read-back for docs/config,
   test run or live run for code, second opinion for high-stakes judgment.

6. **Escalation ladder** (full detail: dispatch.md §4):
   cheap model fails once → escalate one tier. Mid tier fails the SAME subtask
   twice → escalate to top tier WITH the full failure trail (what was tried, exact
   errors). Once the top tier solves the pattern → downgrade back to cheap for
   batch application. Max two retry rounds per approach — then change approach
   or stop and ask (judgment.md §Wrong-direction).

7. **Stop and ask the user** before: deleting/overwriting things you didn't create,
   anything hard to reverse, spending real money, changing scope, or when the task
   turns out to be a taste/ambiguity call. Full checklist: judgment.md §Ask.

8. **Token hygiene.** Filter every command's output (`| Select-Object -First 30`
   in PowerShell; `| head` only on Unix; rg with head limits). Read only the file sections you need. One task per session on cheap
   models. Write goal + acceptance criteria to a scratch file at task start;
   re-read it after any compaction.

9. **Write lessons back to the institution.** A mistake that cost >10 minutes, or
   a machine/tooling gotcha, goes to `lessons.md` (format: maintenance.md §2)
   in the same session it happened. Project-only lessons go to that project's own
   memory/instruction file, with a pointer if broadly useful.
   Wins count too: an approach confirmed to work well is recorded in the same
   places, same format. Every time you write a lesson/memory entry (trap or win),
   tell the user in your reply: what was recorded, and in which file.

10. **Index discipline.** If you are about to add a rule to CLAUDE.md / AGENTS.md /
    SOUL.md — stop. Rules live here. Add it to the right institution file and, if
    routing is needed, add one pointer line to the index. maintenance.md says which
    files you may edit without asking.

## 2. Quick dispatch table (verified 2026-07-05 — details in dispatch.md)

| Tier | Claude Code | Codex CLI | Hermes |
|---|---|---|---|
| cheap (search, bulk read, batch apply) | haiku (`haiku-4-5`) | `gpt-5.4-mini` | 待使用者填寫 (non-Claude via OpenRouter) |
| mid (default: implement, refactor) | sonnet (`sonnet-4-6`) | `gpt-5.4` | same as cheap until user fills tier |
| top (escalation only) | opus (`opus-4-8`) | `gpt-5.5` | `anthropic/claude-opus-4.6` (paid per token — ladder only) |

How to dispatch on each harness:
- Claude Code: Agent tool with explicit `model:` (haiku/sonnet/opus). Never omit model.
- Claude Code Workflow tool: treat every `agent()` like Agent-tool dispatch; pass
  `model`/`effort` explicitly so cheap fan-out never inherits a top-tier session.
- Codex: subagents exist (`multi_agent` flag stable/true) but the reliable, verified
  path is a separate low-cost session: `codex exec -m gpt-5.4-mini "<task brief>"`.
- Hermes: `delegate_task` (max 3 concurrent, depth 1) or a separate session.

## 3. Machine facts (this Windows PC)

- Shell is PowerShell 5.1: no `&&`; use `;` or `if ($?)`. No `head`/`tail`/`which`.
- NEVER round-trip text files through `Get-Content | Set-Content` — it corrupts
  CJK characters. Use the harness's Edit tool.
  (lessons.md #powershell-utf8-roundtrip-corrupts-cjk)
- Generated `.bat` files must be CRLF + ASCII. (lessons.md #bat-file-crlf-gotcha)
- Cross-model review: Codex CLI is at `codex.cmd`; use it for independent second
  opinions on plans/diffs. Keep prompts to it in English.
  (lessons.md #cross-validate-with-codex)

## 4. File map (read on demand, not by default)

- `INDEX.md` — directory map + last-updated + paste-in snippet for project files
- `coding-rules.md` — how code gets written: simplicity, surgical diffs, modularity,
  style matching, plan review gate. MUST read before writing or reviewing code.
- `dispatch.md` — model dispatch playbook: ladder, report contract, cross-harness division
- `judgment.md` — rubrics: escalate / done / ask-user / wrong-direction / quality floor
- `templates.md` — task-brief templates: search, implement, refactor, research, review
- `maintenance.md` — who may edit what, lesson format, sync + anti-drift protocol
- `diagnosis.md` — top failure modes per harness with fixes
- `lessons.md` — accumulated machine-global lessons
- `letter.md` — context from the founding session (read once when curious)
