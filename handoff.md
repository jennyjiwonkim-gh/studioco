# StudioCo Handoff

## Overall Goal
StudioCo is Jenny's private, single-file React + Babel-standalone ceramic-studio
management app (`index.html` at repo root, no build step), deployed via
Netlify (auto-builds from GitHub on push to `main`), backed by Supabase for
persistence.

## Current Status (as of this session, 2026-08-06)
- Live site is still exactly commit `0bf4483` (last session's push) — **no
  app code changed this session**, docs only.
- Local commit **`75e4b44`** sits on top of that, containing this session's
  full documentation restructure (see below). **Explicitly not pushed** —
  Jenny asked directly not to push to `main` for this. Push whenever she
  says so; nothing time-sensitive about it.
- Netlify is still on the Personal plan (1,000 credits/mo). Jenny corrected
  an assumption this session: don't treat that upgrade as removing cost
  concerns — stay frugal about triggering deploys on principle, not just as
  a policy against running out. Codified in both memory
  (`feedback_frugal_netlify_credits.md`) and now `CLAUDE.md`'s "Deploy loop"
  section directly, so it's durable in the repo itself, not just my memory.

## What Changed This Session

### 1. Documentation restructure — TODO.md / PLAN.md carved out
`CLAUDE.md`'s "Outstanding work" and `handoff.md`'s "Exact Next Steps" /
"Open Question" sections were getting heavy. Split into two new files:
- **`TODO.md`** (new) — flat standing backlog, no dates/narrative. Holds the
  5 feature items formerly in `CLAUDE.md` (AI copy-tasks, freeform paste,
  storefront vibe reader, real IG/Shopify/Etsy integrations, Home screen
  content) plus the still-unresolved `savePersisted` silent-failure item.
- **`PLAN.md`** (new) — single active initiative only: the project landing
  page redesign (`ProjectDashboard`/`BoardScreen`), with the reference
  Artifact link and concrete steps. Gets replaced wholesale, not
  accumulated, when the active initiative changes.
- **`CLAUDE.md`** — "Outstanding work" replaced with a pointer to `TODO.md`.
  New **"Documentation file allocation"** section added, spelling out each
  of the four `.md` files' job and instructing: every time `handoff.md` is
  written (i.e. end of session), cross-check `TODO.md`/`PLAN.md` against
  what actually happened and reconcile them. This session is the first time
  following that instruction — checked both; no items needed updating
  (nothing on either list was touched this session).
- **`handoff.md`** — trimmed to point at `PLAN.md`/`TODO.md` instead of
  carrying "Exact Next Steps"/"Open Question" directly. This is also the
  **first commit `handoff.md` has ever had** — it existed as an untracked
  file across prior sessions until now.

### 2. Netlify credit philosophy reaffirmed
Jenny explicitly corrected the assistant for framing the Free→Personal plan
upgrade as meaning credits are "no longer a real cost concern." Standing
instruction going forward: keep minimizing deploys on principle regardless
of plan tier, keep preferring deploy previews over `main` pushes, never push
to `main` without her explicit go-ahead. See `CLAUDE.md`'s Deploy loop
section and memory `feedback_frugal_netlify_credits.md`.

### 3. Housekeeping notes (no action taken)
- `Pottery tracker landing page.zip`, `marina_vase.HEIC`, and
  `mccall_bowls.heic` — present at the start of this session, gone from disk
  by the time of the commit. Not caused by anything in this session (never
  staged/touched), never tracked by git either way. Flagging in case it's
  unexpected on Jenny's end; no cleanup performed.
- A new untracked file, `StudioCo Landing/image-gen-1.png` (dated today),
  appeared during this session — also not created by anything done here.
- Confirmed the `Pottery tracker landing page.zip` (before it disappeared)
  was a claude.ai web design-tool export (`.dc` file, `image-slot.js`/
  `support.js` scaffolding, pasted screenshots + a `studioco-v50.html` and
  `uploads/CLAUDE.md`) — not something this Claude Code session or any
  previous one produced.
- Jenny confirmed she's fine with the rest of the untracked repo contents
  (`.claude/`, `Archive/`, `Redesign/`, `SaleCo/`, `StudioCo Landing/`)
  staying as-is — no cleanup requested, nothing actioned.

## Next Steps
See `PLAN.md` for the active initiative (landing page redesign) and
`TODO.md` for the backlog. Nothing session-specific left open beyond those —
this was a pure documentation-hygiene session.

## Testing Environment Notes (carries forward)
- No Node/npm available in the shell environment as of the last session that
  needed it (2026-08-06 photo-fix session) — the esbuild syntax-check step
  from `CLAUDE.md`'s testing methodology couldn't run then. Worth
  re-checking whether Node is available next time a code change (not just
  docs) needs testing.
- A PowerShell `HttpListener` static file server was left running across
  sessions on `http://127.0.0.1:8744/`, serving the whole repo root (script
  lives in a session scratchpad, not the repo) — last confirmed live
  2026-08-06. Not touched this session (no code testing needed). Reuse
  rather than starting a new one on the same port if still alive.
- `.claude/worktrees/agile-mixing-cat/` still exists with a stale
  `index.html`/`home-redesign-preview.html` (last touched 2026-08-05) —
  `redesign-concept.html` inside it is the reference file `PLAN.md` points
  to for the landing-page redesign, so keep the worktree until that's done;
  safe to clean up after.
