# Harness Kit — portable agent institution
<!-- 中文摘要：這個資料夾是「制度正本 master」，可整包帶到任何電腦。
     global\ 裝一次（全域生效），project-template\ 每個專案套一次。
     部署方法：把 global\INSTALL.md 丟給該電腦上任何一個 AI agent 照做。 -->

One canon, thin adapters. This kit makes every AI coding session — on Claude
Code, Codex CLI, or Hermes, on any machine — follow the same externalized
judgment: cheap-model dispatch, cross-family plan review, evidence-based "done",
surgical minimal-diff code, and a compounding lessons loop.

## Layout
```
harness-kit\
├─ README.md                ← you are here
├─ global\
│  ├─ INSTALL.md            ← give this file to any agent on a new machine
│  ├─ institution\          ← the 10 canon files ({{HOME}} placeholders inside)
│  └─ indexes\              ← thin-index templates for the 3 harnesses
└─ project-template\
   ├─ APPLY.md              ← give this to an agent to wire up one project
   ├─ AGENTS.md             ← thin project index (Codex + Hermes + Claude read it)
   ├─ CLAUDE.md             ← thin project index (Claude; fill the blanks)
   └─ LESSONS.md            ← per-project lessons ledger (compounding loop)
```

## Deploy on a new machine (once)
Open any AI agent on that machine and say:
"Read and execute D:\...\harness-kit\global\INSTALL.md" (adjust to where this
kit lives). The agent copies the institution to `<HOME>\.agents\institution\`,
replaces `{{HOME}}` with the real home path, installs the three thin indexes,
and verifies. Takes minutes; a cheap model can do it.

## Wire up a project (once per repo)
Say: "Read and execute project-template\APPLY.md for <project path>."
Existing CLAUDE.md/AGENTS.md are merged, never overwritten (backups made).

## Rules of this kit
- This kit is the MASTER. Deployed copies must sync back here in the same
  session they are edited (institution maintenance.md §6).
- Kit institution files contain `{{HOME}}` placeholders; deployed copies have
  the machine's absolute home path. That substitution is the ONLY allowed
  difference between kit and deployment.
- Never edit `global\institution\` files casually — they load into every
  session on three harnesses. maintenance.md §1 says what needs user sign-off.
