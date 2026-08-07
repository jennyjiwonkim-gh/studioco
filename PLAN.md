# StudioCo PLAN

The single active initiative — not a backlog (see `TODO.md` for that) and
not a session log (see `handoff.md` for that). Rewritten/replaced wholesale
when the active initiative changes, rather than accumulating.

## Active: Public landing page (pre-login) + waitlist

Source design: Jenny's Claude Design project "Pottery tracker landing page"
(`https://claude.ai/design/p/b0e3cc1f-69f9-4ee4-b23b-afd16b1acffe`, file
`StudioCo Landing.dc.html`). To read it, use the design tool's own file API
from a logged-in browser tab — `WebFetch` 403s on `claude.ai/design/*`, and
working from screenshots loses the exact values.

Shape agreed with Jenny: keep **both** halves. The original landing copy sits
at the top, full width, with the "eight stages" references removed; the new
marketing page sits underneath it. "Join the waitlist" is the primary CTA,
"Log in" stays, "Create an account" is unlinked while access is waitlist-only.

Steps:
1. Rebuild `LandingScreen` in `index.html` against the app's own design system
   rather than the mockup's raw hexes. ✅ landed in `06da477` on
   `landing-page-redesign`.
2. Persist waitlist emails for real. ✅ `waitlist_signups` table, anon
   INSERT-only grant + policy, no read path, plus a `submitWaitlistEmail`
   client helper. Verified insert succeeds while SELECT and DELETE are refused.
3. Run `studioco-design-reviewer`. ✅ run — it flagged the mockup's five pastel
   band tints and the hero's decorative circle as tinted-fill drift. Jenny
   chose neutral cream/paper alternation; applied. The duplicated dark-theme
   hexes it also flagged are now named constants (`LANDING_DARK_CARD`).
4. Verify: local server, 1280 + 390 + a true 320px viewport, screenshot every
   change. ✅ done. Two real bugs found and fixed at 320px (a 22px nav
   overflow, and uneven calendar columns).
5. **Next: Jenny's visual review.** She has not seen the rendered page yet —
   only the code and a description of it. Expect changes.
6. Then: a Netlify deploy preview for the branch, and a merge decision.
   Nothing here has been pushed to `main`.

## Also still open — previous initiative, not abandoned

`project-header-tabs-redesign` / **PR #3**: the project *detail* page
(`ProjectDashboard`/`BoardScreen`) redesign, built from the "StudioCo —
Redesign Concept" Artifact
(`https://claude.ai/code/artifact/d7fec2a1-56ad-43f0-a03d-537a39b1ab78`).
Still open against `main`, unmerged, last commit `87c1746`. Its remaining
blocker is unchanged: a full authenticated click-through at each breakpoint,
which needs Jenny to log in. Note that branch also carries its own newer
`PLAN.md`/`handoff.md` annotations that `main` does not have — expect a
conflict in those two files when it merges.
