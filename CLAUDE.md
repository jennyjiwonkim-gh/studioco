# StudioCo

A private studio-management app for ceramic artists, built and used by Jenny
(solo ceramic artist, Instagram `@offline_jenny`). Single-file React + in-browser
Babel, no build step. Deployed as `index.html` at the repo root; Netlify
auto-deploys on every push to `main`.

A second, separate site — **StoreCo** — is the public-facing sales/archive
window. StudioCo is the private workshop and the source of truth; StoreCo is fed
by it. Do not merge them.

---

## Non-negotiables

- **Simplicity wins.** The audience is artists who are not necessarily
  tech-savvy. Ease of use is the single biggest factor for adoption. Prefer the
  obvious solution over the clever one, and suggest it *first* — not after
  exhausting alternatives.
- **Test before showing.** Never present a file, fix, or claim as working
  without having run it. Do not report progress on work that has not been done.
- **Consult before architectural decisions.** Do not choose a backend, restructure
  data, or introduce a new pattern unilaterally.
- **Do not suggest stopping or pausing.** Jenny decides when to stop.

## Design north star

Bauhaus-editorial. Inter; `border-radius: 3px` (sharp, not 0, not soft).

**Display face:** headlines on the public landing page use **Cormorant
Garamond** (`SERIF` in `index.html`) — Jenny's call, added 2026-08-07, because
the sans headlines read flat on a marketing page. It is a high-contrast face:
use it only at large sizes (>= 26px) with tight leading, never for UI, labels,
controls or body copy. Everything inside the app proper is still Inter.

**Static assets:** `assets/` at the repo root, served by Netlify alongside
`index.html` and referenced by relative path — the only non-`.md`, non-HTML
content in the repo. Both current images come from the same Pexels photo by
**Raymond Petrik** (free to use under the Pexels licence, no attribution
required), a 4104x6039 / 4 MB original that lives outside the repo in
`StudioCo Landing/`:

- `hero-vessel.jpg` — the hero background, downscaled to 1200px wide at q82
  (~233 KB).
- `vase-cutout.jpg` — the same vase with its background removed, for the
  waitlist band. **It is a JPEG, not a PNG, and its background is not
  transparent — it is flood-filled with `#111111` to match `--ink`.** As a
  transparent PNG this was 1.7 MB; composited onto the band's own colour it is
  107 KB and looks identical. The catch: if that band ever stops being
  `#111111`, this image will show as a dark rectangle and must be re-exported.

Always resize and recompress before committing an image; do not ship
camera-sized originals, and do not base64 them into `index.html`, which is
already ~375 KB.

Background removal is worth a note: the vase is lit from the right, so its
shadowed left edge is *darker* than the shadowed wall beside it. No threshold
or flood-fill separates them — both were tried and both failed. Use a real
matting tool (Jenny used remove.bg). Its free tier returns a small preview, so
the trick is to take the alpha from that preview, upscale it, and apply it to
the full-resolution original rather than upscaling the preview itself.
Cream `#F8F6F1` background. Project identity is carried by a **colored left-border
stripe** plus a vessel silhouette icon — never by tinted background fills.

Only these five accent hues, at full saturation:

| hue | light | dark |
|---|---|---|
| navy | `#102A43` | `#6FA0CC` |
| royal blue | `#120A8F` | `#8C8AF0` |
| cobalt | `#1D4ED8` | `#7FA9FF` |
| lemon | `#8F7300` (darkened for WCAG-AA) | `#FFF176` |
| tomato | `#F24A3D` | `#FF7A6E` |

White and black are for text and surfaces only, never as an accent fill.
Do not introduce a new visual language. Ground every change in what is already
in the file.

## Persistence & auth

State lives in Supabase, table `app_state`, keyed by `(user_id, key)` with RLS
scoping every read/write to the logged-in account. The app stores JSON blobs
under seven keys: `projects`, `tasks`, `boardContent`, `contentLibrary`,
`campaignLibrary`, `contacts`, `theme`.

This deliberately replaced an earlier 7-table relational schema. Those tables
still exist but are unused — a relational rewrite of every board's shape was a
much larger, riskier job than the app needed for a single user.

The whole account loads in **one** request into a cache at boot; writes are
debounced and go through `savePersisted`. Auth is Supabase email/password via
the REST API (no client SDK — it does not work as a plain CDN script in a
single-file app). Expired access tokens refresh once and retry automatically.

One table sits outside that model: `waitlist_signups` (`id`, `email`,
`created_at`), written by the public landing page's "Join the waitlist" form
via `submitWaitlistEmail` — the only write in the app that runs **without** a
session. It grants `anon`/`authenticated` **INSERT only**, with a matching
insert RLS policy and no SELECT/UPDATE/DELETE grant, so signups can be added
from the public page but the list can never be read back over the public API.
Read it from the Supabase dashboard/service role. Note that RLS and the table
`GRANT` are both required — a policy alone still returns `42501`.

There is no migrations directory in this repo; schema changes are applied
straight to Supabase (same as the original `app_state` table) and documented
here.

## Testing methodology

1. Extract the JSX from the `<script type="text/babel">` block and syntax-check
   it with esbuild.
2. Playwright behavioural tests in headless Chromium over `file://`.
3. Check desktop (1280px) **and** mobile (390px, 320px).
4. Screenshot and actually look at it for any visual or layout change.

## Traps that have already cost time

- **Test scripts, not the app, are usually the bug.** Every "failure" so far
  traced back to the harness. Before believing a failure, verify the selector.
- `get_by_text` matches the **hidden nav-drawer link** as well as the visible
  tab button; the drawer is translated off-screen, so clicks time out with
  "element is outside of the viewport". Use `get_by_role("button", name=...)`.
- Several seeded tasks share a title (`"Pick up"` appears twice). Never identify
  a task by title — diff the payload by `id`.
- The Home screen only ticks *today's* tasks, and the seeded dates land tomorrow.
  Drive task-toggle tests from the Task View tab.
- Stacking-context bugs are invisible to DOM and style assertions. A
  `z-index: -1` illustration was hidden behind the root's opaque background;
  only a screenshot caught it. Fixed by giving the root `position: relative;
  z-index: 0`.
- Locally-opened `file://` pages break CDN-loaded libraries. Test deployed, or
  vendor the libs.
- Chrome on Windows refuses to size a window narrower than ~545px, so
  `resize_window` **cannot** actually test the 320px breakpoint — it silently
  reports success while `window.innerWidth` stays ~545. Load the page inside a
  320px-wide `<iframe>` instead: the inner document gets a genuine 320px
  viewport. Then assert on
  `documentElement.scrollWidth - clientWidth` for horizontal overflow, and walk
  the elements comparing `getBoundingClientRect().right` to find which one
  overflows. This caught a real 22px nav overflow that eyeballing missed.
- A bare `1fr` grid track has a min-content floor, so long cell text silently
  pushes columns to uneven widths at narrow viewports. Use `minmax(0, 1fr)`
  whenever the cells contain wrapping text.
- `{/* … */}` is a JSX comment and is only valid where JSX *children* are
  expected. Inside the parens of `{cond && ( … )}` it parses as a block
  statement and Babel fails with `Unexpected token, expected ","`. Use a plain
  `/* … */` there, or move the comment above the `{cond && (`. Babel's reported
  line numbers are relative to the inline `<script>`, not the file — add the
  script's start line (~70) to map them back.
- The file must be named **`index.html`** — web servers serve that by default;
  any other name 404s at the site root.
- `.claude/settings.local.json`'s git permission allowlist is intentionally
  narrow (status/diff/log/show/add/commit -m/branch listing/checkout -b/
  checkout main/fetch/remote -v/ls-files/ls-remote/stash list & push/plain
  push/push -u origin) and deliberately excludes destructive or
  history-rewriting commands (`push --force`/`-f`, `reset --hard`, `clean -f`,
  `branch -D`, `checkout .`/`restore .`, `rebase -i`, `filter-branch`) so
  those still prompt every time. But the matcher is prefix-based, so e.g.
  `git push -u origin some-branch --force` would still match the `git push -u
  origin *` allow-rule — the allowlist narrows the surface, it isn't a real
  guarantee against a force-push slipping through. Never construct a
  destructive git command without Jenny explicitly asking, regardless of what
  the allowlist would technically permit.

## Deploy loop

Commit to `main` → Netlify redeploys automatically. Changing Netlify *settings*
does not trigger a redeploy; that needs a manual "Trigger deploy". Every
production deploy costs a flat 15 credits regardless of change size — stay
frugal about triggering one, on principle, independent of the current plan's
credit ceiling. Don't push to `main` without Jenny's explicit go-ahead;
prefer a branch/PR deploy preview (free, unlimited) when something just
needs to be checked live rather than actually shipped.

## Working style

Jenny gives direct, terse feedback and moves fast. She thinks visually and
shares reference images. She catches errors and corrects firmly — take the
correction, fix the root cause, don't over-apologize. Increment version numbers
on deliverables.

## Outstanding work

See `TODO.md` for the outstanding feature backlog.

## Documentation file allocation

Four `.md` files at the repo root, each with exactly one job. Keep items in
the file that matches their nature — don't let one file absorb another's
content:

- **`CLAUDE.md`** (this file) — standing rules: non-negotiables, design
  system, architecture facts, testing methodology, traps. Changes rarely,
  survives across every session.
- **`handoff.md`** — snapshot of the most recent session: what changed, what
  state things are in, what's still ambiguous. Gets rewritten each session,
  not accumulated.
- **`PLAN.md`** — the single active initiative, with concrete steps.
  Rewritten/replaced wholesale when the active initiative changes.
- **`TODO.md`** — flat standing backlog, no dates or narrative. Items get
  checked off or deleted as they land; new ones get appended as they come up.

**Every time `handoff.md` is written** (i.e. at the end of a session), check
`TODO.md` and `PLAN.md` against what actually happened: cross off / remove
items that were completed or superseded, and add any new open items or
follow-ups that surfaced during the session. Don't let either file drift out
of sync with reality.
