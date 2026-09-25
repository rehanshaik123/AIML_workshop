# Deploying the workshop map with Claude Code — setup guide

This is the reference for turning the "From Rules to Reasoning" workshop map artifact into a deployed website using Claude Code (CLI), connected to the empty GitHub repo. Written 2026-09-25.

## 0. Starting state
- The current artifact source is a single self-contained `index.html` (~45KB, no build step, no dependencies) — vanilla HTML/CSS/JS with inline `<script>`/`<style>`.
- It is NOT yet updated with the last round of requested design tweaks (better title than "From Rules to Reasoning", stopping fully at interactive sessions during progression instead of jumping past them, prettier/more unique node diagrams, renamed Level 5 away from "Generative AI", renamed Level 7 to mention automation + multi-agent systems, extra decoration on the outer page surface beyond the path). Decide before deploying: ship as-is and iterate live afterward, or fold those changes in first.
- Repo is empty and already connected to Claude Code.

## 1. Recommended repo structure
```
/
├── index.html              ← the workshop map (GitHub Pages serves this automatically at the repo root)
├── CLAUDE.md                ← persistent project instructions (see below) — Claude Code reads this automatically every session
├── PLAN.md                  ← Claude Code writes this itself when asked to plan; you review/approve it before execution
└── materials/                ← for later: per-level teaching content
    ├── level-0/
    ├── level-1/
    └── ...
```
No `.github/workflows/` needed — for a static single-file site, GitHub Pages' built-in "Deploy from a branch" (Settings → Pages → source: `main`, folder: `/root`) is simpler and avoids burning Actions runs on every tweak. Only add a workflow later if you want PR previews or a build step.

## 2. CLAUDE.md — put this file at the repo root
```markdown
# Workshop Map — Project Instructions

## What this is
A single-page interactive progress tracker for a 3-hour AI/ML workshop
("From Rules to Reasoning"). Students/presenter click through 8 level nodes
(Introduction → Supervised → Unsupervised → Reinforcement Learning →
Neural Networks → Generative AI/LLMs → Agentic Systems → Multi-Agent
Systems) plus 3 inline "Interactive Session" stops, marking each complete
as the live workshop progresses. Progress persists via localStorage
(key: `aiml-workshop-progress-v1`). No backend, no build step, no
frameworks — vanilla HTML/CSS/JS in one file, by design.

## Hard constraints — do not violate without asking first
- Keep it a static site with zero build step. Do not introduce a bundler,
  framework, or package.json unless I explicitly ask for one.
- Keep it a single `index.html` unless a change genuinely requires
  splitting files (e.g. adding per-level material files under `materials/`).
- Never remove: bottom-to-top level progression, the smooth curved path,
  inline interactive-session stops between levels, sound effects
  (Web Audio, synthesized — no external audio files), localStorage
  progress persistence, or the "mark complete" flow closing its modal
  immediately (no popup should block the completion animation).
- Deployment target is GitHub Pages serving from the repo root on `main`.
  Do not add a different hosting config unless asked.

## Workflow expectations
1. For any non-trivial change, write or update PLAN.md with the concrete
   steps first. Wait for my go-ahead before executing, unless I've said
   "just do it" for that specific request.
2. Make the smallest diff that accomplishes the task. Don't rewrite
   working sections while touching something unrelated.
3. After any behavioral change (not pure copy/text edits), do a basic
   sanity check before calling it done — at minimum a syntax check;
   for interaction changes, actually trace through the click/state flow
   or write a small throwaway test rather than assuming it works.
4. If something fails, breaks, or is ambiguous: STOP. Explain what
   happened in plain terms, give me 2–3 concrete options with
   trade-offs, and wait for my decision. Do not guess-and-retry, and do
   not silently pick an option for me on anything irreversible
   (force-push, deleting content, overwriting my design decisions).
5. When you need something from me to proceed (a link, a file, a
   decision), say exactly what you need and why, in one short message —
   not buried in a wall of other text.
6. Keep responses brief. I don't need a restated summary of what I
   asked — just do the work and report the outcome.

## Deployment
- GitHub Pages, source = `main` branch, root folder.
- After any push to `main`, the live URL updates automatically within
  ~1 minute. No manual deploy step needed.
```

## 3. First prompt to give Claude Code (copy-paste as-is)
```
I've added index.html (the workshop site) and CLAUDE.md to this repo.
Read CLAUDE.md first — it has the constraints and workflow rules for this
project and I want you to follow them for everything below and going
forward.

Task: get this live on GitHub Pages.

Before touching anything:
1. Write a short PLAN.md listing the exact steps (repo settings to change,
   any files to add/adjust, how we'll verify it's live). Nothing beyond
   what's needed to deploy what's already here — no redesign, no
   refactor.
2. Show me the plan and wait for my go-ahead before executing.

Once I approve, execute it, then tell me the live URL and confirm you
opened/checked it actually renders and the level nodes are clickable.

If you hit any error, permission issue, or ambiguous choice (e.g. Pages
isn't enabled and you can't toggle it via CLI) — stop, tell me plainly
what's blocking you and exactly what you need me to click or provide,
then wait.
```

## 4. Adding per-level materials later (next phase, don't do yet)
Each level in the `LEVELS` array in `index.html`'s script already has a
`resourceUrl: ''` field meant for this. Two options when ready:
- **Files in the repo** (`materials/level-1/slides.pdf` etc.) — set
  `resourceUrl` to the relative path. Works offline during the actual
  workshop, no dependency on an external account being logged in live.
  Recommended given this runs in a room with unpredictable wifi.
- **External links** (Google Slides/Docs) — set `resourceUrl` to the
  share link. Easier to keep editing after the fact, but needs internet
  and the right account signed in during the live session.
When this phase starts, give Claude Code one level at a time rather than
all 8 in one prompt — keeps each diff small and reviewable.

## 5. General efficient-prompting notes for this project
- Point back to CLAUDE.md instead of re-explaining constraints every message.
- One concrete task per message; avoid open-ended asks like "make it better."
- When resuming after a gap or interruption, say "resume from PLAN.md" — don't
  let it restart from scratch or re-derive context it already wrote down.
- Ask it to report outcomes (what changed, live URL, what to check), not to
  paste the whole file back into the chat.
