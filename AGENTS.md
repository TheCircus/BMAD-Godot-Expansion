# Repository Guidelines

## Project Structure & Module Organization
- `bmad-godot-game-dev/agents/`: role briefs for team members.
- `bmad-godot-game-dev/checklists/`: definition‑of‑done and review checklists.
- `bmad-godot-game-dev/tasks/`: task prompts and flows used by agents.
- `bmad-godot-game-dev/templates/`: document templates (design docs, briefs, stories).
- `bmad-godot-game-dev/workflows/`: orchestrations for end‑to‑end game work.
- `bmad-godot-game-dev/data/`: knowledge base and guidelines.
- `bmad-godot-game-dev/teams/`: team presets and roles.
- `bmad-godot-game-dev/config.yaml`: pack metadata (name, version, prefix).

## Build, Test, and Development Commands
- No build step required; edit Markdown directly. Preview with your editor.
- Quick checks (optional):
  - Search for TODOs or broken refs: `rg -n "TODO|TBD|\]\(|http"`.
  - Validate formatting if you use a linter: `markdownlint "**/*.md"`.

## Coding Style & Naming Conventions
- Markdown: Title Case headings; use `-` for lists; wrap at ~100 chars.
- Filenames: kebab‑case (`game-design-doc-tmpl.md`), one topic per file.
- Paths: use relative repo paths; avoid machine‑specific or absolute paths.
- Examples and code fences: annotate language when relevant (e.g., `gdscript`).

## Testing Guidelines
- Templates: open standalone; confirm placeholders and sections render clearly.
- Checklists: ensure items are actionable and consistent across roles.
- Cross‑refs: when adding files, update any referring task, checklist, or workflow.
- Useful scans: `rg -n "\[\[|\{\{|TODO|TBD" bmad-godot-game-dev/`.

## Commit & Pull Request Guidelines
- Commits in this repo are short and imperative (e.g., "Update team file").
  Prefer concise messages; optional scope prefixes are welcome (e.g., `agents: add game-developer brief`).
- PRs should include: purpose, affected paths, before/after notes or screenshots, and links to related items in `tasks/` or `workflows/`.
- If you add or significantly change pack content, consider bumping `version` in `config.yaml` and update `README.md` as needed.

## Security & Configuration Tips
- Do not include secrets or tokens. Keep external links non‑sensitive and stable.
- Keep `config.yaml` consistent with folder contents and naming prefixes.
