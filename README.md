# Skills Repository

This repository contains a collection of agent skills for AI coding assistants. Skills are reusable instruction sets that extend your coding agent's capabilities, providing specialized workflows and best practices for software development.

## Available Skills

### 1. **Learn (learn)**
**Description:** Socratic learning guide for programming and software engineering topics. Uses a 3-step ladder (explore → concepts → different example) to guide you toward the answer without giving it directly. Supports official docs, pedagogical sources, and domain-distance anti-example rules.

**When to use:** Use with explicit triggers like "help me learn X", "explain Y", or "I'm trying to understand Z".

### 2. **File Writing (file-writing)**
**Description:** Governs how the agent writes files. Enforces chunked, sequential writes to prevent silent truncation from buffer/context limits.

**When to use:** Use for all file writing operations to ensure reliable, safe file creation and modification.

### 3. **Design Interview (design-interview)**
**Description:** Runs structured design interviews to reach shared understanding of plans, features, or systems. Walks design trees branch by branch, resolving decision dependencies, sharpening fuzzy language, and stress-testing with concrete scenarios. Inspired by [Matt Pocock's grill-with-docs skill](https://github.com/mattpocock/skills/blob/main/skills/engineering/grill-with-docs/SKILL.md).

**When to use:** Use when designing new features, reviewing plans, or needing to clarify complex requirements before implementation.

### 4. **Premortem (premortem-this)**
**Description:** Produces a premortem for a plan, project, or decision by imagining that it already failed and working backward to find the causes. The agent asks for the information it needs, then writes the full analysis alone. The report covers hidden assumptions, weak decisions, missing risks, root causes, and ranked recommendations. Inspired by Gary Klein's premortem technique.

**When to use:** Use before launching a product, starting a complex project, making a major architectural decision, or after a design-interview session to stress-test the clarified plan.

### 5. **AI-native SDLC (ai-native-sdlc)**
**Description:** Teaches the agent to work with the artifact chain. Every change moves through intent, spec, plan, code with tests, and a pull request. Each stage commits a versioned artifact that the next stage reads, and the commit history is the audit trail. Covers plan-before-code, the feedback loop, institutional knowledge as files, hooks as approval gates, closing the loop, and the metrics per stage.

**When to use:** Use for feature work, refactors, and bug fixes in a repository that follows the AI-native SDLC. Derived from Anthropic's AI-native SDLC playbook and applied in the rove repository.

### 6. **Human-Readable Code (human-readable-code)**
**Description:** Enforces strict visual rhythm, line-breaking, flat control flow, and natural naming so code reads cleanly for humans. Covers 80-character soft wrapping, one idea per line, paragraph spacing, early returns, flat nesting, top-down reading order, and high-signal comments.

**When to use:** Use for any code-writing or refactoring task where clarity for human readers matters more than terseness.

## How to Use Skills with Vercel Labs Skills CLI

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
npx skills add cesumilo/skills --skill learn --skill file-writing

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

### Using File Writing Skill
The file-writing skill ensures:
- 📏 No more than 50 lines per write operation
- 🔁 Sequential chunked writes from beginning to end
- ✅ Verification after each chunk
- 📋 Clear communication about what was written and what remains

### Using Design Interview Skill
The design-interview skill provides:
- 🔍 One question at a time with recommended answers
- 🌳 Design tree walking to resolve decision dependencies
- 📝 Sharpening fuzzy language into canonical terms
- 🧪 Stress-testing with concrete scenarios
- 📋 ADR creation only for hard-to-reverse, surprising, trade-off decisions

### Using Premortem Skill
The premortem-this skill provides:
- 📥 A mandatory information-gathering step before the analysis starts
- 🧠 Autonomous production of the failure analysis, with no discovery questions
- 🔎 Root cause tracing with the Five Whys method
- 📊 Ranked recommendations, each traced to a root cause
- 📋 A single structured premortem report

### Using AI-native SDLC Skill
The ai-native-sdlc skill provides:
- 🔗 The five-stage artifact chain: intent, spec, plan, code and tests, pull request
- 📝 Plan before code, with an approved plan committed first
- 🔁 A feedback loop so each session checks its own work
- 🛡️ Hooks as approval gates and a production gate the agent cannot cross
- 📈 A leading and a lagging indicator for each stage

### Using Human-Readable Code Skill
The human-readable-code skill provides:
- 📏 An 80-character soft wrap with one idea per line
- 🌬️ Paragraph spacing around blocks and grouped logic
- 🔀 Flat control flow with early returns over `else`
- 🧭 Top-down reading order and declare-right-before-use
- 🏷️ Human-friendly, spelled-out naming and high-signal comments

## Skill Integration

These skills are designed to work together:

1. **File Writing + All skills**: Ensures safe, reliable file operations across all workflows
2. **Design Interview + Learn**: Interview questions and design decisions can be explored with the Socratic learning ladder
3. **Design Interview + Premortem**: Design-interview clarifies the plan, then premortem-this stress-tests it
4. **AI-native SDLC + Simple English**: The intent, the spec, and the plan are written in Simple English
5. **AI-native SDLC + Design Interview + Premortem**: The interview resolves the spec decisions, and the premortem stress-tests the spec before implementation
6. **All skills**: Comprehensive workflow covering learning, design, stress-testing, artifact-chain delivery, and reliable file operations
7. **Human-Readable Code + AI-native SDLC**: The code and tests stage is written in a readable, scannable style

## Managing Installed Skills

```bash
# List installed skills
npx skills list

# Update skills to latest version
npx skills update

# Remove skills
npx skills remove learn

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
- `learn/` - Socratic learning guide
- `file-writing/` - Safe file writing and chunking
- `design-interview/` - Structured design interview process
- `premortem-this/` - Autonomous premortem stress-testing
- `ai-native-sdlc/` - Artifact-chain working method
- `human-readable-code/` - Readable, scannable code style

Each skill directory contains a `SKILL.md` file with the complete skill definition.

## License

MIT
