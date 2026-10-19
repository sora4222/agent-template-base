# Agent instructions

These instructions apply to GitHub Copilot, OpenAI Codex, and other AGENTS.md-aware agents. Claude Code reads the equivalent guidance in `CLAUDE.md`.

## Code review rules

**Always use the `thermo-nuclear-code-quality-review` skill when reviewing code or pull requests**, including reviews of changes made in this template. Read its `SKILL.md` fully before reviewing. Evaluate structural simplification, maintainability, branching complexity, boundaries, duplicated abstractions, and changes that push files beyond 1,000 lines. Report actionable findings ordered by severity. Do not claim a review occurred if you could not load the skill.

## Skills and scenario tags

- `#code-review`, `#pr-review`, `#maintainability`: use `thermo-nuclear-code-quality-review`.
- `#unslop`, `#writing`, `#documentation`: use `unslop` when polishing prose and preserving its meaning.
- Both skills allow model invocation. Load the applicable skill based on the request, even without an explicit tag.
- Canonical skill content lives under `.agents/skills/<name>/SKILL.md`. Keep `.claude/skills/<name>/SKILL.md` identical to its canonical counterpart whenever editing a skill.
- For unfamiliar skills, validate the YAML `name` and `description` and check discovery paths for all supported agents before merging.
