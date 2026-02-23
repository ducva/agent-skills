# agentic-docs

A Claude Code plugin for AI-powered document creation and management. Automatically drafts, reviews, outlines, and updates Markdown and architecture documents for your project.

## Features

- **`/agentic-docs:create`** — Research the codebase and draft a complete document (README, ADR, spec, design doc)
- **`/agentic-docs:outline`** — Generate a document outline for review before full drafting
- **`/agentic-docs:review`** — Analyze an existing doc for accuracy, completeness, and staleness
- **`/agentic-docs:update`** — Sync an existing doc with the current codebase, rewriting only stale sections
- **Proactive suggestions** — After editing source files, a lightweight agent checks if documentation is missing

## Agents

| Agent | Model | Role |
|-------|-------|------|
| `doc-drafter` | Sonnet | Researches codebase and writes full documents |
| `doc-suggester` | Haiku | Scans for missing docs and suggests improvements |

## Installation

```bash
# Option 1: Reference directly
claude --plugin-dir /path/to/agentic-docs

# Option 2: Add to your project's .claude/settings.json
{
  "plugins": ["/path/to/agentic-docs"]
}
```

## Setup

No additional setup required. The plugin uses your existing Claude Code session.

## Usage

### Create a document

```
/agentic-docs:create readme my-module
/agentic-docs:create adr use-postgres
/agentic-docs:create spec auth-flow
/agentic-docs:create design payment-refactor
```

### Generate an outline first

```
/agentic-docs:outline spec websocket-api
```

### Review an existing document

```
/agentic-docs:review README.md
/agentic-docs:review docs/architecture.md
```

### Update a stale document

```
/agentic-docs:update docs/api-spec.md
```

### Ask about missing docs

```
What docs are missing for my project?
```

## Document Types

| Type | Output Location | Structure |
|------|----------------|-----------|
| `readme` | Module directory | Title, install, usage, API, config |
| `adr` | `docs/adr/` | Status, context, decision, consequences |
| `spec` | `docs/` | Goals, design, API, edge cases |
| `design` | `docs/` | Problem, solution, trade-offs, plan |
| `architecture` | `docs/` | Components, flows, dependencies |

## How It Works

1. **Create/Update**: The `doc-drafter` agent reads your source files, configs, and existing docs to understand the codebase, then writes a well-structured document.
2. **Outline**: Claude generates headings + brief descriptions for review before committing to a full draft.
3. **Review**: Claude reads your doc and compares it to the actual codebase, flagging inaccuracies and gaps.
4. **Proactive suggestions**: A PostToolUse hook fires after every source file edit and checks if the directory has relevant documentation.

## Version

`0.1.0`
