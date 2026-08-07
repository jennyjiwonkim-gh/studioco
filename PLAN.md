# StudioCo PLAN

The single active initiative — not a backlog (see `TODO.md` for that) and
not a session log (see `handoff.md` for that). Rewritten/replaced wholesale
when the active initiative changes, rather than accumulating.

## Active: Project landing page redesign (`ProjectDashboard`/`BoardScreen`)

Reference: "StudioCo — Redesign Concept" Artifact
(`https://claude.ai/code/artifact/d7fec2a1-56ad-43f0-a03d-537a39b1ab78`),
local copy at `.claude/worktrees/agile-mixing-cat/redesign-concept.html` —
specifically its Project Detail page (built for "Moon jars," stage tracker +
Inspiration-board canvas).

Steps:
1. Run `studioco-pattern-scout` first — inventory existing UI patterns before
   designing anything new. ✅ done for the header/tabs + project-detail work.
2. Design against `CLAUDE.md`'s Bauhaus-editorial system (accent hues,
   `border-radius: 3px`, left-border-stripe project identity, no new visual
   language).
3. Implement directly in `index.html` — real code, not a new mockup. ✅ landed
   across `0bca32c`, `1817695`, `7e4630c`, `9ffd81f`, `87c1746` on
   `project-header-tabs-redesign` (PR #3, open, not merged).
4. Run `studioco-design-reviewer` after landing a chunk. ✅ run against the
   latest chunk (`9ffd81f`) — clean, one follow-up fix applied (`87c1746`:
   Gallery mobile-responsive grid + hero border).
5. Verify: local static server, desktop 1280px + mobile 390/320px, screenshot
   every visual change. ⚠️ partial — syntax/console-clean checks done via
   local server; full authenticated click-through at each breakpoint still
   blocked by Supabase login (needs Jenny present). Do this before merging.
6. Verify via a Netlify deploy preview (branch + PR) before deciding to push
   to `main`. ✅ done for `87c1746` — preview build `ready`, commit confirmed
   via Netlify API. PR #3 still needs Jenny's go-ahead to merge to `main`.
