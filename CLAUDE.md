# Jingle Workflow Marketplace

This is a **monorepo** hosting Claude Code plugins for the Jingle team. Each plugin lives under `plugins/<plugin-name>/` with its own manifest, skills, and CLAUDE.md.

## Monorepo structure

```
.
├── .claude-plugin/
│   └── marketplace.json       # Marketplace catalog — lists all plugins
├── CLAUDE.md                   # This file (repo conventions)
├── README.md                   # Install & usage guide
└── plugins/
    ├── jingle-workflow/        # First plugin
    │   ├── .claude-plugin/
    │   │   └── plugin.json
    │   ├── CLAUDE.md
    │   └── skills/
    │       └── <skill-name>/
    │           └── SKILL.md
    └── <future-plugin>/        # Add more plugins here
```

## Conventions

- **Plugin names** use kebab-case: `jingle-workflow`, `code-quality`, etc.
- **Skill names** use kebab-case: `review-requirements`, `deploy-check`, etc.
- **Each plugin** must have `.claude-plugin/plugin.json` and its own `CLAUDE.md`.
- **Skills** go in `plugins/<plugin>/skills/<skill-name>/SKILL.md`, never inside `.claude-plugin/`.
- **Report-producing skills** write output to `./review-reports/` in the user's project directory (created at runtime).

## Adding a new plugin

1. Create `plugins/<plugin-name>/`
2. Add `.claude-plugin/plugin.json` with `name`, `description`, `version`
3. Add `CLAUDE.md` with plugin-scoped context
4. Add skills under `skills/<skill-name>/SKILL.md`
5. Register the plugin in `.claude-plugin/marketplace.json` → `plugins` array
6. Validate: `claude plugin validate .`

## Adding a skill to an existing plugin

1. Create `plugins/<plugin-name>/skills/<skill-name>/SKILL.md`
2. Reload in Claude Code: `/reload-plugins`

## Local testing

```bash
# Test a single plugin directly
claude --plugin-dir ./plugins/jingle-workflow

# Test via the marketplace
# In Claude Code:
#   /plugin marketplace add /path/to/this/repo
#   /plugin install jingle-workflow@jingle-workflow-marketplace
```

## Validation

```bash
claude plugin validate .
```
