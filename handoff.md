# MOA Studio Handoff

## Overall Goal
MOA Studio is Jenny's private, single-file React + Babel-standalone
ceramic-studio management app (`index.html` at repo root, no build step),
deployed via Netlify (auto-builds from GitHub on push to `main`), backed by
Supabase for persistence.

## Current Status (2026-08-07)
- **Everything shipped.** `main` is at `24108bb`, the merge of PR #5. The
  production deploy is confirmed live via the Netlify API — `context:
  production`, `state: ready`, `error_message: null`, `commit_ref: 24108bb`,
  published 17:52.
- Live at `https://studioco-app.netlify.app`. **The URL keeps the old name on
  purpose** — that is the Netlify site name, and changing it would break the
  domain. Same for the GitHub repo.
- **No open PRs.** #5 and #4 merged; #3 auto-closed once its commits landed in
  `main` via #5.
- **No active initiative.** See `PLAN.md` — the next one hasn't been chosen.

## What Shipped This Session

### 1. Public landing page + waitlist
The pre-login screen was rebuilt from Jenny's design: nav, hero with the Home
screen recreated over a photograph, "One studio, one place." feature strip, a
project-detail mock, dark early-access band, footer. Cormorant Garamond for
headlines; Inter still carries everything else.

Waitlist emails really persist, to a `waitlist_signups` table with an anon
INSERT-only grant and no read path. This is the only write in the app that runs
without a session. See `CLAUDE.md` for the schema and the RLS-plus-GRANT
gotcha.

**The public face of the site is now a waitlist page, not a login box.** That
was deliberate, but it is the first time it has been live.

### 2. Project-detail redesign
Back arrow, project switcher, tab strip, Product folded into Overview, gallery
hero + grid, shared Tags library. Built in a previous session, verified and
shipped in this one.

### 3. Renamed StudioCo → MOA Studio
Product name only. The identifiers that were deliberately left alone are listed
in `CLAUDE.md` — do not "tidy" them later.

## Decisions Jenny Made This Session
- Keep both the original landing copy and the new marketing page, stacked.
- Cormorant Garamond for headlines; drop the mockup's pastel band tints in
  favour of neutral cream/paper alternation.
- Wire the waitlist to real storage rather than faking the success state.
- Ship via one combined PR rather than merging #3 and #4 separately — one
  production deploy instead of two.

## Verification Done
- Console clean and **zero horizontal overflow at 1440, 1300, 884, 390 and a
  true 320px viewport**, checked repeatedly through the session.
- The authenticated click-through that had been outstanding on #3 since before
  this session is **closed**. Jenny confirmed the auth gate on the deploy
  preview (signed in → studio, signed out → landing page); the project-detail
  screens were then verified at 1280/390/320 against a scratch copy with the
  auth gate bypassed, served outside the repo and deleted afterwards.
- Caveat worth keeping: that scratch run used the seeded demo projects, not
  real data. Layout is confirmed; behaviour against real projects rests on
  Jenny's sign-in.

## Housekeeping Left Over
- **Stale remote branches**: `landing-page-redesign`, `preview/combined`,
  `project-header-tabs-redesign`, `test-preview-deploy`. All merged or dead.
  Not deleted — say the word.
- **Jenny's main checkout** was on `project-header-tabs-redesign`. It needs
  `git checkout main && git pull`.
- `.claude/worktrees/agile-mixing-cat/` held `redesign-concept.html`, the
  reference for the project-detail work. That initiative has shipped, so the
  worktree is now safe to remove.

## Testing Environment Notes (carries forward)
- Still no Node/npm/esbuild in this shell, and `python` is the Windows Store
  stub in both Git Bash and PowerShell. The working substitute is a PowerShell
  `HttpListener` static server plus reading the browser console — a Babel
  syntax error surfaces as an `EXCEPTION` and leaves `#root` empty. The server
  scripts live in a job-scoped tmp dir and are not durable; recreate them.
- Chrome on Windows will not size a window below ~545px, so `resize_window`
  cannot test 320px — use a 320px-wide iframe. This caught two real bugs this
  session that eyeballing missed.
- To inspect logged-in screens without credentials, serve a copy of
  `index.html` with the `App()` session gate stubbed out. It boots on seeded
  data with a "saving unavailable" banner. Keep that copy outside the repo so
  it can never be committed.
- Netlify deploy previews need SSO team login; confirm builds via the Netlify
  MCP reader (`commit_ref` + `state`) rather than by opening the URL.
