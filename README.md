# Skills Repository

This repository contains a collection of agent skills for AI coding assistants. Skills are reusable instruction sets that extend your coding agent's capabilities, providing specialized workflows and best practices for software development.

## Available Skills

### 1. **Domain-Driven Design (ddd)**
**Description:** Guides Domain-Driven Design architecture across four layers (domain, application, infrastructure, presentation) organized by bounded context. Enforces dependency rules, tactical patterns, ubiquitous language, and aggregate rules. Supports greenfield and incremental legacy migration.

**When to use:** Use when building complex business applications with rich domain logic, migrating legacy systems, or establishing clean architecture patterns.

### 2. **Long-Term Memory (long-term-memory)**
**Description:** Maintains a persistent project journal in MEMORY.md across sessions. Records state, architectural decisions, discoveries, task history, and open questions; reconciles with the project's planning documents; archives old entries automatically.

**When to use:** Use for any project to maintain continuity across sessions, track architectural decisions, and preserve project knowledge.

### 3. **Pair Programming Mode (pair-mode)**
**Description:** Pair programming navigator mode. Toggles a state where the agent scaffolds structure and writes failing tests, but refuses to implement business logic — the user writes it.

**When to use:** Use when you want to actively participate in implementation while the agent provides guidance, scaffolding, and review.

### 4. **TDD Workflow (tdd-workflow)**
**Description:** Enforces strict Test-Driven Development (Red-Green-Refactor) workflow for any language or framework.

**When to use:** Use whenever implementing new features, fixing bugs, or modifying behavior in code to ensure test coverage and maintainable code.

## How to Use Skills with Vercel Labs Skills CLI

### Installation

First, install the Vercel Labs skills CLI globally:

```bash
npm install -g @vercel-labs/skills
# or
npx @vercel-labs/skills
```

### Installing Skills from this Repository

#### Option 1: Install from GitHub directly
```bash
npx skills add cesumilo/skills
```

#### Option 2: Install specific skills
```bash
# List available skills
npx skills add cesumilo/skills --list

# Install specific skills
npx skills add cesumilo/skills --skill ddd --skill tdd-workflow

# Install to specific agents (e.g., OpenCode)
npx skills add cesumilo/skills -a opencode
```

#### Option 3: Install all skills
```bash
npx skills add cesumilo/skills --all
```

### Available Agent Support

The skills CLI supports installation to various coding agents:

- **OpenCode** (`-a opencode`)
- **Claude Code** (`-a claude-code`)
- **Cursor** (`-a cursor`)
- **GitHub Copilot** (`-a github-copilot`)
- **Codex** (`-a codex`)
- And [40+ more agents](https://github.com/vercel-labs/skills#supported-agents)

### Installation Scope

- **Project scope** (default): Installs to `./.agents/skills/` within your project
- **Global scope** (`-g`): Installs to `~/.config/opencode/skills/` (OpenCode) or equivalent for other agents

## Skill Usage Examples

### Using DDD Skill
When working on a domain-driven design project, the DDD skill will:
- Guide you through bounded context identification
- Enforce four-layer architecture (domain, application, infrastructure, presentation)
- Maintain ubiquitous language glossary
- Refuse architectural violations
- Integrate with TDD workflow and long-term memory

### Using Pair Programming Mode
Activate pair mode with:
```
pair on
```

The agent will:
- Scaffold file structure and test frameworks
- Write failing tests (RED phase)
- Leave TODO comments for implementation
- Provide hints instead of solutions
- Review your implementation

Deactivate with:
```
pair off
```

### Using TDD Workflow
The TDD skill automatically enforces:
- 🔴 **RED**: Write failing test first
- 🟢 **GREEN**: Minimal implementation to pass
- 🔵 **REFACTOR**: Clean up while keeping tests green

## Skill Integration

These skills are designed to work together:

1. **DDD + TDD**: Domain layer tests are pure unit tests, application layer tests mock repositories
2. **DDD + Long-Term Memory**: Records bounded contexts, architectural decisions, and discoveries
3. **Pair Mode + TDD**: Agent writes tests, user implements to make them pass
4. **All skills**: Comprehensive workflow covering architecture, testing, memory, and collaboration

## Managing Installed Skills

```bash
# List installed skills
npx skills list

# Update skills to latest version
npx skills update

# Remove skills
npx skills remove ddd

# Find more skills
npx skills find
```

## Creating Custom Skills

You can create your own skills by following the [Agent Skills specification](https://agentskills.io):

1. Create a directory with a `SKILL.md` file
2. Add YAML frontmatter with `name` and `description`
3. Write detailed instructions for the agent
4. Install with: `npx skills add ./your-skill-directory`

Example `SKILL.md` structure:
```yaml
---
name: your-skill-name
description: What this skill does and when to use it
---

# Your Skill Name

Detailed instructions for the agent...

## When to Use

Describe scenarios where this skill should be activated.
```

## Resources

- [Vercel Labs Skills Repository](https://github.com/vercel-labs/skills)
- [Agent Skills Specification](https://agentskills.io)
- [Skills Directory](https://skills.sh)
- [OpenCode Skills Documentation](https://opencode.ai/docs/skills)

## Contributing

Skills in this repository:
- `ddd/` - Domain-Driven Design architecture
- `long-term-memory/` - Project memory management
- `pair-mode/` - Pair programming workflow
- `tdd-workflow/` - Test-Driven Development

Each skill directory contains a `SKILL.md` file with the complete skill definition.

## License

MIT