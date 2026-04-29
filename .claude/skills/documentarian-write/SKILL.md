---
name: documentarian-write
description: Writing conventions for The Documentarian. Defines the allowed output types, rules file structure, and the stop-after-writing rule. Invoke this at the start of the Write phase of every session.
allowed-tools: Read, Write, Edit, Glob, Grep
---

# Documentarian Write Phase

You are entering the Write phase of the session. Research is complete; now you produce the output. Read this entire skill before you begin writing.

You, the Documentarian, must produce a document. Do not rely on a document that was written by an over-enthusiastic research agent. 

## Output types

Every session must produce exactly one of the following. The shapes below describe what each output is *for*; the directory layout under `claude_docs/` is project-specific and defined in `claude_docs/CLAUDE.md`.

- **Rules file** (preferred for tightly-scoped directories and feature areas) -- auto-loaded by agents working in the target path, highest ROI per session. Lives in `.claude/rules/`. See Rules File Conventions below.
- **Workflow doc** -- traces a feature across layers (UI + application code + client-side scripts + library calls + DB). Answers "what happens when a user does X" end-to-end.
- **Business logic doc** -- describes user-facing behavior in plain language. No code signatures or schema details. Answers "how does X work" for customer-support-style questions.
- **Reference doc** -- library-level documentation with method signatures, patterns, and conventions.
- **Plan** -- for yourself, when a task is too large for one session or requires future research. Lives in `claude_documentarian/plans/` and must be referenced from `documentarian-journal.md`.

Pick ONE. Do not mix types in a single session.

The shape vocabulary above is not a closed set. If the project would benefit from a new shape (or retirement of an existing one), surface the proposal during the next phase-planning session for human approval before adopting it.

**Exception:** When writing rules files, you may produce multiple files in one session if splitting a planned rules file into narrower-scoped siblings would better serve the scope-the-paths-narrowly principle below.

## Rules File Conventions

Rules files live in `.claude/rules/` and auto-load when an agent works on files matching their `paths:` frontmatter pattern. A good rules file:

### Scope the paths narrowly
The `paths:` pattern determines which agent sessions pay the context cost of loading this file. Prefer scoping to specific files or a tight subdirectory over broad wildcards. A rules file that loads on every file under a large directory must justify every single rule against every agent that opens any file in that scope. 

**DO NOT USE `globs:`!! It doesn't work! Use `paths:`! All rules files must contain paths!**

**Frontmatter format:** Always use the YAML list format with quoted strings:
```yaml
---
paths:
  - "src/admin/users/**"
  - "src/admin/user-edit/**"
---
```

### Required sections
- **Target summary** -- one sentence max. 
- **Reference docs** -- pointers to relevant `claude_docs/` files so agents know where deeper context lives
- **Entry-point descriptions** -- short table of files/routes/components with their role in one brief phrase. Skip for directories with more than ~10 entries.
- **Companion asset locations** -- paths to client-side scripts, stylesheets, or other paired assets that correspond to files in this directory
- **Entry-point-to-library mappings** -- which shared library methods the files call and for what purpose
- **Common pitfalls and quirks** -- non-obvious traps that an agent could hit

### Keep it concise
- A rules file that requires scrolling is probably too long. It should feel like a quick-reference card, not a document.
- Link to more detailed content in `claude_docs/`
- Do NOT include boilerplate code examples for patterns that appear across the codebase (auth gates, standard ORM usage, etc.). A short sentence describing the pattern is enough.
- Do NOT duplicate content across sections. If one section already states a constraint, do not repeat it elsewhere.
- Every pitfall must be actionable and non-obvious. Cut stylistic nits, redundant observations, and implementation details an agent can read in 5 seconds from the source. If it doesn't save time or reads, don't include it.
- When condensing tables of similar items (e.g. four near-identical SQL queries), collapse to a single paragraph describing the pattern rather than enumerating each row.

### Length check before finalizing
After writing, compare the file's length against peer rules files in `.claude/rules/`. If it is disproportionately long for its scope (e.g. 3 pages in 250 lines when peers cover 20 pages in 170), trim before committing. Per-page density matters more than absolute length.

## Business Logic Doc Conventions

Business-logic docs describe user-facing feature behavior in plain language. They live under `claude_docs/` per the project's taxonomy in `claude_docs/CLAUDE.md` and may be passed through an MCP boundary to top-level agents that summarize them for non-developer users.

### Audience
- **Primary:** a non-developer human reader, or an automated agent (e.g. an MCP-boundary chatbot or summarizer) that reads the doc and synthesizes a summary for one. Markdown is optimized for downstream agent parsing.
- **Guardrail:** any sentence may be passed verbatim to a non-developer reader. Write so the verbatim quote reads cleanly to such a reader.

### Hard constraints
- **No method catalogs.** Do not enumerate functions, classes, or method signatures. That is reference-doc work.
- **No code blocks** except where a rule cites a specific user-visible value or threshold (e.g. minimum dollar amount, day-count for expiration). Inline-code formatting for a literal value is acceptable.
- **No SQL, schema names, or table names** in the published doc. Schema identifiers don't belong in non-developer-facing docs. Table-name breadcrumbs go in the commit message instead.
- **No internal-path citations.** Do not link to `.claude/rules/` files, workflow docs, or reference docs. Cross-references flow the other way: source docs carry back-references to business-logic docs, added in the same session that creates or updates the business-logic doc.
- **No business jargon and no techno-jargon.** Plain language only. Avoid corporate-speak ("customer journey," "value proposition") and avoid technical-speak ("idempotent," "transactional context," "API endpoint"). Both add tokens without information; downstream consumers don't need them and the verbatim reader is confused by them.

### Baseline sections
Structure varies by feature, but at minimum a business-logic doc contains:
- **Opening paragraph.** One-paragraph definition of the feature in user-facing terms.
- **User-facing behavior.** What the user sees, what they do, what surfaces present the feature.
- **Rules and limits.** Discrete rules the system enforces. Bulleted lists or tables.
- **What happens when...** Feature interactions with other states (cancellation, refund, expiration, account change). Short paragraphs or bullets, each starting with the trigger condition.
- **Terminology.** Internal-name vs user-facing-name mappings, only if mismatched. Skip if none.

Add sections as needed. Do not omit a baseline section without explicit reason.

### Format
- H1: the feature title in user-facing terms.
- H2: each major section.
- Bulleted lists for discrete rules.
- Tables for rules that vary across sub-types of the feature.
- Bold for terms that appear elsewhere in the same doc.

### Naming
- Files use `snake_case` matching the user-facing feature name.
- Where a 1:1 workflow-doc counterpart exists, mirror its name minus qualifiers (e.g. `store_credit_redemption.md` workflow becomes `store_credit.md` business-logic).
- Multi-feature docs use a family name covering the group (e.g. `mailing_lists.md`).

### Length
- Comprehensive enough that a MCP-boundary agent or summarizing agent can answer common questions without further lookup.
- A doc that fits in one screen is probably under-covering. A doc longer than five screens is probably enumerating implementation detail that belongs in reference or workflow docs.

### DB grounding
- Grounding queries are session-only research artifacts. They do not appear in the published doc.
- When a business-logic doc was written using DB grounding, the session commit message lists the database tables consulted as a drift breadcrumb.

### Index update
Add the new doc to `claude_docs/docs.md` under the matching section, one short sentence per file. Apply the standard index conventions in this skill.

## Updating the docs.md index

Whenever you create or update a file under `claude_docs/`, also update the `claude_docs/docs.md` index. The index is a lookup tool, not a summary document. Apply these rules every time:

- **One short sentence per file.** Aim for ≤150 characters; one row of a markdown table. Just enough for an agent to judge "is this the doc I need." Detailed structure description belongs inside the doc itself, not the index.
- **No quirk counts, no numbered references, no methodology tallies, no per-observation status.** That detail lives in the doc.
- **No bolded subsection callouts** (e.g. `**Section X:** ...`, `**Sub-feature subsection:** ...`). One subsection mention is acceptable as a trailing clause if the subsection materially changes which agent should open the file; otherwise omit.
- **Match the local style.** Compare your new entry against neighboring rows in the same table — if peers are one short sentence and yours is a paragraph, trim before saving.
- This rule applies to every doc type (rules files, workflow docs, business-logic docs, reference docs, plans, and any project-specific additions).

When extending an existing doc (EXTEND mode), do NOT append a new clause to that doc's index entry. The index entry describes the doc as a whole; the doc's own internal structure is where the new subsection lives.

## Stop after writing

When you finish your doc, **DO NOT START ANOTHER TASK.** Even if:
- Your doc turned out larger than ideal
- You noticed another area that needs documentation
- You realized existing docs should be re-organized
- Your research surfaced a second topic worth documenting

Queue any follow-ups in your journal during wrap-up. Do not start a new write in the same session.

Proceed to `/documentarian-wrapup` when writing is complete.
