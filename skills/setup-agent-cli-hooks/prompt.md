# Setup Agent CLI Hooks

## Purpose

Set up lifecycle hooks for all major AI coding agent CLIs in a project. This skill creates a unified hook system that works across **Claude Code**, **Codex CLI**, **OpenCode**, **Gemini CLI**, and **Amp** — using a single set of shared hook scripts with agent-specific configuration wrappers.

## Instructions

When the user invokes this skill, run the setup script to configure hooks for their project. The script auto-detects which agent CLIs are installed and creates the appropriate configuration for each.

### Running the Script

```bash
bash "$CLAUDE_PROJECT_DIR/skills/setup-agent-cli-hooks/setup-agent-hooks.sh" [OPTIONS]
```

### Available Options

| Option | Description | Default |
|--------|-------------|---------|
| `--project-dir DIR` | Target project directory | Current directory |
| `--scope SCOPE` | `project` (repo-level) or `user` (global) | `project` |
| `--hook-type TYPE` | Hook preset to install | `lint-on-edit` |
| `--custom-cmd CMD` | Command for `--hook-type custom` | — |
| `--dry-run` | Preview without writing files | `false` |
| `--list-agents` | Detect and list installed CLIs | — |
| `--list-hooks` | List available hook presets | — |

### Hook Presets

| Preset | Description | Agent Events |
|--------|-------------|-------------|
| `lint-on-edit` | Auto-lint files after edits | Claude: PostToolUse, Gemini: AfterTool |
| `block-rm` | Block destructive `rm` commands | Claude: PreToolUse, Gemini: BeforeTool |
| `test-on-write` | Run tests after file writes | Claude: PostToolUse, Gemini: AfterTool |
| `notify-done` | Notification when agent finishes | Claude: Stop, Gemini: AfterAgent, Codex: notify |
| `session-context` | Inject git context on start | Claude: SessionStart, Gemini: SessionStart |
| `custom` | User-provided command | Varies |

### How It Works

1. **Detection** — The script checks for installed agent CLIs (`claude`, `codex`, `opencode`, `gemini`, `amp`)
2. **Shared scripts** — Creates hook scripts in `.agent-hooks/` that work across all agents
3. **Agent configs** — Generates agent-specific configuration files:
   - Claude Code: `.claude/settings.json` with hooks object
   - Codex CLI: `.codex/config.toml` with `notify` key
   - OpenCode: `.opencode/plugins/agent-hooks.ts` TypeScript plugin
   - Gemini CLI: `.gemini/settings.json` with hooks object
   - Amp: `.amp/toolboxes/` with executable toolbox scripts
4. **Merge** — If existing settings files are found, hooks are merged (not overwritten)

### Agent-Specific Notes

- **Codex CLI** has limited hook support — only the `notify` key for `agent-turn-complete` events. Full lifecycle hooks are not yet available.
- **OpenCode** uses TypeScript plugins rather than JSON configuration. The script generates a `.ts` plugin that wraps the shared hook scripts.
- **Amp** uses "toolboxes" (self-describing executable scripts) instead of hooks. Set the `AMP_TOOLBOX` environment variable to the toolboxes directory to enable them.

### Examples

```bash
# Set up lint-on-edit hooks for all agents (default)
bash setup-agent-hooks.sh --project-dir /path/to/project

# Preview what would be created
bash setup-agent-hooks.sh --dry-run

# Set up test-on-write with user-level scope
bash setup-agent-hooks.sh --scope user --hook-type test-on-write

# Use a custom command
bash setup-agent-hooks.sh --hook-type custom --custom-cmd "npx prettier --check ."

# Just see which agents are installed
bash setup-agent-hooks.sh --list-agents
```

## Inputs

- **project-dir**: Path to the project where hooks should be configured
- **scope**: Whether to configure project-level or user-level hooks
- **hook-type**: Which hook preset to install
- **custom-cmd**: (Optional) Custom command when using the `custom` preset

## Output

- Shared hook scripts in `.agent-hooks/`
- Agent-specific configuration files for each detected CLI
- Summary of what was created and how to activate the hooks
