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

### 5. **Code Quality (code-quality)**
**Description:** Proactively guides and enforces code quality practices: eliminating magic numbers and strings, constructor-based dependency injection, and thorough documentation. Flags violations as they are noticed, plans safe incremental refactors.

**When to use:** Use for maintaining high code quality standards, refactoring legacy code, or establishing best practices in new projects.

### 6. **File Writing (file-writing)**
**Description:** Governs how the agent writes files. Enforces chunked, sequential writes to prevent silent truncation from buffer/context limits.

**When to use:** Use for all file writing operations to ensure reliable, safe file creation and modification.

### 7. **Problem Solving (problem-solving)**
**Description:** Governs the agent's internal reasoning process and communication style. Enforces hypothesis-driven, test-first, iterate-fast problem solving. Eliminates silent internal loops, premature abstraction, and invisible approach changes.

**When to use:** Use for any non-trivial problem solving to ensure transparent, efficient reasoning and communication.

### 8. **Design Interview (design-interview)**
**Description:** Runs structured design interviews to reach shared understanding of plans, features, or systems. Walks design trees branch by branch, resolving decision dependencies, sharpening fuzzy language, and stress-testing with concrete scenarios. Inspired by [Matt Pocock's grill-with-docs skill](https://github.com/mattpocock/skills/blob/main/skills/engineering/grill-with-docs/SKILL.md).

**When to use:** Use when designing new features, reviewing plans, or needing to clarify complex requirements before implementation.

### 9. **Premortem (premortem-this)**
**Description:** Stress-tests any plan, project, or decision by imagining it has already failed and working backward to discover why. Surfaces hidden assumptions, weak decisions, and missing risks before resources are committed. Inspired by Gary Klein's premortem technique.

**When to use:** Use before launching a product, starting a complex project, making a major architectural decision, or after a design-interview session to stress-test the clarified plan.

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

### Using Code Quality Skill
The code-quality skill continuously:
- 🔍 Scans for magic numbers and strings
- 🏗️ Enforces constructor-based dependency injection
- 📝 Ensures thorough documentation
- 📋 Plans safe, incremental refactoring
- ⚠️ Flags violations as they're discovered

### Using File Writing Skill
The file-writing skill ensures:
- 📏 No more than 50 lines per write operation
- 🔁 Sequential chunked writes from beginning to end
- ✅ Verification after each chunk
- 📋 Clear communication about what was written and what remains

### Using Problem Solving Skill
The problem-solving skill enforces:
- 🧠 One hypothesis at a time approach
- ⚡ Maximum 2 internal iterations before asking for help
- 📐 Decision format with risk assessment and test verification
- ❓ Explicit questions when torn between approaches
- 🔄 Transparent approach changes instead of silent restarts

### Using Design Interview Skill
The design-interview skill provides:
- 🔍 One question at a time with recommended answers
- 🌳 Design tree walking to resolve decision dependencies
- 📝 Sharpening fuzzy language into canonical terms
- 🧪 Stress-testing with concrete scenarios
- 📋 ADR creation only for hard-to-reverse, surprising, trade-off decisions

## Skill Integration

These skills are designed to work together:

1. **DDD + TDD**: Domain layer tests are pure unit tests, application layer tests mock repositories
2. **DDD + Long-Term Memory**: Records bounded contexts, architectural decisions, and discoveries
3. **Pair Mode + TDD**: Agent writes tests, user implements to make them pass
4. **Code Quality + All skills**: Enforces quality standards across all workflows
5. **File Writing + All skills**: Ensures safe, reliable file operations across all workflows
6. **Problem Solving + All skills**: Provides transparent reasoning and efficient decision-making
7. **Design Interview + TDD**: Interview decisions become acceptance criteria for failing tests
8. **Design Interview + Premortem**: Design-interview clarifies the plan, then premortem stress-tests it
9. **Premortem + Long-Term Memory**: Record premortem findings as architectural decisions in MEMORY.md
10. **Premortem + DDD**: Stress-test bounded context boundaries and aggregate designs before implementation
11. **All skills**: Comprehensive workflow covering architecture, testing, quality, reasoning, memory, design, and collaboration

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
- `code-quality/` - Code quality and refactoring guidance
- `file-writing/` - Safe file writing and chunking
- `problem-solving/` - Transparent reasoning and decision-making
- `design-interview/` - Structured design interview process
- `premortem-this/` - Premortem stress-testing of plans and decisions

Each skill directory contains a `SKILL.md` file with the complete skill definition.

## License

MIT
