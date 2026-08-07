# MOA Studio TODO

Standing backlog — flat checklist, not tied to any one session. Check items
off (or delete) as they land; add new ones as they come up. No narrative,
no dates — that belongs in `handoff.md`.

## Features
- [ ] Signed-in Home screen still needs real content — it's the thinnest screen
      in the app. The flat Home mock on the landing page doubles as a sketch of
      what it could be.
- [ ] Real AI-powered "Copy tasks" — currently exact-phrase matching; needs
      genuine natural-language interpretation plus a confirm-before-applying
      step.
- [ ] Freeform image/screenshot paste on the Inspiration and Planning board
      (sketch/text/eraser already work).
- [ ] AI-generated storefront that reads a project's visual "vibe" from its
      photos. Its own feature; do not ship a shallow version.
- [ ] Real Instagram / Shopify / Etsy integrations — need OAuth and a backend.
- [ ] Waitlist signups can only be read from the Supabase dashboard. If the
      list grows, add an in-app view for it (needs an authed read path — the
      table is deliberately INSERT-only for `anon`).

## Landing page follow-ups
- [ ] Footer's Instagram link points at `@offline_jenny`; swap it for a MOA
      Studio account if one gets made.
- [ ] "Create an account" is unlinked while access is waitlist-only.
      `AuthScreen` still supports `mode="signup"`, so restoring it is a
      one-line change in `App()`.
- [ ] The landing page's five feature blurbs and the two flat mocks describe
      the app as intended, not as built. Re-check them when features land.

## Naming loose ends (all deliberate — read before "fixing")
- [ ] Netlify site is still `studioco-app`, so the live URL is
      `studioco-app.netlify.app`. Renaming it changes the domain; only do it
      deliberately.
- [ ] GitHub repo is still `studioco`.
- [ ] `studioco:` localStorage keys (`AUTH_KEYS`, `STORAGE_PREFIX`) are
      **intentionally** unrenamed — changing them signs the user out and
      orphans cached data. Leave them unless there's a migration plan.

## Housekeeping
- [ ] Delete stale remote branches: `landing-page-redesign`,
      `preview/combined`, `project-header-tabs-redesign`,
      `test-preview-deploy`. All merged or dead.
- [ ] `.claude/worktrees/agile-mixing-cat/` can go — the initiative that
      needed its `redesign-concept.html` has shipped.
