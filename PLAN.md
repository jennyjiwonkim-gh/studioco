# MOA Studio PLAN

The single active initiative — not a backlog (see `TODO.md` for that) and
not a session log (see `handoff.md` for that). Rewritten/replaced wholesale
when the active initiative changes, rather than accumulating.

## Active: none

The previous initiative — the public landing page + waitlist, shipped together
with the project-detail redesign, the MOA Studio rename and the new logo —
**is live on `main` (`c0dc628`)**. There is no active initiative until Jenny
picks the next one.

Left empty on purpose rather than inventing work: `TODO.md` holds the backlog,
and choosing from it is Jenny's call, not a default.

**One PR is open and is not an initiative**: [#7](https://github.com/jennyjiwonkim-gh/studioco/pull/7),
moving "Target post date" onto each content piece. It just needs merging — see
`handoff.md` for why it ended up on its own PR.

## Candidates, when it's time to pick

Roughly in order of how ready each is to start:

1. **Home landing screen (in-app)** — the signed-in Home is still the thinnest
   screen in the app. The two flat mocks drawn for the marketing page are, in
   effect, a design sketch for what it could become.
2. **Freeform image/screenshot paste on the Inspiration board** — sketch, text
   and eraser already work, so this extends a working feature rather than
   breaking new ground.
3. **Real AI-powered "Copy tasks"** — currently exact-phrase matching. Needs
   genuine natural-language interpretation plus a confirm-before-applying step.
4. **AI-generated storefront from a project's photos** — its own feature, and
   explicitly not to be shipped shallow.
5. **Real Instagram / Shopify / Etsy integrations** — the largest by far; needs
   OAuth and a backend, so it starts as an architecture conversation.

## Standing reminders for whatever comes next

- The public landing page is now the front door for signed-out visitors. Any
  change to `LandingScreen` changes what the world sees.
- Deploy previews are free; production deploys cost 15 credits each. Batch work
  behind one merge rather than shipping piecemeal.
