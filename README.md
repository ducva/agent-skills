# agent-skills

A collection of reusable agent skills (custom slash commands) for Claude Code.

## Getting Started

### Creating a New Skill

1. Copy the template:
   ```bash
   cp -r skills/_template skills/my-new-skill
   ```

2. Edit `skills/my-new-skill/skill.json` with your skill's metadata

3. Write your prompt in `skills/my-new-skill/prompt.md`

4. Test locally, then commit

### Skill Structure

Each skill lives in its own directory under `skills/`:

```
skills/my-skill/
├── prompt.md      # Main prompt template
├── skill.json     # Metadata (name, description, version, tags)
└── README.md      # Optional detailed documentation
```

## Repository Layout

| Path | Description |
|------|-------------|
| `CLAUDE.md` | Project guidelines for Claude Code |
| `agents.md` | Agent type definitions and configurations |
| `skills/` | Individual skill modules |
| `skills/_template/` | Starter template for new skills |
| `shared/prompts/` | Reusable prompt fragments |

## Contributing

1. Create your skill directory under `skills/`
2. Follow the conventions in `CLAUDE.md`
3. Ensure `skill.json` has all required fields
4. Submit a pull request