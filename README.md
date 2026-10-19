# agent-template-base

Reusable agent skills and Markdown instructions for GitHub Copilot, OpenAI Codex, and Claude Code.

## Skills and scenario tags

| Skill | Tags | Use |
| --- | --- | --- |
| [unslop](.agents/skills/unslop/SKILL.md) | `#unslop` `#writing` `#documentation` | Remove AI writing patterns while preserving meaning. Adapted from [pstack](https://github.com/cursor/plugins/blob/main/pstack/skills/unslop/SKILL.md); model invocation enabled. |
| [thermo-nuclear-code-quality-review](.agents/skills/thermo-nuclear-code-quality-review/SKILL.md) | `#code-review` `#pr-review` `#maintainability` | Strict maintainability and architecture review. Adapted from [Cursor team kit](https://github.com/cursor/plugins/blob/main/cursor-team-kit/skills/thermo-nuclear-code-quality-review/SKILL.md). |

## Agent compatibility

| Agent | Skill discovery path | Always-on instructions |
| --- | --- | --- |
| OpenAI Codex | `.agents/skills/<name>/SKILL.md` | `AGENTS.md` |
| GitHub Copilot | `.agents/skills/<name>/SKILL.md` | `.github/copilot-instructions.md`; `AGENTS.md` where supported |
| Claude Code | `.claude/skills/<name>/SKILL.md` | `CLAUDE.md` |

`.agents/skills` contains the canonical skills, with identical files under `.claude/skills` to support Claude discovery. When changing skills, update both locations.

## Usage

Skill frontmatter describes when each skill should be selected automatically. Optional scenario tags give extra routing hints:

- `#unslop Revise this README to remove generic AI phrasing.`
- `#code-review Review this PR with thermo-nuclear standards.`

**Every code review must use the thermo-nuclear code quality review skill**, even without a tag. This is required by [AGENTS.md](AGENTS.md), `CLAUDE.md`, and the Copilot instructions.

Skill discovery and automatic invocation can vary by agent version, mode, and settings. All three agent clients have not been tested live with this template.
