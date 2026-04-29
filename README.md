# The Documentarian

A self-managing documentation agent for any codebase. The Documentarian runs in short, repeated sessions, plans its own work, dispatches research sub-agents, produces one document per session, and journals what it did. Goal: improve future agents' ability to navigate, understand, and maintain the codebase.

## Invocation

Routine session:

```
claude --agent documentarian "Run a Session"
```

Out-of-cycle / one-off work:

```
claude --agent documentarian "Special Session -- ignore usual session lifecycle and await instructions"
```

## Setup

### 1. (Optional, recommended) Dedicated documentation branch

Create a branch off `main` for documentation work, ideally as a git worktree so docs and code can progress in parallel. Periodically merge `main` into the docs branch (pull in code changes) and merge the docs branch back into `main` (publish accumulated docs).

### 2. Place the template files

Extract `.claude/`, `claude_docs/`, and `claude_documentarian/` into the project (or worktree) root. If `.claude/` already exists from prior Claude Code use, merge the contents rather than overwriting.

### 3. First session: configuration + phase planning

Run the first invocation. The Documentarian:

1. Configures the Quirk Prosecutor for the team's ticketing system. Follow the Setup block in `.claude/agents/quirk-prosecutor.md` — replace the four placeholders.
2. Runs a phase-planning session with the human, defining the first phase's scope and exit criteria.

After that, normal session cadence begins.

### 4. Operating mode

- **Sequential only.** Run one session at a time; review the wrap-up summary before invoking the next.
- **Human-in-the-loop.** The Documentarian dispatches sub-agents, runs git commands, edits its own `.claude/` files, and may make MCP calls. Plan to attend each session.

### 5. (Optional) Launch shortcut

Windows shortcut that opens a fresh terminal at the project (or worktree) directory and launches the Documentarian:

```
"C:\Users\<you>\AppData\Local\Microsoft\WindowsApps\wt.exe" -d "C:\Projects\<project>" pwsh -NoExit -Command "claude --agent documentarian --permission-mode acceptEdits 'Run a Session'"
```

`--permission-mode acceptEdits` auto-accepts file edits (including the Documentarian's self-edits to its `.claude/` files); Bash, sub-agent dispatch, and MCP calls still prompt for approval.

## File map

```
INVOCATION
.claude/agents/documentarian.md             ← invoke this
    │
    │ invokes
    ├──▶ .claude/skills/documentarian-{research,write,wrapup,
    │                                  phase-change,agent-refinement}/
    │
    │ dispatches
    ├──▶ .claude/agents/documentarian_*.md   ← research sub-agents
    │                                          (sample template at claude_documentarian/
    │                                           documentarian_example-library-scout.md)
    │
    │ may spawn at wrap-up
    ├──▶ .claude/agents/quirk-prosecutor.md  ← verifies + files tickets
    │
    │ may spawn (rare, break-glass)
    └──▶ .claude/agents/topiarist.md         ← prunes Documentarian's agent files

STATE  (read at orient, mutated at wrap-up)
    claude_documentarian/
        phases.md                             ← phase ledger
        documentarian-journal.md              ← current state + ≤3 recent entries
        documentarian-archive.md              ← demoted session summaries
        plans/                                ← per-phase + per-topic plans

OUTPUT  (accumulated across sessions)
    claude_docs/                              ← on-demand documentation
        docs.md                               ← doc index
        CLAUDE.md                             ← doc-type conventions (project-specific)
        <type>/                               ← subdirectory layout per claude_docs/CLAUDE.md
    .claude/rules/                            ← path-scoped auto-loaded rules
```

## Recommended phase flow

A phase is a thematic batch of sessions. Phases are defined per-project in consultation with the human during phase-planning. A typical rollout for a new codebase:

1. **Reference docs** — library- and module-level documentation: method signatures, patterns, and conventions, organized per directory.
2. **Rules files** — path-scoped, auto-loaded guidance capturing common patterns, pitfalls, and navigation hints for agents working in specific areas.
3. **Cross-cutting workflows** — end-to-end feature traces spanning layers and files (UI through application code to data persistence).
4. **Business logic** — plain-language descriptions of user-facing behavior, building on the workflow docs and grounded with read-only database queries where authorized. No method names, no column names, no tech or business jargon. 
5. **Maintenance mode** — ongoing updates as the codebase evolves: new files, recent git changes, and ticket-driven fixes or feature implementations.

Order is a default, not a rule. Adjust during phase-planning to fit the project.

## Customization

- **Ticketing system:** the Quirk Prosecutor is system-agnostic. Configure during first-time setup. See its Setup block.
- **Research sub-agents:** a barebones template lives at `claude_documentarian/documentarian_example-library-scout.md`. Copy it into `.claude/agents/`, rename, fill in `Codebase Context`, and add stack-specific extraction rules during the first phase.
- **Document organization:** the layout under `claude_docs/` is project-specific. Define the taxonomy that fits the codebase in `claude_docs/CLAUDE.md`. The Documentarian's write skill recognizes common output shapes (rules files, workflow docs, business-logic docs, reference docs, plans) — map these to whatever directory structure suits the project.
