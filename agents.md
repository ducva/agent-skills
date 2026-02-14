# Agents

This file defines the agent configurations used across skills in this repository.

## Agent Types

### Researcher
- **Purpose**: Explore codebases, search for patterns, and gather context
- **Best for**: Understanding code, answering questions, finding references
- **Tools**: Read, Grep, Glob, WebSearch, WebFetch

### Implementer
- **Purpose**: Write code, create files, and make changes
- **Best for**: Building features, fixing bugs, refactoring
- **Tools**: Read, Write, Edit, Bash, Grep, Glob

### Reviewer
- **Purpose**: Review code changes, check for issues, validate quality
- **Best for**: PR reviews, code audits, security checks
- **Tools**: Read, Grep, Glob, Bash

### Planner
- **Purpose**: Design solutions, break down tasks, create implementation plans
- **Best for**: Architecture decisions, task decomposition, project planning
- **Tools**: Read, Grep, Glob, WebSearch

## Defining Custom Agents

Custom agents can be configured per-skill by specifying behavior in the skill's `prompt.md`. Key aspects to define:

1. **Role** - What the agent is responsible for
2. **Tools** - Which tools the agent should use
3. **Constraints** - Limitations or guidelines the agent must follow
4. **Output format** - How the agent should structure its responses

## Agent Composition

Skills can compose multiple agent types in sequence. For example:
1. **Researcher** gathers context about the codebase
2. **Planner** designs the implementation approach
3. **Implementer** makes the actual changes
4. **Reviewer** validates the final result

## Adding a New Agent Type

To add a new agent type:
1. Define its purpose, tools, and constraints in this file
2. Reference it in relevant skill prompts
3. Document usage patterns and examples
