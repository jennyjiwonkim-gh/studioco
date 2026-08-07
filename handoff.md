# MOA Studio Handoff

## Overall Goal
MOA Studio is Jenny's private, single-file React + Babel-standalone ceramic-studio
management app (`index.html` at repo root, no build step), deployed via
Netlify (auto-builds from GitHub on push to `main`), backed by Supabase for
persistence.

## Current Status (as of this session, 2026-08-07)
- Branch `project-header-tabs-redesign`, **PR #3 open against `main`, not
  merged**. This is the active initiative from `PLAN.md` (project
  landing-page redesign).
- Commit history on the branch: `0bca32c` (header redesign: back arrow,
  project switcher, tab strip) → `1817695` (content platform multi-select,
  header dropdown styling) → `7e4630c` (fold Product page into Overview) →
  `9ffd81f` (Gallery hero+2x2 layout, Care & safety → shared Tags library) →
  `87c1746` (this session: Gallery mobile-responsive fix + hero border
  removal).
- Deploy preview confirmed live and matching the latest commit via the
  Netlify API (`commit_ref` = `87c1746...`, `state: ready`,
  `error_message: null`): `https://deploy-preview-3--studioco-app.netlify.app`.

## What Changed This Session
1. Ran `studioco-design-reviewer` against the most recently landed chunk
   (`9ffd81f`, Gallery hero+2x2 + Tags library) — no design-system
   violations (accent hues, `border-radius: 3px`, project-identity treatment
   all clean, `MultiLibraryPickerField` reuse for Tags is exactly the kind
   of pattern-reuse `CLAUDE.md` asks for). It did flag that `ProjectGallery`
   was the one multi-column section in the file that never branched on
   `viewport` — worth a real screenshot check.
2. Fixed: `ProjectGallery` now takes a `viewport` prop and branches like
   every other section (index.html:4779 pattern). On phone widths the hero
   photo goes full-width on top (`3 / 2` aspect ratio) with secondary photos
   flowing 2-up beneath it, instead of squeezing 3 explicit grid columns
   into a ~320px panel — secondary thumbnails were ~68px square, now
   ~148px. Desktop layout (`2fr 1fr 1fr`, hero spanning 2 rows) is
   byte-for-byte unchanged.
3. Removed the 2px black (`COLORS.ink`) border that singled out the hero
   photo tile in `GalleryTile` (Jenny's call) — now uses the same 1px
   hairline border as every other tile, on both phone and desktop.
4. Verified: Babel/console syntax check clean via a local PowerShell static
   server + Chrome (no Playwright click-through this session — see Testing
   Environment Notes). Built a standalone, isolated CSS-grid Artifact
   preview (old vs. new grid at 320px, plus unchanged desktop) since the
   real Gallery view sits behind Supabase login and can't be reached without
   Jenny's credentials.
5. Committed (`87c1746`) in a worktree branch off `project-header-tabs-redesign`,
   then pushed directly onto that branch (not a new PR) to update the
   existing open PR #3. Confirmed via the Netlify MCP reader that the
   resulting deploy-preview build succeeded and contains the right commit.

## Next Steps
See `PLAN.md` — the header/tabs + project-detail redesign initiative
continues; PR #3 has not been merged to `main`. Outstanding before that
decision: a full authenticated click-through (desktop + 390/320px) against
the deploy preview or local server, which needs Jenny present to log in.

## Testing Environment Notes (carries forward)
- Node/npm/esbuild are still unavailable in this shell — same as prior
  sessions, the esbuild syntax-check step from `CLAUDE.md` can't run.
- **New this session:** `python` in Git Bash resolves to the Windows Store
  app-execution-alias stub, not a real interpreter — it prints an install
  prompt and exits rather than serving anything. Use PowerShell's
  `python.exe` directly, or (more reliably) a small PowerShell
  `System.Net.HttpListener` static-file-server script — used this session
  on `127.0.0.1:8745`, confirmed working. The script itself was written to
  a job-scoped tmp path, not the repo, so it isn't durable — recreate it
  next session if needed (a few lines, see this session's transcript or
  just ask).
- Gallery/Project-detail views require an authenticated Supabase session to
  reach at all — can't screenshot or click-through them without Jenny's
  login. Worked around this session by building an isolated CSS-only
  reproduction of just the grid layout in question (Artifact), using the
  exact style values from the diff, rather than the real rendered page.
- **New this session:** Netlify's non-production deploy previews require
  SSO team login to open directly in a browser
  (`netlify-project-services-reader` reports
  `requiresSSOTeamLogin: "non_production"`), on top of the app's own
  Supabase auth gate. Confirming a preview build succeeded and contains the
  right commit is best done via the Netlify MCP tools
  (`netlify-deploy-services-reader` → `get-deploy-for-site`, checking
  `commit_ref` and `state`) rather than by opening the URL directly.
- `.claude/worktrees/agile-mixing-cat/` still exists, untouched this
  session — `redesign-concept.html` inside it is still the reference file
  `PLAN.md` points to for the landing-page redesign. Keep until that
  initiative is done; safe to clean up after.
