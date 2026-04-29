---
name: documentarian-wrapup
description: End-of-session commit routine for The Documentarian. Stages and commits all documentation changes with the correct conventions. Invoke this at the end of every session.
allowed-tools: Read, Edit, Bash(git *)
---

# Step 1: Wrap-up 

Plan for the next session. You will likely have an over-arching plan that you are already following. However, you must always carefully consider if the current session changes the plan priority. Use your journal to keep track of your session-to-session planning. 

## Wrap-up steps 

### 1. Prioritize for Next Sessions

Consider whether you faced any challenges in the current session, or whether your actions could create challenges for a future session. 

- If you were prevented from doing a task by the "DO NOT START ANOTHER TASK" rule, queue that task as a candidate future session with priority.
- If a document became larger than the recommended limit, queue that task as a candidate future session with priority.
- If your findings in the current session warrant a special deep-search research session that only outputs a plan, plan a full session for this. 
- If you found that you had to read your own documents in order to make decisions or discern your own conventions, plan a full session to improve your index documents or make a paths-scoped rule.  
- If there were any hard-to-explain inconsistencies in your session findings, you may want to plan a web search session to review and revise the current document or series of documents for accuracy. This is especially important for external sources, APIs, or service wrappers.
- If and only if none of the above are true, continue with your current overarching plan, if you have one.  
- If you're unsure if something is right-sized for a session, review the Task Sizing section of your documentarian.md agent instruction file. 

**Prioritize staying focused and acting accurately within each session context rather than overall token efficiency.** 

###  2. Update the Journal  

The `documentarian-journal.md` has three sections with different update rules. Update the log according to these rules:

#### A. Current State (edit in place, always reflects latest)

Contains:
- Agent Inventory table
- Remaining Work Summary tables
- Next Session Plan (replace fully)
- Do not include info about the prosecutors or their results in the journal. They are fire-and-forget, and they handle all their own tracking and updates themselves. 

Use the Edit tool to update these in place. Do NOT append new copies. The Next Session Plan from the previous session is overwritten by yours.

##### Journal Hygiene (hard rules)

The journal is an **orientation primer** — only the information needed to plan the next session. Detailed plans, audit queues, and per-doc backlogs belong in plan files (`/claude_documentarian/plans/`), not the journal. Apply these caps every wrapup:

- **Agent Inventory:** ≤2 short sentences per cell (purpose + current status). No refinement-history archaeology. "Refined S<N> via Topiarist (M→K lines)" is archaeology — cut. The agent file itself is the durable record.
- **Next Session Plan:** ≤30 lines total. Structure: 1 sentence on what the prior session delivered + 1 short paragraph on the recommended next move + ≤4 bulleted alternatives. If you find yourself writing follow-up lists, audit-only queues, or per-quirk backlogs, stop — those go in a topic-specific plan file under `plans/`. Reference the plan file by path; do not inline.
- **Open prosecutor candidates carry exactly one session.** If the prior session's Next Session Plan listed unspawned prosecutor candidates, this wrapup either spawns them or drops them — do not re-list them in the new Next Session Plan. If they have lasting value, move to a plan file with explanation. Never accumulate across multiple sessions.
- **Operational Notes:** durable conventions only. Transient session observations are not durable.

#### B. Recent Sessions (prepend new entry, cap at 3)

The Recent Sessions section runs to the end of the journal file -- there is nothing below it. This makes demotion a simple tail-trim.

- Prepend a new entry for this session at the top of the Recent Sessions section.
- The first line of the entry MUST be a single-sentence one-line summary, used later for archive demotion. Format:
**Summary:** <one sentence describing what was done>.
  - **Hard cap: ≤1 sentence, ≤200 characters.** Mentally paste it into `| <session#> | <date> | <summary> |` — if the cell would wrap to a second line, trim. No quirk counts, no numbered references, no per-observation status. The summary is for archive lookup, not for re-explaining the session.
- Per-entry content: **Summary** and **Completed** bullets only.
- Do NOT repeat Agent Inventory or Remaining Work Summary inside the entry -- those live only in Current State.
- **Length cap:** the per-session entry should be roughly 1 page or less. Aim for ≤10 Completed bullets.

**Phase-change session exception:** if this session was a phase-change session, it just wholesale-archived all prior Recent Sessions entries. Prepend a single short entry for this session (Summary + ≤5 Completed bullets naming the closed/opened phases and the new plan file). Skip Step C (archive demotion) -- the Recent Sessions list will only have one entry. Skip Step 4 quirk escalation (phase-change sessions produce no quirks).

#### C. Archive demotion (append to `documentarian-archive.md`)

If Recent Sessions now contains more than 3 entries, demote the oldest (which is the final entry in the file):
1. Read the last session entry in `documentarian-journal.md` to lift its **Summary:** line.
2. Append a row to the table in `claude_documentarian/documentarian-archive.md`: `| session# | date | one-line summary |`.
3. Delete the trailing session entry from the journal. Since Recent Sessions runs to EOF, this is a straight tail-trim -- no separator or following section to preserve. **Use dedicated tools only:** `Read` the tail of the journal to see the exact block to remove, then `Edit` with `old_string` = the full trailing session block (including its leading blank line) and `new_string` = empty. Do NOT shell out to `head`/`tail`/`sed`/redirection to rewrite the file -- dedicated tools are faster and do not require approval.

Do not read `documentarian-archive.md` beyond the append operation. It is not session-start context.

# Step 2: Commit

## Preprocessed Context

- **Current branch:** !`git branch --show-current`
- **Working directory:** !`pwd`
- **Git status:** !`git status --short`
- **Unstaged diff (stat):** !`git diff --stat`
- **Staged diff (stat):** !`git diff --cached --stat`

## Commit Conventions

Follow these rules exactly:

1. **Never combine `cd` and `git` in one command.** Do not use `cd <dir> && git ...`. If you must change directory, do it in a separate Bash call from any git command. Prefer using `git -C <path>` instead of `cd`.
2. **Do not add "Co-Authored-By: Claude"** or any variation of it.
3. **Sign the commit as The Documentarian.** End every commit message with:
   ```
   Authored by The Documentarian
   ```
4. Avoid the `"$(cat...` pattern, as it requires approval by human. Keep the commit message simple.

## Commit Steps

### 1. Review what will be committed

Use the preprocessed context above to understand what changed. If the status is empty, there is nothing to commit -- inform the user and stop.

### 2. Stage changes

Stage all documentation changes. Use specific file paths rather than `git add -A` or `git add .` when possible.

Do NOT stage:
- Claude Code config files (`.claude/settings.json`, `.claude/settings.local.json`). 
- Any file that is not documentation, comments, a journal entry, the documentarian file, an agent file, a skills file, or a rules file. 

### 3. Write the commit message

- **Summary line:** Brief description of what was documented or updated (under 72 chars).
- **Body (optional):** If the session touched multiple areas, list them as bullet points.
- Always end with `Authored by The Documentarian`.

### 4. Commit and verify

Commit, then run `git status` to confirm the working tree is clean (or that only intentionally unstaged files remain).

# Step 3: Output

Give an succinct output to Human. This output should be given in layperson's terms, in a way where a non-tech manager would be able to understand the reasoning. 

- Brief session summary. Include agents used/improved, docs, what you chose to put in your journal. 
- Short Summary of document(s) written this session.
- Summary of plan for next session.
- Your process and decisions when prioritizing the next session. Include reasoning for choosing next session plan.

# Step 4: Quirk Escalation (if applicable)

Review all quirks discovered in this session. For any that you believe could cause incorrect behavior in production -- wrong data, broken UI, silent failures affecting users -- spawn a Quirk Prosecutor agent to verify and escalate.

For each quirk worth escalating:
1. Spawn the prosecutor as a **background** agent via the Agent tool (`subagent_type: "quirk-prosecutor"`).
2. Provide the quirk details: session number, file path, line number, the incorrect code, the correct code, and your analysis.
3. The prosecutor will independently verify the bug and file a ticket in the configured ticketing system if confirmed.

If no quirks warrant escalation, skip this step.

**After spawning all prosecutors, do NOT wait for verdicts or read partial output. The session ends here.**

