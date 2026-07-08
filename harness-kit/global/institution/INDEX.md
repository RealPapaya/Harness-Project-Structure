# Institution Index
<!-- 中文摘要：共用正本的目錄地圖。三個 harness 的薄索引都路由到這裡。
     Last updated 行由每次修改正本的 session 更新。 -->

Last updated: 2026-07-07 (added planning-playbook.md — the project-agnostic
decision-maker loop for turning a request into a buildable plan; its worked
instances are the PLAN files each project keeps in its own repo)

This directory is the single source of truth for how AI agents work for Morris
across Claude Code, Codex CLI, and Hermes. Harness instruction files are thin
routers pointing here. If you find a rule duplicated in a harness file, that is
drift — fix per maintenance.md §4 (Anti-drift checks).

## Always load
1. `core-rules.md` — the ten core behaviors + quick dispatch table + machine facts

## Load on demand (when the trigger fires)
| File | Read when… |
|---|---|
| `coding-rules.md` | you are about to write, modify, or review code, or approve a plan |
| `planning-playbook.md` | you are planning a new feature, or the task's shape is unknown — the P1–P8 decision loop |
| `dispatch.md` | you are about to delegate work, pick a model, or escalate/downgrade |
| `judgment.md` | you are about to claim done, escalate, retry, or ask the user |
| `templates.md` | you are writing a task brief for another model/session |
| `maintenance.md` | you are about to edit any instruction/memory/institution file |
| `diagnosis.md` | the session feels wasteful, drifting, or error-prone |
| `lessons.md` | you hit a machine/tooling error, or before batch file operations |
| `letter.md` | once, if you want the founding session's context and caveats |

## Harness thin indexes (routers → here)
- Claude Code: `{{HOME}}\.claude\CLAUDE.md`
- Codex CLI:  `{{HOME}}\.codex\AGENTS.md`
- Hermes:     `{{HOME}}\AppData\Local\hermes\SOUL.md` (routing block at bottom)

## Paste-in snippet for project instruction files
Add these 2 lines at the TOP of any project `CLAUDE.md` or `AGENTS.md`
(one file can serve Codex + Hermes + Claude in the same repo):

```
Global rules: read {{HOME}}\.agents\institution\core-rules.md first.
Everything below is project-specific and adds to (never overrides) those rules.
```
