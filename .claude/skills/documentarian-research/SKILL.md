---
name: documentarian-research
description: Research conventions for The Documentarian. Defines sub-agent dispatch rules, brief-construction discipline, and task/session sizing. Invoke this at the start of the Planning and Research phase of every session.
allowed-tools: Read, Glob, Grep, Agent
---

# Documentarian Planning and Research Phase

## Plan the current session
- Choose a workload for the session and plan your sub-agent delegations. 
- Proceed directly from planning into Research; do not pause for plan approval.

## Task sizing

- Prefer smaller sessions. A targeted, accurate, concise session is always preferable to a bloated session that tries to do more than it needs to.
- Do NOT try to document an entire large subsystem in one session.
- If a task feels like it will require reading more than ~10 source files, break it up.
- Don't combine tasks. Choose one appropriately sized task, and defer others in your journal. For example, don't write a document on one system and triage another system in the same session, unless the triage is strictly required for the task at hand.

## Session sizing examples

- Writing a small, targeted /rules file to help your agents traverse commonly revisited code is a good session.
- Researching a large library with more than 10 files, and writing yourself a plan for a larger overarching doc project on that library, is a good single session.
- The writing of a broad but efficient single doc file for one library with 10 or fewer files is a good session.
- Adding doc comments across a small library (< 20 public methods) is a good session.
- A gap analysis of one project area is a good session.
- Reviewing and revising an existing large document, or series of related documents, for accuracy and efficacy, is a good session.
- Breaking up one large document into smaller, more specific and individually useful documents is a good session.
- Searching the web to write or update a doc file about an external api or web service is a good session.

## Dispatching sub-agents

- Spawn the custom `documentarian_*` sub-agents you maintain. Do NOT use generic explorers.
- Do NOT instruct an agent to output the final document. The Documentarian writes the doc; the agent conducts research and keeps the Documentarian's context lean.
- If a first round of agent output is insufficient, dispatch again with a refined brief. If a second round is still insufficient, the task is too large -- split it into a plan and a smaller session.

## Brief construction

- Pre-verify load-bearing facts directly from source (small targeted reads) before dispatch. Pass the verified facts as inputs in the brief; the agent's first job becomes verifying-then-extending rather than re-discovering.
- When dispatching an agent for an EXTEND on structured content (tables, mini-tables, fixed-format lists), pre-read the existing structure and pass column headers and row count as inputs. This prevents paste-ready format mismatches that the agent cannot detect without scope-broadening reads.
- Scope the brief to the task at hand. Do not invite tangential surveys.
