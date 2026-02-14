# Setup Agent CLI Hooks

Set up lifecycle hooks for all major AI coding agent CLIs from a single script. Supports **Claude Code**, **Codex CLI**, **OpenCode**, **Gemini CLI**, and **Amp**.

## Why?

Each AI coding agent CLI has its own hook system with different formats:

| Agent CLI | Config Format | Hook Config Location | Hook Events |
|-----------|--------------|---------------------|-------------|
| Claude Code | JSON (settings.json) | `.claude/settings.json` | SessionStart, PreToolUse, PostToolUse, Stop, Notification, +more |
| Codex CLI | TOML (config.toml) | `.codex/config.toml` | notify (agent-turn-complete only) |
| OpenCode | TypeScript plugins | `.opencode/plugins/` | tool.execute.after, session.idle, +more |
| Gemini CLI | JSON (settings.json) | `.gemini/settings.json` | SessionStart, BeforeTool, AfterTool, AfterAgent, Notification, +more |
| Amp | Executable toolboxes | `.amp/toolboxes/` | describe/execute pattern |

This script creates a **shared hook layer** (`.agent-hooks/`) with portable bash scripts, then generates the agent-specific configuration wrappers for each CLI.

## Quick Start

```bash
# Set up default lint-on-edit hooks for all agents
./setup-agent-hooks.sh

# Preview first
./setup-agent-hooks.sh --dry-run

# Choose a different hook preset
./setup-agent-hooks.sh --hook-type test-on-write
```

## Architecture

```
your-project/
├── .agent-hooks/              # Shared hook scripts (portable bash)
│   ├── lint-on-edit.sh
│   ├── block-rm.sh
│   ├── test-on-write.sh
│   ├── notify-done.sh
│   └── session-context.sh
├── .claude/
│   └── settings.json          # Claude Code hooks config
├── .codex/
│   └── config.toml            # Codex CLI notify config
├── .opencode/
│   └── plugins/
│       └── agent-hooks.ts     # OpenCode TypeScript plugin
├── .gemini/
│   └── settings.json          # Gemini CLI hooks config
└── .amp/
    └── toolboxes/
        └── <tool-name>        # Amp toolbox executable
```

## Hook Presets

### `lint-on-edit` (default)

Runs a linter on files after they are edited or written. Auto-detects the language and linter:

- **JS/TS**: ESLint
- **Python**: ruff / flake8
- **Go**: golangci-lint
- **Rust**: cargo clippy
- **Ruby**: RuboCop

### `block-rm`

Blocks destructive `rm -rf` commands targeting dangerous paths (`/`, `~`, `$HOME`, `..`, `.git`). Uses `PreToolUse` / `BeforeTool` events to intercept before execution.

### `test-on-write`

Runs the project's test suite after files are written. Auto-detects the test runner:

- **JS/TS**: `npm test`
- **Python**: `pytest`
- **Go**: `go test ./...`
- **Rust**: `cargo test`
- **Ruby**: `bundle exec rake test`

### `notify-done`

Sends a desktop notification when the agent finishes. Supports macOS (osascript), Linux (notify-send), and Windows WSL (PowerShell).

### `session-context`

Injects git context (branch, recent commits, uncommitted changes) when a session starts.

### `custom`

Use any command you want:

```bash
./setup-agent-hooks.sh --hook-type custom --custom-cmd "npx prettier --check ."
```

## Agent CLI Details

### Claude Code

Hooks are configured in `.claude/settings.json` using the `hooks` object. Supports 14+ lifecycle events including `SessionStart`, `PreToolUse`, `PostToolUse`, `Stop`, `Notification`, `SubagentStart/Stop`, and more. Hooks receive JSON via stdin and can return structured decisions.

**Docs**: https://code.claude.com/docs/en/hooks

### Codex CLI

Codex has limited hook support via the `notify` key in `.codex/config.toml`. Currently only the `agent-turn-complete` event is supported. The community has requested additional hook events.

**Docs**: https://developers.openai.com/codex/config-advanced/

### OpenCode

OpenCode uses TypeScript plugins placed in `.opencode/plugins/`. Plugins export event handlers that receive typed event objects. The plugin system provides `client` and `$` (Bun shell API) utilities.

**Docs**: https://opencode.ai/docs/plugins/

### Gemini CLI

Hooks are configured in `.gemini/settings.json` with a structure similar to Claude Code. Supports `SessionStart`, `BeforeTool`, `AfterTool`, `BeforeAgent`, `AfterAgent`, `Notification`, and more. Hooks receive JSON via stdin.

**Docs**: https://geminicli.com/docs/hooks/

### Amp

Amp uses "toolboxes" — executable scripts that self-describe via the `TOOLBOX_ACTION=describe` pattern. When `TOOLBOX_ACTION=execute`, the script runs the actual tool logic. Set `AMP_TOOLBOX` to the toolboxes directory.

**Docs**: https://ampcode.com/manual

## Options Reference

| Option | Description | Default |
|--------|-------------|---------|
| `--project-dir DIR` | Target project directory | `.` (current) |
| `--scope SCOPE` | `project` or `user` | `project` |
| `--hook-type TYPE` | Hook preset | `lint-on-edit` |
| `--custom-cmd CMD` | Command for custom preset | — |
| `--dry-run` | Preview without writing | `false` |
| `--list-agents` | List detected CLIs | — |
| `--list-hooks` | List available presets | — |

## Requirements

- **bash** 4.0+
- **jq** (for JSON manipulation — optional but recommended)
- The hook scripts themselves detect and use available linters/test runners
