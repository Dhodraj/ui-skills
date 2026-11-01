# ADR 0001 — One orchestrator, three companions, two scripts

Date: 2026-10-27
Status: accepted

## Context

The brief asks for a skill that makes an agent think like a business owner, a
product manager, a customer, a designer, a critic, a frontend engineer and a
QA engineer, rather than like someone implementing a ticket. Twenty-two
sources were supplied to draw on; the reading is in
[source-research.md](source-research.md).

Two facts about this machine shaped the design more than anything in the
brief.

1. **Two of the jobs were already done elsewhere.** The author's `blueprint`
   skill planned a screen and carried the numeric defaults. Its `inspect`
   skill rendered a screen at three widths and read the images against seven
   named checks. Both are the author's own work. The first design referenced
   them rather than restating them, to avoid a second source of truth.
2. **A browser is already connected.** The Playwright MCP server answers
   here, with Chromium installed. Anything that needs a second browser stack —
   Python Playwright, a downloaded binary — is a dependency this machine does
   not need.

The gaps the existing skills leave are: deciding what the customer needs
before the first file; finding a component that already exists instead of
writing one; using the running product as a customer rather than looking at
it; auditing something that already exists with a severity order; and not
breaking what worked.

## Decision

One entry-point skill, `ui-design`, with eleven stages that scale to the size
of the change, and two companion skills worth invoking on their own:

| Skill | Why it is separate |
|---|---|
| `ui-design` | The entry point and the order of work |
| `ui-find` | "Find me a component for X" is a request on its own, often with no build attached |
| `ui-review` | "Audit this" must not start editing; a separate skill makes the two modes explicit. Walking a journey as a customer lives here too: it is the verification step for every other skill, and splitting it out left two skills that could not be used without each other |

Everything else is a reference document under the entry point, loaded when
the stage needs it: the design system format, the gates, product thinking,
directions, craft, the journey and its states, browser mechanics, regression
checks.

On 2026-11-01 that referencing was reversed. Depending on a second repository
for the numbers made `ui-design` unusable without it, so `rules.md`, the
dense-screen rules and `inspect`'s seven checks were absorbed, and
`blueprint-ui` was marked superseded.

`references/craft.md` was added after the first build. The skill as first
written could prove a screen was not wrong. It could not stop the screen
being forgettable, which is a different failure, and the one the brief cares
about. The file is loaded at stage G before every UI edit, including a small
one. Craft is applied while the code is written; it cannot be added at review
time.

Two scripts, because both do something no instruction can do reliably:

- `scripts/find-components.js` searches the public shadcn registry directory
  and each registry's catalogue, and prints candidates with the exact command
  to read each one.
- `scripts/measure.js` measures a rendered page in the browser and returns
  numbers.

Plus `scripts/check-sources.js` and `tests/run.sh`.

## Why not the alternatives

| Alternative | Why not |
|---|---|
| One large skill | The eleven stages, the component rules, the browser mechanics and the audit model come to several thousand words. Loading all of it to change a label is waste, and a skill nobody can read is a skill nobody follows |
| A skill per stage, ten or more | Most stages are never invoked alone. A stage that is only ever reached through the workflow is a reference document, not a skill |
| Vendor ui-ux-audit's `uicheck.py` | MIT allows it, and it is the best measuring script read in this research. But it needs Python Playwright, a second browser stack beside the MCP server that is already connected. `measure.js` runs through what is here. The severity model from that skill is adapted and credited instead |
| Keep pointing at blueprint's `rules.md` for the numbers | It was the first design, and it made `ui-design` unusable without a second repo installed. On 2026-11-01 the numbers, the dense-screen rules and `inspect`'s seven checks were absorbed into `references/directions.md`, `references/craft.md` and `ui-review`, and `blueprint-ui` was marked superseded |
| Build discovery on the 21st.dev MCP | Its component retrieval starts at a paid plan, and the free tier is two copies a day. The shadcn directory is free, scriptable and covers 508 registries including most of what 21st indexes |
| Require the shadcn MCP server | It is supported and useful, but a skill that only works with an MCP server configured does not work on a fresh machine. The script needs only Node and the network, and `--curated-only` keeps it useful with neither |
| Install impeccable or taste-skill as dependencies | Both are good, and both are the user's decision: impeccable downloads a binary on first run, and taste-skill is a large asset library. They are referenced where they are the better tool |
| Keep `journey-walk` as a fourth skill | It was one, and it was folded into `ui-review` on 2026-11-01. Nearly every invocation of it was the second stage of a review, and nearly every review needed it. Two skills that are always used together are one skill with a stage |
| Invent a design-system format | `google-labs-code/design.md` already specifies one and ships a linter for it, under Apache-2.0. A new format would need its own tooling to be worth anything, and the ecosystem that reads `DESIGN.md` at a project root already includes `hallmark` and `open-design`. The section list is extended rather than replaced |
| Use the 9-section prose format alone | It is what 84 real brand files and 151 `open-design` packages use, so the ecosystem fit is better. But nothing can lint prose, and a contrast failure written into a design system propagates into every screen built from it. The token layer buys a real gate for the cost of some YAML |
| Put the craft values in `directions.md` | That file answers "what should this look like"; craft answers "how is it built so it feels right". They are read at different stages — E for the direction, G for every edit — so they are separate files |
| Fold `gates.md` into `craft.md` | They answer different questions at different costs. `gates.md` is a floor whose rules name a harmed customer or a false claim, and some of it is never waivable. `craft.md` is quality above that floor and is all judgement. Mixing them makes the floor look negotiable |
| A craft linter instead of a reference | Most of it cannot be measured. A shadow standing in for a border, an entrance on a repeated interaction, an optical mis-centre: each needs the judgement of someone looking. `measure.js` already takes the parts that are numbers |

## Consequences

**Good**

- The research method stays in `customer-research` rather than being
  restated here.
- The core — product thinking, journey mapping, visual direction, the audit —
  works with no network and no registry: only `ui-find` needs either.
- Component discovery is deterministic and free, and its output names its own
  limits.
- Adding a component source is one object in `sources.json`, validated by a
  script and a test.

**Bad, and accepted**

- Referring to `taste-skill` and `customer-research` means a machine without
  them gets less. Each reference says what to do instead, but the fallback is
  thinner than the real thing.
- `measure.js` has to be passed to `browser_evaluate` as a string, which
  costs tokens on the first measurement of each page. It leaves itself on the
  page as `window.pxeMeasure` so the later widths cost one line, but a page
  reload means pasting it again.
- `measure.js` measures less than `uicheck.py` does. It does not check
  card-row alignment, baselines, gutters, concentric corners or padding
  asymmetry. Those are left to the look, and to that skill where it is
  installed.
- Nothing enforces `craft.md` or `gates.md`. Both are read at stage 7 and
  then relied on. The demo in `demo/` was built to them by hand, and the
  difference is visible against `demo/console-plain.html`. An agent that
  skips those files produces the plain version and still passes every
  automated check. `DESIGN.md` is the one exception, because
  `pnpm dlx @google/design.md lint` is a real gate with a non-zero exit code.
- Dealing three directions is now a stage with a hard stop, which closes part
  of the gap `taste-skill` closes differently. It does not close all of it:
  nothing can force the three to differ on a real axis, so three shades of
  one idea would pass the stage and fail the purpose.
- Depending on `@google/design.md` means a network fetch on first use and a
  published package that could change. Version 0.4.0 was confirmed on npm.
  The schema is simple enough to follow by hand if the package goes away;
  what would be lost is the gate, not the format.
- Three skills mean three descriptions competing to be matched. The
  descriptions were written to be disjoint, but overlap is possible. Folding
  `journey-walk` into `ui-review` removed the worst of it: "does this work"
  and "is this good" were two descriptions chasing the same request.

## What would change this decision

- If the Playwright MCP server stopped being available by default, the
  browser route would have to be reconsidered and `uicheck.py`, or something
  like it, would earn its dependency.
- If a free registry index with real semantic search appeared, step 3 of
  `ui-find` would change.
- If the numbers in `references/directions.md` moved to a package of their
  own, this skill would reference that package rather than carry the table.
