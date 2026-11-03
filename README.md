# Korent Harness

My whole agentic coding setup on one page. One Windows PC, Claude Code on three subscription accounts, an Obsidian vault as long term memory, and a few scheduled jobs that keep working while I sleep.

This is not a framework you install. It is my setup, glued to my machine. The public parts you can take and use. The private parts are listed so you see the whole picture. 🔒 means the repo is private.

## How it fits together

```mermaid
%%{init: {'theme':'base','themeVariables':{'background':'#0d1117','primaryColor':'#161b22','primaryTextColor':'#e6edf3','primaryBorderColor':'#30363d','lineColor':'#8b949e','secondaryColor':'#161b22','tertiaryColor':'#0d1117','clusterBkg':'#0d1117','clusterBorder':'#30363d','titleColor':'#e6edf3','edgeLabelBackground':'#0d1117','fontFamily':'ui-monospace, SFMono-Regular, monospace'}}}%%
flowchart TB
  subgraph IN["Input"]
    direction LR
    ME([Maj]):::me
    VOICE["FreeFlow dictation"]
    NB["Notion Notebook<br/>one capture page"]
  end

  subgraph CC["Claude Code · 3 subscription accounts rotated by cswap"]
    direction LR
    SESS["Interactive sessions<br/>CLI · desktop · VS Code"]
    HEAD["Headless head<br/>orchestrators"]
    SUB["Subagents<br/>Explore · implementer · reviewer"]
    HOOKS["Hooks<br/>file-claim lock · mnemosyne trace"]
  end

  subgraph VAULT["MajVault · Obsidian · PARA"]
    direction LR
    PROJ["Project CLAUDE.md<br/>roadmap + decisions"]
    TRACES["Agent traces<br/>by project"]
    DIST["backfill + distill<br/>hourly, Jev classifies"]:::ai
  end

  subgraph SCHED["Windows Task Scheduler"]
    direction LR
    NIGHT["autonight<br/>every 30 min"]
    BRIEF["majbrief<br/>05:00 daily"]
    DB[("hq.db<br/>task queue")]
  end

  subgraph OUT["Out"]
    direction LR
    GH["GitHub<br/>branches · PRs"]:::ext
    MAIL["Inbox"]:::ext
    HQ["HQ dashboard + session viewer"]:::ext
    PHONE["Phone<br/>Syncthing"]:::ext
  end

  ME --> SESS
  VOICE --> SESS
  ME --> NB
  SESS -- big work --> HEAD
  HEAD --> SUB
  SESS --> SUB
  SESS --> HOOKS

  PROJ -- read before work --> SESS
  HOOKS -- trace on exit --> TRACES
  TRACES --> DIST
  DIST -- update --> PROJ

  NIGHT --> DB
  DB --> HEAD
  HEAD --> GH
  NB --> BRIEF
  PROJ --> BRIEF
  BRIEF --> MAIL
  DB --> HQ
  SESS --> HQ
  VAULT --> PHONE

  classDef me fill:#1f6feb,stroke:#58a6ff,color:#ffffff
  classDef ai fill:#2a1a3d,stroke:#a371f7,color:#e2c5ff
  classDef ext fill:#0f2a1a,stroke:#3fb950,color:#aff5b4
```

**The loop in one paragraph.** Every session starts by reading the project's `CLAUDE.md` in the vault, so it knows what earlier sessions decided. While it works, a hook locks the files it edits, so two parallel agents never write the same file. When it ends, another hook writes a trace of what it did into the vault. Every hour those traces get classified and distilled back into the project's `CLAUDE.md`. The next session starts smarter. At night autonight picks tasks from `hq.db` and runs them on whichever account still has quota. At 05:00 a brief lands in my inbox.

## The parts

### Memory

| Part | What it does | Repo |
|---|---|---|
| MajVault | Obsidian vault in PARA folders. Each project has a `CLAUDE.md` that is its memory. ~3700 agent traces so far. | 🔒 private, it is my brain |
| Obsidian Tracehold | The vault pattern as a template: read before working, write a trace after. Works with Claude Code, Codex, Copilot. | [korentmaj/obsidian-tracehold](https://github.com/korentmaj/obsidian-tracehold) |
| mnemosyne | Exports session transcripts. My fork adds `vault trace`, which the SessionEnd hook calls. | [korentmaj/mnemosyne](https://github.com/korentmaj/mnemosyne) (fork of AletheiaResearch) |
| Notion Notebook | One page where I dump everything. Every 30 min Jev sorts each line into Task, Event, Note or Idea. | 🔒 private |

### Running agents

| Part | What it does | Repo |
|---|---|---|
| cswap (claude-swap) | Three Claude subscriptions, rotated by 5 hour and 7 day usage. Big work burns the other accounts first. | third party |
| autonight | Night coding engine. Task Scheduler calls `dispatch.mjs` every 30 min, it picks a task from `hq.db` and runs it. | 🔒 private |
| Head orchestrators | Headless Opus sessions on another account that spawn their own subagents for work that needs 3+ agents or 30+ minutes. | 🔒 private (skills) |
| file-claim hook | PreToolUse hook. Locks a file to the session editing it, denies other sessions for 10 min after the last edit. | 🔒 private |
| Subagents | `Explore` (read only), `implementer` (well specified change), `reviewer` (adversarial review, runs the tests). | 🔒 private |

### Seeing what is going on

| Part | What it does | Repo |
|---|---|---|
| Session viewer | Local dashboard + TUI. Which sessions are working, which wait for me, answer them inline. Stdlib Python. | [korentmaj/simple-agent-session-manager](https://github.com/korentmaj/simple-agent-session-manager) |
| HQ dashboard | The cockpit. Accounts, running jobs, sessions, briefs, vault health, skills, projects. Also a read API for agents. | 🔒 private |

### Daily life

| Part | What it does | Repo |
|---|---|---|
| majbrief | Morning brief. Reads vault, Notion, calendar and news, mails me a plan for the day. Runs on the subscription, no API bill. | [korentmaj/majbrief](https://github.com/korentmaj/majbrief) (generic version), live one 🔒 |
| FreeFlow | Hotkey, talk, polished text appears in any app. Most of my prompts start as voice. | [korentmaj/freeflow](https://github.com/korentmaj/freeflow) |
| Thunderbird AI reply | Button in Thunderbird that drafts a reply with Claude Code on my own machine. | [korentmaj/thunderbird-ai-reply](https://github.com/korentmaj/thunderbird-ai-reply) |

### Skills and plugins

About 85 skills across the accounts. The ones I wrote and that matter most: `korent-harness` (lets an agent understand this whole setup), `majvault` (search and write the vault), `delegate` (fork a big task to another account), `maj-tone` (write in my voice), `transkripcija` (audio to speaker labelled text). All 🔒 for now.

Third party plugins I run every day: [superpowers](https://github.com/obra/superpowers) (brainstorm, plan, TDD, verify), ponytail (keeps agents lazy in the good way), Context7 (current library docs instead of memory), TypeScript LSP, TypeSafe (typed AI judgments).

## What I would take first

If you want one thing from this: the vault loop. Read `CLAUDE.md` before work, write a trace after, distill traces back into `CLAUDE.md`. Start with [obsidian-tracehold](https://github.com/korentmaj/obsidian-tracehold). Everything else is nice to have.

Honest limits: it is Windows only, tied to my paths, and the private parts are not cleaned up for anyone else. Ask me if you want a specific piece opened.

Maj Korent · [WMinus](https://wminus.com)
