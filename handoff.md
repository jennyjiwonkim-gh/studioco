# MOA Studio Handoff

## START HERE — read this first

**Read these four files at the repo root: `CLAUDE.md`, `handoff.md`, `PLAN.md`,
`TODO.md`.** They are the source of truth, not GitHub's commit history. But
they are only trustworthy if the checkout is on an up-to-date `main`, and that
has already gone wrong once.

Before trusting anything below, run:

```
git checkout main
git pull
```

Then confirm the file you are reading is current: the "Live commit" below
should match `git log --oneline -1 origin/main`. **If it doesn't, this file is
stale — pull before doing anything else.**

Why this matters: for most of 2026-08-07 the local checkout sat on a feature
branch, and `main` itself was two sessions behind, because doc updates were
being committed to branches that hadn't merged. A session reading the folder
in that state gets a confident, detailed, wrong picture. `netlify.toml` now
makes doc-only commits free to merge, so there is no longer a reason to let
`main` fall behind — keep it current.

---

## Live commit
**`c0dc628`** on `main`. Production deploy verified via the Netlify API:
`context: production`, `state: ready`, `error_message: null`, published
2026-08-07 19:38. Live at `https://studioco-app.netlify.app`.

The URL, the GitHub repo and the `studioco:` localStorage keys all still say
"studioco" **on purpose** — see the naming section in `CLAUDE.md` before
"fixing" any of them.

## One PR open
**[PR #7](https://github.com/jennyjiwonkim-gh/studioco/pull/7)** — moves
"Target post date" off the project Overview and onto each content piece.
Preview verified at `commit_ref 6af06eb`. Not merged.

It exists because of a mistake worth knowing about: that commit was pushed to
the PR #6 branch at almost the same moment PR #6 was merged, so the merge
captured the previous head and the commit was orphaned on the branch. The
merge commit's parents (`24108bb` + `cc74a81`) show it plainly. **A green
check on a PR is not proof your latest commit shipped — compare the deploy's
`commit_ref` to the branch head.** That is how this was caught, after nearly
sending a stale preview link.

## What shipped 2026-08-07

1. **Public landing page + waitlist.** Rebuilt the signed-out screen: nav, hero
   with the Home screen recreated over a photograph, feature strip, project
   mock, dark early-access band, footer. Waitlist emails persist for real to a
   `waitlist_signups` table (anon INSERT-only, no read path) — the only write
   in the app that runs without a session. **The public face of the site is now
   a waitlist page, not a login box.**
2. **Project-detail redesign.** Back arrow, project switcher, tab strip,
   Product folded into Overview, gallery hero + grid, shared Tags library.
3. **Renamed StudioCo → MOA Studio**, product name only.
4. **New logo** — `MoaMark`/`MoaLockup`, medallion with four gathering nodes.
   The dark variant needs no separate asset; the node colours are the accent
   tokens and swap themselves.
5. **`netlify.toml`** — build-ignore rule so commits touching neither
   `index.html` nor `assets/` skip the deploy.

## Verified
- Console clean and **zero horizontal overflow at 1632, 1440, 1300, 884, 390
  and a true 320px viewport**, checked repeatedly.
- The authenticated click-through outstanding on the project-detail work is
  **closed**: Jenny confirmed the auth gate on a deploy preview, and the
  project screens were checked at 1280/390/320 against a scratch copy with the
  session gate stubbed out.
- Caveat: that scratch run used seeded demo projects, not real data.

## The `netlify.toml` ignore rule works
Confirmed on its first real test: the docs-only commit on PR #7 produced
`"Canceled build due to no content change"` and never deployed.

**Expect the PR check to read "Deploy Preview canceled" — that is success, not
a failure.** Netlify records an ignored build with `state: error` and that
message, which looks alarming and isn't. When it happens, the previous deploy
remains the live preview, which is correct: nothing that affects the page
changed.

## Not verified
- Whether a skipped build costs zero credits or merely fewer. It clearly
  avoids the deploy; the billing detail is unconfirmed.
- The breakpoint checks on the project-detail screens ran against seeded demo
  projects, not real data.

## Housekeeping
- **Stale remote branches** to delete: `landing-page-redesign`,
  `preview/combined`, `project-header-tabs-redesign`, `docs/session-wrap`,
  `test-preview-deploy`. All merged or dead. Not deleted — needs Jenny's word.
- `.claude/worktrees/agile-mixing-cat/` can go; the initiative that needed its
  `redesign-concept.html` has shipped.

## Testing environment (carries forward)
- No Node/npm/esbuild in this shell, and `python` is the Windows Store stub in
  both Git Bash and PowerShell. Substitute: a PowerShell `HttpListener` static
  server plus the browser console — a Babel syntax error shows as an
  `EXCEPTION` and leaves `#root` empty. Server scripts live in a job-scoped tmp
  dir and are not durable; recreate them.
- Chrome on Windows won't size a window below ~545px, so `resize_window` can't
  test 320px. Use a 320px-wide iframe. This caught bugs eyeballing missed.
- To inspect logged-in screens without credentials, serve a copy of
  `index.html` with the `App()` session gate stubbed to a fake session. It
  boots on seeded data with a "saving unavailable" banner. **Keep that copy
  outside the repo** so it can never be committed.
- Deploy previews need Netlify SSO login; confirm builds through the Netlify
  MCP reader (`commit_ref` + `state`), never the green check alone.
