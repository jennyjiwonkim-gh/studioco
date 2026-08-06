# StudioCo Handoff

## Overall Goal
StudioCo is Jenny's private, single-file React + Babel-standalone ceramic-studio
management app (`index.html` at repo root, no build step), deployed via
Netlify (auto-builds from GitHub on push to `main`), backed by Supabase for
persistence.

## Current Status (as of this session, 2026-08-06)
- **Pushed to `main` and confirmed live**: commit `0bf4483` — last session's
  full visual redesign (Home/nav/Content Library/Contacts/Tasks/Campaign
  Library/Project View/alphabetized dropdowns) **plus** this session's photo
  data-loss fix (see below). Verified live via a direct `curl` of the
  deployed `index.html`: it now serves `heic-to`/`window.HeicTo` (the
  earlier HEIC fix, commit `7c7aa9f`, was on `origin/main` but had never
  actually gone live until this push — see "HEIC fix" below for why).
- **Netlify plan upgraded**: Jenny moved from Free → **Personal** ($9/mo,
  1,000 credits/month). No longer credit-constrained the way Free was.
- **`CLAUDE.md` updated** (committed separately if not already — check
  `git status`) with a new trap note about the narrowed git permission
  allowlist (see "Permissions" below).
- One thread is **explicitly unresolved** — see "Open Question" below.

## What Changed This Session

### 1. Photo data-loss bug — root cause corrected, then fixed
Last session's handoff blamed a Supabase payload-size limit. That was wrong.
With Jenny's OK, connected directly to the live Supabase project
(`hvqjpoqamxalitydrega`) and found the real cause: `usePersistentState`
(`index.html` ~612-631) loads each persisted key into React state once per
tab/session, and every edit debounces 300ms then overwrites the **entire**
value for that key with no version check — pure last-write-wins. A stale
session's unrelated edit could silently clobber a photo another session had
just saved. `boardContent` itself was only ~4.5KB, nowhere near a size limit,
and every save in the API logs was returning 200 — saves were succeeding,
just overwriting each other.

Jenny chose the real fix over a cheap optimistic-concurrency patch: photos
now live in a new **Supabase Storage bucket (`photos`, public, per-user
folder RLS)** instead of inline base64 in the `boardContent` blob.
- `resizeImageFile` now resolves to a JPEG `Blob` (was a base64 data URL).
- New `uploadPhotoToStorage`/`deletePhotoFromStorage` helpers hit the
  Storage REST API directly (no SDK, same `authedFetch` pattern as the rest
  of the app).
- `ProductPage`'s `handleFiles`/`removePhoto` upload/delete through Storage;
  photo objects are now `{ id, url, path }`, not `{ id, dataUrl }`. All four
  `<img src>` call sites updated accordingly.
- Verified end-to-end two ways: Jenny uploaded real photos
  (`marina_vase.HEIC`, `mccall_bowls.heic`) through the actual Product page
  UI via a local static server pointed at the real Supabase backend, and
  independently confirmed via SQL that both objects landed in the `photos`
  bucket with correct mimetype/size while `boardContent` stayed tiny.
- Full writeup in memory: `project_photo_dataloss_bug.md`.

**Not fixed / still open**: the same last-write-wins pattern still applies
to every other persisted key (`projects`, `tasks`, `contentLibrary`,
`campaignLibrary`, `contacts`) — just without the dramatic "data vanishes"
symptom since none of them embed large binary blobs. Out of scope this
session; flag if it comes up again.

### 2. Netlify billing investigation
Jenny's Free plan hit its credit limit ~2 days after account creation.
Traced it precisely via the connected Netlify account: **21 production
deploys × 15 credits each = 315 credits**, exhausting the 300/month Free
allotment almost exactly. **Not** Agent Runners (0 credits consumed — that
was a red herring from an earlier tangent) — pure deploy volume, since every
`git push` to `main` triggers one full-price production deploy regardless of
change size.

Also solved a standing mystery: the HEIC fix (`7c7aa9f`) was pushed to
`origin/main` in an earlier session but never went live. Almost certainly
because that push landed right as the account's credits ran out — Netlify
silently blocks production deploys once credits hit zero, with no obvious
in-app error at push time.

Practical upshot, saved to memory (`project_deploy_gate.md`,
`feedback_prefer_deploy_previews.md`):
- Batch commits into fewer pushes — each push has a flat 15-credit cost.
- **Prefer Netlify deploy previews (branch + PR) over pushing to `main`**
  for verification — previews are free/unlimited on this plan tier,
  confirmed by opening a real throwaway PR this session and checking the
  billing page showed 0 credits consumed afterward.
- **Important caveat**: `index.html` has no build step, so
  `SUPABASE_URL`/the anon key are hardcoded in the file. A deploy preview
  runs against the exact same live Supabase project/data as production —
  it's not a sandboxed environment. Fine for "does this deploy correctly,"
  not a safety net for testing something destructive.

### 3. Permissions / tooling housekeeping
- Jenny ran a separate session that used the `fewer-permission-prompts`
  skill against this project and added two new read-only subagents:
  `.claude/agents/studioco-pattern-scout.md` (inventories existing UI
  patterns before a redesign — `Glob/Grep/Read` only) and
  `.claude/agents/studioco-design-reviewer.md` (checks a change against
  CLAUDE.md's design system after the fact — adds read-only `Bash` for `git
  diff`). Both are legitimate and worth using for the upcoming project
  landing page redesign.
- That same pass had added a blanket `"Bash(git *)"` allow to
  `.claude/settings.local.json`, which would've silently pre-approved
  destructive git commands too. Narrowed it to ~24 specific safe
  subcommands (status/diff/log/add/commit -m/checkout -b/push/etc.),
  explicitly excluding `push --force`, `reset --hard`, `clean -f`,
  `branch -D`, `checkout .`/`restore .`, `rebase -i`. Documented the
  residual caveat (prefix-matching can't fully guard against a trailing
  `--force`) in `CLAUDE.md`'s "Traps" section so it's permanent, not
  session-local.

## Open Question / Next Steps

See `TODO.md` (the `savePersisted` silent-failure item — still needs the
ambiguity in Jenny's original question resolved before building) and
`PLAN.md` (the active initiative: project landing page redesign).

This session also added a "Documentation file allocation" section to
`CLAUDE.md` — `PLAN.md`/`TODO.md` now carve out active-work and backlog
items that used to live in `CLAUDE.md`'s "Outstanding work" and this file's
"Exact Next Steps." Check both against reality at the end of every session
going forward.

## Testing Environment Notes (carries forward)
- No Node/npm available in this shell environment this session — the
  esbuild syntax-check step from `CLAUDE.md`'s testing methodology couldn't
  run. Fell back to the local static server + real-browser check instead,
  which also catches JSX syntax errors since Babel-standalone parses
  in-browser. Worth re ‑checking whether Node is available at the start of
  next session; if so, restore the esbuild step.
- A PowerShell `HttpListener` static file server has been left running
  across sessions on `http://127.0.0.1:8744/`, serving the whole repo root
  (script lives in a session scratchpad, not the repo). Confirmed still
  live and serving current file contents as of this session — reuse it
  rather than starting a new one on the same port (it'll just fail to bind
  and you'll be using the old one anyway, which is fine since it reads from
  disk per-request).
- `.claude/worktrees/agile-mixing-cat/` still exists with a stale
  `index.html`/`home-redesign-preview.html` (last touched 2026-08-05) — not
  used this session, may be safe to clean up if it's not needed for the
  landing page redesign reference beyond the `redesign-concept.html` file
  already noted above.
