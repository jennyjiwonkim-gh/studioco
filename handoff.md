# MOA Studio Handoff

## Overall Goal
MOA Studio is Jenny's private, single-file React + Babel-standalone ceramic-studio
management app (`index.html` at repo root, no build step), deployed via
Netlify (auto-builds from GitHub on push to `main`), backed by Supabase for
persistence.

## Current Status (as of this session, 2026-08-07)
- Branch **`landing-page-redesign`**, cut from `origin/main` (not from the
  older PR branch). **PR #4 is open** against `main`, deliberately not merged.
  Nothing pushed to `main` — no production deploy, no credits spent.
- Deploy preview confirmed `ready` via the Netlify API (`commit_ref`
  `d7682b3`, `error_message: null`):
  `https://deploy-preview-4--studioco-app.netlify.app` — needs Netlify SSO
  team login to open, on top of the app's own auth gate.
- The work: the pre-login public landing page has been rebuilt, and waitlist
  signups now really persist. See `PLAN.md` for the initiative.
- **Jenny has not seen the rendered page yet** — she chose the structure and
  made the design call below from descriptions, not screenshots. Her visual
  review is the immediate next step, and changes should be expected.

## What Changed This Session
1. Read Jenny's Claude Design mockup "Pottery tracker landing page".
   `WebFetch` 403s on `claude.ai/design/*`; the route that worked was the
   design tool's own file API (`OmeletteService/ListFiles` → `GetFile`,
   base64-decoded) from a logged-in browser tab. Screenshots alone would have
   lost the exact values.
2. Rebuilt `LandingScreen` in `index.html` as a full marketing page: nav, the
   original landing copy full-width on top, a product hero, a "Built for
   artists" strip, a flat recreation of Home, five alternating feature bands
   with small mock panels, a dark early-access CTA, and a footer.
3. Kept it in the app's own language rather than the mockup's — `ProjectIcon`
   vessel silhouettes instead of the mockup's generic outline icons, and
   `COLORS`/`RADIUS` tokens instead of raw hexes.
4. Wired the waitlist for real (schema documented in `CLAUDE.md`). The gotcha
   worth remembering: an RLS policy alone is **not** enough — the table
   `GRANT` is also required. The first end-to-end test failed with `42501`
   until `grant insert … to anon, authenticated` was added. Verified after
   the fix that INSERT succeeds while SELECT and DELETE are both refused.
5. Ran `studioco-design-reviewer`. It flagged the mockup's five pastel band
   tints and the decorative circle behind the hero vessel as tinted-fill
   drift against `CLAUDE.md`. Put it to Jenny; she chose **neutral
   cream/paper alternation**, so both were removed — mock cards now take the
   opposite surface of the band they sit on, so nothing is white-on-white.
   The reviewer's other finding, four hardcoded dark-theme hexes, is fixed
   via a named `LANDING_DARK_CARD` constant.

## Decisions Jenny Made This Session
- Keep **both** the old landing copy (top, full width) and the new marketing
  page (underneath) — not one replacing the other.
- Drop the "eight stages" references; the rest of the old copy stays as-is.
- Remove "Create an account", keep "Log in", make "Join the waitlist" the CTA.
- Wire the waitlist to real storage rather than faking the success state.
- Neutral cream/paper bands instead of the mockup's pastel tints.

## Next Steps
1. **Jenny reviews the rendered page** — via the PR #4 deploy preview above,
   or locally.
2. Apply whatever she wants changed, then the merge decision on PR #4.
3. Separately, **PR #3 is still open and unmerged** (see `PLAN.md`).

## Testing Environment Notes (carries forward)
- Node/npm/esbuild are still unavailable in this shell, so the esbuild
  syntax-check step from `CLAUDE.md` can't run. The substitute that works:
  serve the repo and read the browser console — a Babel syntax error surfaces
  as an `EXCEPTION` and leaves `#root` empty.
- `python` in Git Bash **and** `python.exe` in PowerShell are both the Windows
  Store alias stub, not an interpreter. Use a PowerShell
  `System.Net.HttpListener` static server instead. This session's copy went to
  the job tmp dir (port 8746) and is not durable — recreate it next time,
  it's ~40 lines.
- **New this session:** Chrome on Windows won't size a window below ~545px, so
  `resize_window` cannot actually reach the 320px breakpoint — it reports
  success while `innerWidth` stays ~545. Use a 320px-wide iframe. This is what
  caught the 22px nav overflow that eyeballing missed. Written up in
  `CLAUDE.md` along with the `minmax(0, 1fr)` and JSX-comment traps.
- Netlify deploy previews still require SSO team login to open in a browser;
  confirm builds via the Netlify MCP reader (`commit_ref` + `state`) instead.
- `.claude/worktrees/agile-mixing-cat/` still exists, untouched — it holds the
  `redesign-concept.html` reference for the still-open PR #3 initiative. Keep
  it until that lands.
