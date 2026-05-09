# agents

A collection of agent skills for AI coding assistants (Claude, Codex, Copilot CLI, and other tools that support the [Agent Skills specification](https://agentskills.io/specification)).

## What's here

Each directory under `skills/` is a self-contained skill that can be invoked by an agent when the situation it describes arises. Skills follow the [agentskills.io specification](https://agentskills.io/specification): a `SKILL.md` with YAML frontmatter (`name`, `description`) followed by Markdown instructions, optionally with `scripts/`, `references/`, or `assets/` subdirectories.

## Available skills

| Skill | Purpose |
|-------|---------|
| [`tradeoff-analysis`](skills/tradeoff-analysis/SKILL.md) | Guides a structured, collaborative trade-off analysis for technology and system-design decisions (databases, protocols, cloud services, build-vs-buy, etc.), keeping the user involved through assumptions, criteria, weights, reversibility, risks, and sensitivity checks before a recommendation is finalized. |

## Repository layout

```
agents/
└── skills/
    └── <skill-name>/
        ├── SKILL.md          # Required: frontmatter + instructions
        ├── scripts/          # Optional: executable helpers
        ├── references/       # Optional: deep-dive reference docs
        └── assets/           # Optional: templates, data files
```

## Installing a skill

Copy (or symlink) the skill directory into your agent's skills location. Common locations:

- **Claude Code:** `~/.claude/skills/`
- **Codex / Copilot CLI:** `~/.agents/skills/`

Example:

```bash
# Clone this repo
git clone https://github.com/bpinhosilva/agents.git

# Symlink a skill into your agent's skills directory
ln -s "$PWD/agents/skills/tradeoff-analysis" ~/.agents/skills/tradeoff-analysis
```

Your agent will pick up the skill on its next invocation (some agents require a reload).

## Authoring new skills

Skills in this repo are developed test-first, following the approach in the [`writing-skills`](https://github.com/obra/superpowers) methodology:

1. **RED** — run a realistic scenario against an agent *without* the skill and document the gaps
2. **GREEN** — write the minimal `SKILL.md` that closes those gaps
3. **REFACTOR** — rerun the scenario and close any remaining loopholes

Every skill must:

- Conform to the [agentskills.io specification](https://agentskills.io/specification) (`name` matches directory name; `description` covers both what and when; SKILL.md under 500 lines / ~5k tokens)
- Have a `description` that includes concrete triggering conditions so agents can decide when to load it
- Be tested end-to-end with a subagent before being merged

## License

MIT — see [LICENSE](LICENSE).
