# MOA Studio Handoff

## START HERE

**This file is for the next Claude session, not for Jenny.** She doesn't need
to read it. It exists so a session can learn the current state without
reconstructing it from thirty commit messages — git history says what changed,
this says what is true now, plus the things history can't record: what is
unverified, and which oddities will waste an hour if rediscovered.

**Read it from `origin/main`, never from the working folder:**

```
git fetch origin
git show origin/main:handoff.md
```

The working folder is often parked on an old branch, so the copy on disk can
be months out of date while `origin/main` is always current. Reading from
`origin/main` makes that impossible to get wrong and asks nothing of Jenny.
Same for `CLAUDE.md`, `PLAN.md` and `TODO.md`.

Keep documentation landing on `main` — `netlify.toml` makes doc-only commits
skip the deploy, so it is free.

---

## Live commit
**`44a5c57`** on `main` (this file's own merge). The last commit that changed
the site is `c0dc628`, whose production deploy was verified via the Netlify
API: `context: production`, `state: ready`, `error_message: null`, published
2026-08-07 19:38. Live at `https://studioco-app.netlify.app`.

Docs merges after that deployed nothing at all — see the ignore-rule section
below — so `main` moving without a new deploy is expected, not a fault.

The URL, the GitHub repo and the `studioco:` localStorage keys all still say
"studioco" **on purpose** — see the naming section in `CLAUDE.md` before
"fixing" any of them.

## One PR open
**[PR #8](https://github.com/jennyjiwonkim-gh/studioco/pull/8)** — moves
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
Confirmed twice: on a preview, and then on a real merge to `main` — PR #9 was
docs-only and produced **no production deploy at all**. Netlify's current
deploy stayed the one from 2026-08-07 19:38. Docs cost nothing to land, so
keep landing them.

The message to expect is `"Canceled build due to no content change"`.

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

## Housekeeping — done
- Stale remote branches deleted. The remote is now **`main` plus
  `feat/target-post-date`** (PR #8) and nothing else. Each was checked before
  deletion by diffing its `index.html` against the surviving branch, not by
  trusting a "merged" label — see the PR #6 note above for why that matters.
- Jenny's checkout is on `main` at `44a5c57`, matching `origin/main`.

## Still outstanding
- `.claude/worktrees/agile-mixing-cat/` can go; the initiative that needed its
  `redesign-concept.html` has shipped.
- The old session worktrees under `.claude/worktrees/` are disposable — all
  work from them is in `main` or PR #8.

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
