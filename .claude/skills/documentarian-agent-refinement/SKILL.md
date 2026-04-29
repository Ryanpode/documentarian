---
name: documentarian-agent-refinement
description: Refinement conventions for The Documentarian's agent files. Defines the methodology-vs-checklist test, allowed refinement outcomes, and the stop-after-editing rule. Invoke this at the start of the Write phase when the session is an agent refinement session.
allowed-tools: Read, Write, Edit, Glob, Grep, Agent
---

# Documentarian Agent Refinement Phase

You are entering the Write phase of an agent refinement session. Instead of producing a doc, this session produces changes to an existing agent file (or splits one into multiple). Read this entire skill before you begin editing.

The goal: agent files earn their permanent place by encoding durable methodology. Task-specific checklists do not earn that place; they belong in invocation prompts. Over time, agent files accumulate archaeology. This session removes it.

## Constraints
- ONLY make edits to agent files with the "documentarian_" prefix. The topiarist is never edited in a session where this skill is invoked. 
- When you rename an agent or create a new one, you must give it a creative, stylish, and unique name that fits the theme of the agent's description and purpose. 

## The methodology-vs-checklist test

Before keeping or adding any content in an agent file, apply this test:

- **Methodology** -- describes *how to work*. Applies regardless of file type or specific task. Encodes judgment the agent needs every time it runs. Examples: quirk reporting discipline, uniform pattern condensation, parallel-pair diff methodology, output format rules.
- **Checklist** -- describes *what to capture for one file type or task*. Applies only when reading file type X or producing output Y. Example: "when reading <file type Y>, capture <specific data Z>."

Methodology stays. Checklists move to invocation prompts. When in doubt, cut -- a future session can always re-promote content if it proves durable across multiple unrelated invocations.

## Step 1: Invoke the Topiarist

Spawn the Topiarist (`.claude/agents/topiarist.md`) to read the target agent file plus its journal usage history and return a structured pruning proposal. The Topiarist's classification framework, controlled vocabulary, and required output shape are defined in its agent file -- consult it for full detail. You review the proposal and apply edits; the Topiarist does NOT edit files.

## Allowed refinement outcomes

This session may produce any of these changes, alone or in combination:

- **Prune** -- delete sections that fail the methodology-vs-checklist test or show no recent use.
- **Merge** -- combine overlapping modes into one section with clear conditional guidance.
- **Promote** -- move a durable cross-cutting rule into a stable "Core Principles" top section.
- **Split** -- create a new agent file when scope has drifted too far to reconcile in one agent. Update `documentarian-journal.md` agent inventory.
- **Rename / rescope** -- update the agent's `description` frontmatter when actual scope differs from declared scope. The agent `name` may also change if it misrepresents purpose.
- **Move-to-prompt** -- content that is task-specific but worth keeping goes into a prompt-template file or is simply discarded. The documentarian writes task-specific briefs into invocation prompts; they do not live permanently in the agent file.

## Core Principles convention

Every refined agent file should have a stable top section (after the opening summary) labeled "Core Principles" or equivalent. This section holds durable cross-cutting rules -- the content that survives every prune. Anything below Core Principles is subject to removal in future refinement sessions. This gives future prune passes a clear target.

## Keep the agent's shape intact

- Ensure the frontmatter description always remains accurate. 
- Do not remove the opening identity/role paragraph.
- Do not remove the base output format spec unless replacing it with a better one.
- Do not remove references to codebase-wide conventions (repo paths, language-stack context) that every invocation needs.

## Journal the cuts

When the session ends, the wrapup journal entry must record:

- Which agent was refined.
- Section titles or line ranges removed, with a one-line reason each.
- Any split-to-new-agent changes, with pointers to both files.

This log lets future refinement sessions see what was already considered and rejected, preventing re-addition of cut content.

## Stop after editing

When the refinement is complete, **DO NOT START ANOTHER TASK.** Even if:

- Another agent file looks bloated.
- You noticed a doc that should be updated.
- Your analysis surfaced a new rules file to write.

Queue any follow-ups in your journal during wrap-up. Do not start a new write, refinement, or research task in the same session.

Proceed to `/documentarian-wrapup` when refinement is complete.
