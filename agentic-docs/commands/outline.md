---
description: Generate a structured document outline (headings only) for review before full drafting
argument-hint: "<type> <topic>  (e.g., spec auth-flow, adr use-redis)"
allowed-tools:
  - Read
  - Glob
  - Grep
  - Write
  - AskUserQuestion
---

You are helping the user create a document outline — headings and brief section descriptions only, not full content. The user will review this outline before committing to a full draft.

## Steps

1. **Parse arguments**: Extract `<type>` and `<topic>` from `$ARGUMENTS`.
   - If missing, use `AskUserQuestion` to clarify.

2. **Research**: Quickly scan the codebase for context:
   - Use `Glob` to find relevant source files, configs, existing docs
   - Use `Read` to skim key files (README, package.json, main source modules)
   - Identify the key concepts, components, and flows related to `<topic>`

3. **Generate the outline**: Output a Markdown document with:
   - A clear `# Title`
   - All section headings (`##`, `###`) with 1-line descriptions of what each section will cover
   - Placeholder `> [TODO: ...]` notes for content
   - No prose paragraphs — headings + brief notes only

4. **Ask the user**: After showing the outline, ask:
   > "Does this outline look good? Should I adjust any sections before drafting the full document?"

5. **Write if approved**: If the user approves, optionally save to a file (ask for path). If they request changes, revise and show the updated outline.

## Outline Format Example

```markdown
# Auth Flow Specification

## Overview
> [TODO: Brief description of the auth system and its goals]

## Goals
> [TODO: What this spec aims to define]

## Non-Goals
> [TODO: Explicit scope exclusions]

## Design
### Token Strategy
> [TODO: JWT vs session, storage approach]
### Login Flow
> [TODO: Step-by-step login sequence]

## API
> [TODO: Endpoint definitions, request/response shapes]

## Edge Cases
> [TODO: Expiry, revocation, concurrent sessions]

## Open Questions
> [TODO: Unresolved decisions]
```
