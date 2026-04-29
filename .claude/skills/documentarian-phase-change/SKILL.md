---
name: documentarian-phase-change
description: Conventions for The Documentarian's phase-change session -- the special session that closes one phase and opens the next. Invoke this at the start of the Write phase when the session is a phase-change session.
allowed-tools: Read, Write, Edit, Glob, Grep
---

# Documentarian Phase-Change Session

You are running a phase-change session. This is a special session type that closes the current phase (if one was In Progress) and opens the next phase. Read this entire skill before you begin.

A phase-change session is fundamentally different from a normal session:
- It does NOT produce documentation output for the codebase.
- It does NOT dispatch sub-agents.
- It DOES heavily mutate the Documentarian's own meta-state: phases.md, the journal, plan files.
- It REQUIRES Human consultation before any operations begin.

## Trigger

A phase-change session is warranted when EITHER:

1. **Exit criteria met.** The current phase's per-phase plan file (`claude_documentarian/plans/phase_<N>_<name>.md`) lists exit criteria; all are now satisfied.
2. **No phase In Progress.** Phases.md shows the current phase as Complete (or no phase ever entered) and the next phase is Not Started. This is the natural trigger after the prior phase-change session marked the prior phase Complete without immediately opening the next.

The Documentarian recognizes the trigger at orient time by reading phases.md and the per-phase plan. If the trigger fires, the session's Write phase invokes this skill instead of `/documentarian-write` or `/documentarian-agent-refinement`.

## Pre-flight: Human consultation (MANDATORY)

Before any file operations, surface the following to the Human in chat and pause for explicit confirmation:

1. **Which phase is closing.** Name the phase. Confirm that exit criteria are met (with a checked-list summary). If this is an entry-only transition (no phase was In Progress), state that explicitly.
2. **Which phase is opening.** Name the phase per the phases.md ledger.
3. **Proposed exit criteria for the opening phase.** This is where the upcoming phase's success conditions get defined. Draft them. Be concrete (named outputs, file counts, coverage thresholds). The Human reviews and adjusts before you commit them to the plan file.
4. **Agent needs for the opening phase.** Does the phase require a new agent built before output starts? If yes, name the agent and propose its scope. If the phase includes a new document type, assume a new agent will be needed.
5. **First-session sketch for the opening phase.** One paragraph: what does session 1 of this phase look like. This lands in the journal Next Session Plan during operations.

Wait for explicit Human approval. If the Human pushes back on any of the above, revise and re-surface. Do not proceed to operations until everything is approved.

## Mandatory operations

Once the Human has approved, perform the following in order:

### 1. Wholesale archive Recent Sessions

The journal's Recent Sessions section currently contains up to 3 prior session entries. They are biased toward continuing the closing phase's work shape and would mislead the next session. Move them out of the journal.

For each session entry currently in the journal's Recent Sessions:
1. Lift its `**Summary:**` line.
2. Append a row to `claude_documentarian/documentarian-archive.md`: `| session# | date | summary |`.
3. Delete the entry from the journal.

After this step, the journal's Recent Sessions section is empty (just the heading).

### 2. Update phases.md

Edit `claude_documentarian/phases.md`:
- If a phase was In Progress, change its status to **Complete**.
- Change the opening phase's status to **In Progress**.
- Make no other changes. Do not add session annotations, transition notes, or commentary. Phases.md is a ledger, not a history.

### 3. Create the per-phase plan file

Create `claude_documentarian/plans/phase_<N>_<descriptive_name>.md` (e.g. `phase_4_business_rules.md`, `phase_5_maintenance.md`) using this template verbatim:

```
# Phase <N>: <Phase Name>

<!-- This file holds durable phase-level content only. Do NOT add session-level history, drift annotations, or transition notes. Per-session content lives in the journal Next Session Plan. -->

**Status:** In Progress.

## Scope

<One paragraph: high-level guiding principle. What this phase produces, where it lives in the repo, who reads it, and how output is shaped differently from prior phases. Keep this directional, not prescriptive. Operational constraints belong in Phase guidelines.>

## Phase guidelines

<Phase-specific operating rules, constraints, and protocols. Bulleted. Stronger language than Scope. Easy to recall mid-session. This is where "do not do X" and "always do Y" rules land.>

- <guideline 1>
- <guideline 2>

## Exit criteria

This phase exits when ALL of the following are satisfied:

- [ ] <concrete criterion 1>
- [ ] <concrete criterion 2>
- [ ] <...>

## Agent needs

<Name agents required for this phase. Distinguish: existing agents that apply / existing agents that need adjustment / new agents that must be built before output starts.>
```

Fill the template using the content approved during Human consultation. The First-session sketch from consultation does NOT go in the plan file -- it lands in the journal Next Session Plan in step 5.

### 4. Update the journal Phases section

The journal's Current State has a Phases section pointing at phases.md. Update it so it:
- Names the new In Progress phase.
- Points at the new per-phase plan file.
- States that the prior phase is Complete (if applicable).
- Stays at ≤4 lines.

Example:
```
### Phases
See `claude_documentarian/phases.md` for the full ledger. Phase <N-1> is Complete; Phase <N> (<name>) is In Progress, plan at `claude_documentarian/plans/phase_<N>_<name>.md`.
```

### 5. Reset journal Next Session Plan

The Next Session Plan is currently scoped to the closing phase's work shape. Replace it with a 2-3 line plan rooted in the new phase: cite the new plan file and write session 1 from the First-session sketch approved during pre-flight consultation. After session 1 runs, the Next Session Plan rolls forward via normal wrapup (it is not retained as a permanent record).

### 6. Sanity check

Re-read phases.md, the new plan file, the journal Phases section, the journal Next Session Plan. Confirm:
- Phases.md shows exactly one phase In Progress.
- The new plan file exists and is filled in.
- The journal points at the new plan.
- No journal section references the closing phase as if it were ongoing.
- The Recent Sessions list contains at most the entry for THIS session (which gets prepended in wrapup).

## Stop after the operations

When the operations are complete, **DO NOT START ANOTHER TASK.** Even if:
- The new phase's session 1 looks small enough to start.
- You noticed something else worth doing.

The phase-change session ends after operations + wrapup. The next session begins fresh in the new phase.

Proceed to `/documentarian-wrapup` when operations are complete. The wrapup writes a minimal Recent Sessions entry naming the phase change, commits, and stops. No quirk escalation step (this session produces no quirks).
