# jingle-workflow

Common product & engineering workflows for Jingle teams.

## Skills

| Skill | Trigger | Description |
|-------|---------|-------------|
| `review-requirements` | `/jingle-workflow:review-requirements` | Multi-dimensional PRD review with dual reports (dev + product) |

## Adding new skills

1. Create a folder under `skills/<skill-name>/`
2. Add a `SKILL.md` with frontmatter (`name`, `description`) and step-by-step instructions
3. Run `/reload-plugins` in Claude Code to pick it up

## Output conventions

- All report-generating skills write output to `./review-reports/` in the user's current project directory.
- Filenames include the date to avoid overwriting: `<type>-YYYY-MM-DD.md`.
- If the file already exists, append a sequence suffix (`-2`, `-3`, etc.).
- Always print a brief in-chat summary after writing files.
