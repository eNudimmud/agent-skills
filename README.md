# NeukoAI Agent Skills

AI entertainment stack — give your agent superpowers to create and sustain attention.

A public skill library for AI agents. Skills provide self-contained instructions that enable agents to interact with onchain protocols and APIs.

## Available Skills

| Skill | Description | Status |
|-------|-------------|--------|
| [blowfish-launch-token](./blowfish-launch-token/) | Launch Solana tokens via the Blowfish Agent API | Active |
| [character-image-studio](./character-image-studio/) | Manage character visual identities and generate consistent images via the NeukoAI Image Studio API | Active |

## Quick Start

Point your agent at this repository to browse and install skills.

## Skill Structure

Each skill is a directory containing:

```
skill-name/
├── SKILL.md              # Core skill instructions (required)
├── references/           # Supplementary documentation (optional)
│   ├── topic-a.md
│   └── topic-b.md
└── scripts/              # Executable helper scripts (optional)
    └── helper.sh
```

### SKILL.md Format

Every `SKILL.md` includes YAML frontmatter and a markdown body:

```yaml
---
name: skill-name
description: What this skill does and when to use it.
metadata: {"neuko": {"emoji": "🎯", "homepage": "https://..."}}
---
```

The markdown body contains everything an AI agent needs to execute the skill: endpoints, authentication flows, code examples, error handling, and step-by-step workflows.

## Contributing

1. Fork this repository
2. Create a directory for your skill (lowercase, hyphenated)
3. Add a `SKILL.md` with frontmatter and complete instructions
4. Optionally add `references/` and `scripts/` directories
5. Submit a pull request

## License

MIT
