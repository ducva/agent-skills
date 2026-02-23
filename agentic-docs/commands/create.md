---
description: Create a new document (README, ADR, spec, design doc) for a project module or topic using AI research
argument-hint: "<type> <topic>  (e.g., readme my-module, adr use-postgres, spec auth-flow)"
allowed-tools:
  - Read
  - Write
  - Glob
  - Grep
  - Bash
  - Task
  - AskUserQuestion
---

You are helping the user create a well-structured document for their project.

## Steps

1. **Parse arguments**: Extract `<type>` and `<topic>` from the user's input (`$ARGUMENTS`).
   - Valid types: `readme`, `adr`, `spec`, `design`, `architecture`
   - If either is missing, use `AskUserQuestion` to ask for them.

2. **Clarify target path** (if not obvious):
   - For `readme`: place in the relevant module directory, e.g. `packages/my-module/README.md`
   - For `adr`: place in `docs/adr/NNN-topic.md` (auto-increment if ADR directory exists)
   - For `spec`/`design`/`architecture`: place in `docs/` or ask the user

3. **Delegate to doc-drafter agent**:
   Use the Task tool to spawn the `agentic-docs:doc-drafter` agent with a clear prompt:
   ```
   Create a <type> document about <topic> for this project.
   Target file: <resolved-path>
   Research the codebase thoroughly before writing.
   ```

4. **Report**: After the agent completes, tell the user what was created and its path.

## Document Type Templates

| Type | Structure |
|------|-----------|
| `readme` | Title, description, installation, usage, API, configuration, contributing |
| `adr` | Title, status, context, decision, consequences |
| `spec` | Overview, goals, non-goals, design, API/interfaces, edge cases, open questions |
| `design` | Problem, proposed solution, alternatives, trade-offs, implementation plan |
| `architecture` | System overview, components, data flows, dependencies, deployment |
