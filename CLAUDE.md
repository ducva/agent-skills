# CLAUDE.md - Project Guidelines for Claude Code

## Project Overview

This repository contains a collection of reusable agent skills (custom slash commands) for Claude Code. Each skill is a self-contained module with its own prompt, metadata, and optional supporting files.

## Repository Structure

```
agent-skills/
├── CLAUDE.md              # This file - project guidelines
├── agents.md              # Agent definitions and configurations
├── README.md              # Project overview and usage
├── .gitignore             # Git ignore rules
├── skills/                # Individual skill modules
│   ├── <skill-name>/      # One directory per skill
│   │   ├── prompt.md      # The skill's prompt template
│   │   ├── skill.json     # Skill metadata and configuration
│   │   └── README.md      # Skill-specific documentation
│   └── _template/         # Template for creating new skills
└── shared/                # Shared resources across skills
    └── prompts/           # Reusable prompt fragments
```

## Conventions

### Skill Naming
- Use lowercase kebab-case for skill directory names (e.g., `review-pr`, `generate-tests`)
- Skill names should be descriptive and action-oriented

### Skill Structure
Each skill directory must contain:
- `prompt.md` - The main prompt template for the skill
- `skill.json` - Metadata: name, description, version, author, tags

Optional files:
- `README.md` - Detailed documentation for the skill

### skill.json Schema
```json
{
  "name": "skill-name",
  "description": "Brief description of what the skill does",
  "version": "1.0.0",
  "author": "",
  "tags": []
}
```

### Writing Prompts
- Be specific and unambiguous in instructions
- Include examples where helpful
- Define expected inputs and outputs clearly
- Use markdown formatting for readability

## Development Workflow

1. Copy `skills/_template/` to `skills/<your-skill-name>/`
2. Edit `skill.json` with the skill's metadata
3. Write the prompt in `prompt.md`
4. Add documentation in `README.md` if the skill is non-trivial
5. Test the skill locally before committing

## Code Review Checklist

- [ ] Skill has a valid `skill.json` with all required fields
- [ ] `prompt.md` is clear and well-structured
- [ ] No sensitive data or credentials in any files
- [ ] Skill name follows kebab-case convention
