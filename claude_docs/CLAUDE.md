# Documentation Directory -- Conventions

## Purpose

This directory contains documentation for Claude's use when working on the codebase. These are on-demand docs, not auto-loaded. Claude discovers them via `claude_docs/docs.md` when deeper context is needed.

## Directory Structure

Top-level subdirectories are organized by **document type**. The taxonomy is project-specific — define and list the chosen top-level directories as the project develops.

```
claude_docs/
  docs.md               <- manifest of all topics and docs
  CLAUDE.md             <- this file
  <doc-type>/           <- per-project taxonomy
    ...
```

## Naming

- Use `snake_case` for all directory and file names
- **Top-level directories** are fixed document type categories. Do not create new top-level directories without updating this file.
- **Subdirectories within a type** are named after the topic, domain, or feature they describe (e.g. `<doc-type>/<topic_name>/`)
- Name files after the **specific concept** they describe (e.g. `vendor_api_overview.md`, `vendor_basic_services.md`)
- Break docs into multiple files if they are more than about 25000 characters long

## Index Maintenance

- When creating, updating, or deleting any doc file, always update the `docs.md` index file
- **Directory descriptions:** 1-2 sentences covering scope and library location
- **File summaries:** ONE short sentence -- just enough to judge relevance. Detailed content belongs in the doc file itself, not the index
- Do not duplicate doc file content into the index. The index is a lookup tool, not a summary document

## Staleness

- If a doc's content appears inaccurate or outdated while being used, flag it to the user rather than silently working around it
- Do not update doc content without user approval
