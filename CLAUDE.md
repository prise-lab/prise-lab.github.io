# PRISE Lab website (Quarto + GitHub Pages)

This repository is the PRISE Lab website (https://prise-lab.com). It is a **manually-deployed** Quarto static site served from `docs/` on `main`. There is no CI, no GitHub Actions, and no `gh-pages` branch — every change must be rendered locally and pushed.

## Deploy workflow (do not deviate)

```
1. Edit .qmd / _quarto.yml / styles.css / images/  (sources)
2. quarto render                                    (rebuilds all of docs/)
3. git status   # sanity check — see "guardrails" below
4. git add <changed files>
5. git commit -m "..."
6. git push                                         (this is what deploys)
```

The post-render script (`post-render.sh`, configured in `_quarto.yml`) creates `docs/.nojekyll` and `docs/CNAME` automatically. You don't need to touch those.

## Guardrails — read before touching `docs/`

These are the failure modes that have already happened on this repo. Avoid them.

1. **Never run `quarto publish`.** It targets a separate `gh-pages` branch that this site does not use, and it can wipe `docs/site_libs/`. Always run plain `quarto render` from the repo root.

2. **Never manually delete, move, or hand-edit anything inside `docs/`.** That directory is fully managed by `quarto render`. In particular, `docs/site_libs/` contains Bootstrap CSS, Quarto JS (nav, search, syntax highlighting, tippy, popper, anchor, headroom, clipboard, tabsets) — every HTML file references these by relative path. If `site_libs/` is missing, the live site loses all styling, navigation, and search.

3. **After rendering, check `git status` before committing.** If you see deletions under `docs/site_libs/`, **stop** — your render did not complete cleanly. Recover with `git checkout -- docs/site_libs/` and re-run `quarto render`. Do not commit a status that deletes those files.

4. **Verify the rendered HTML actually contains your changes** before committing. For example, after editing `news.qmd`, grep `docs/news.html` for a phrase from your new entry. A stale `docs/*.html` means the render didn't fire (or fired against the wrong output dir) and the deploy will not reflect your edit.

5. **Save `.qmd` files with LF line endings, not CRLF.** Windows editors (Notepad, some VS Code defaults) save as CRLF, which makes the entire file show as modified in `git diff` even when only one line changed. Configure your editor to use LF for this repo, or run `dos2unix file.qmd` (or `perl -i -pe 's/\r\n/\n/g' file.qmd`) before committing.

6. **Do not commit `.quarto/`.** It is local Quarto cache and is gitignored. (Historic cache files were tracked before the gitignore was added; ignore them in `git status` — do not stage them.)

7. **Editing only sources, not outputs.** If you find yourself editing a file under `docs/`, you are editing build output that will be overwritten on the next render. Edit the corresponding `.qmd` instead.

## Conventions

- **Content layout**: top-level `.qmd` files become navbar pages; `projects/` holds standalone project/opportunity posts surfaced via `.opportunity-card` blocks on `get-involved.qmd` (style defined in `styles.css`).
- **Images**: place in `images/`. Quarto copies referenced images into `docs/images/` during render. Don't add images to `docs/images/` by hand.
- **Team page photos**: in this repo, `images/<lastname>.jpg` (lowercase, e.g. `litzow.jpg`, `abhyankar.jpg`, `rogers.jpg`). Source headshots and bios live upstream in the lab's `orga/team/` folder (outside this repo) under the convention `LASTNAME_First.jpg` + `firstnamelastname.txt`. Coordinate with Elías/Erin before adding new team members.
- **News entries**: descending chronological under `## <year>`, format `**Month Year** - sentence with [Name](team.qmd) links and a trailing period.`

## If you broke something

- `docs/site_libs/` files showing as deleted in `git status` → `git checkout -- docs/site_libs/`, then re-run `quarto render`.
- An HTML file in `docs/` doesn't reflect your edit → re-run `quarto render` from the repo root (not from a subdirectory).
- A `.qmd` file shows whole-file changes in `git diff` but you only edited one line → it's a CRLF issue; convert to LF and re-diff.
- You ran `quarto publish` by mistake → check for a `gh-pages` branch and delete it locally and on GitHub if it appeared; verify `main`'s `docs/site_libs/` is intact; re-render and push.
