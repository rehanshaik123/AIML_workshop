# PLAN — Deploy workshop map to GitHub Pages

Scope: deploy the existing `index.html` as-is. No redesign, no refactor, no file changes to the site.

## Current state (checked)
- `index.html` is already on `main` at the repo root (identical to this branch).
- Repo is **public** → GitHub Pages works on the free plan.
- Pages is **not enabled** yet (`has_pages: false`).
- `index.html` has no Jekyll front matter / Liquid tags → Pages serves it verbatim. No `.nojekyll`, no workflow needed.

## Steps
1. **You (repo owner) enable Pages** — I can't toggle this from here (no Pages-settings API access in this session).
   GitHub → `rehanshaik123/AIML_workshop` → **Settings → Pages** →
   Source: **Deploy from a branch** · Branch: **main** · Folder: **/ (root)** → **Save**.
2. **I confirm the build** via the public GitHub API: `has_pages: true` and a successful `github-pages` deployment on `main`'s latest commit.
3. **I verify the site works**:
   - Try loading the live URL directly.
     Note: this sandbox's network proxy currently blocks `*.github.io` (HTTP 403), so this may fail.
   - Fallback: load the exact `main` `index.html` in headless Chromium, confirm it renders, click level nodes, confirm the modal opens and "mark complete" updates state/localStorage.
   - If the direct load is blocked, you do a final 10-second eyeball of the live URL.
4. **Report** the live URL: `https://rehanshaik123.github.io/AIML_workshop/`

## Files touched
- `PLAN.md` only (this file), on branch `claude/affectionate-fermi-zjdfj7`. Nothing pushed to `main`.
