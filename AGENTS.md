# PRISE Lab Website Guidance

## Project Context
- This is the PRISE Lab website repo for https://prise-lab.com.
- Stack: Quarto static site, GitHub Pages, Namecheap domain.
- GitHub Pages serves the rendered `docs/` directory from `main`.
- Deployment is manual; there is no CI, no GitHub Actions deploy, and no `gh-pages` branch workflow.
- `AGENTS.md` is the Codex-facing guide. `CLAUDE.md` is legacy source material with useful failure-mode history; read both before changing site structure or deployment behavior.

## Required Deploy Workflow
1. Edit source files: `.qmd`, `_quarto.yml`, `styles.css`, or source images.
2. Run `quarto render` from the repo root.
3. Inspect `git status`.
4. Verify the relevant rendered file under `docs/` contains the change.
5. Commit and push.

Do not finish a website-edit task after changing only source files; pushing the rendered output is what deploys the site.

## Guardrails
- Never run `quarto publish`; it targets a separate branch and has previously caused broken output.
- Never manually edit, move, or delete files inside `docs/`.
- If `git status` shows deletions under `docs/site_libs/`, stop and restore those files before committing.
- The post-render script creates `docs/.nojekyll` and `docs/CNAME`; do not hand-maintain them.
- Keep `.qmd` files in LF line endings. Convert CRLF before committing if a diff shows whole-file churn.
- Do not stage `.quarto/`.
- Guidance-only edits such as this file should still be committed and pushed from this repo, but they do not require `quarto render` unless Quarto source files changed.

## Content Conventions
- Top-level `.qmd` files become navbar pages.
- `projects/` holds standalone project or opportunity posts.
- `get-involved.qmd` surfaces opportunity posts via `.opportunity-card` blocks styled in `styles.css`.
- Put source images in `images/`; Quarto copies referenced images into `docs/images/`.
- Team-page photos use lowercase filenames in `images/`, for example `litzow.jpg` or `abhyankar.jpg`.
- Source headshots and bios live upstream in `../orga/team/` using the `LASTNAME_First.jpg` plus `firstnamelastname.txt` convention.
- News entries are descending chronological under `## <year>` and use `**Month Year** - sentence with [Name](team.qmd) links and a trailing period.`
- Coordinate with Elías or Erin before adding new public team members or opportunities.

## Public Copy
- Do not mention internal drafting tools or process in public files.
- Spell `Elías` with the accent in visible prose and bios.
