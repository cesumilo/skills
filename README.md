# Skills

Reusable instruction sets for AI coding agents. Each skill is a directory with
a `SKILL.md` that the agent loads when the task matches.

## Install

```bash
npx skills add cesumilo/skills            # all skills
npx skills add cesumilo/skills --skill learn --skill design-interview
npx skills add cesumilo/skills -a opencode   # target an agent
npx skills add cesumilo/skills -g            # global scope
```

Works with OpenCode, Claude Code, Cursor, GitHub Copilot, Codex, and
[40+ more agents](https://github.com/vercel-labs/skills#supported-agents).

## Skills

| Skill | What it does | When to use |
| --- | --- | --- |
| [learn](learn/SKILL.md) | Socratic ladder for any topic; never gives the answer or a copyable example. | "help me learn X", "explain Y", "I'm trying to understand Z" |
| [design-interview](design-interview/SKILL.md) | Walks the design tree one decision at a time, keeps `GLOSSARY.md` current, records hard trade-offs as ADRs. | "interview me", "let's design", reviewing a plan |
| [premortem-this](premortem-this/SKILL.md) | Assumes the plan already failed and works backward; separates known facts from assumptions and speculation. | before a launch, a major decision, or after a design interview |
| [ai-native-sdlc](ai-native-sdlc/SKILL.md) | Intent, spec, plan, code and tests, pull request. Each stage commits; each phase ships as a stacked PR. | feature work, refactors, bug fixes |
| [human-readable-code](human-readable-code/SKILL.md) | Line rhythm, flat control flow, narrative order, naming, comments. Defers to the project formatter. | writing, refactoring, or reviewing code |

## How they compose

`learn` → `design-interview` → `premortem-this` → `ai-native-sdlc`

The interview clarifies the decisions, the premortem stress-tests the spec, and
the SDLC ships it. `human-readable-code` applies during the code stage.
`learn` supports any step that needs a concept explained.

## Manage

```bash
npx skills list      # installed skills
npx skills update    # update to latest
npx skills remove learn
npx skills find      # discover more
```

## Add your own

Create a directory with a `SKILL.md` that has `name` and `description`
frontmatter, then run `npx skills add ./your-skill-directory`. See the
[Agent Skills specification](https://agentskills.io).

## Resources

- [Vercel Labs Skills](https://github.com/vercel-labs/skills)
- [Agent Skills Specification](https://agentskills.io)
- [Skills Directory](https://skills.sh)
- [OpenCode Skills Documentation](https://opencode.ai/docs/skills)

## License

MIT
