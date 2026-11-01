# ui-skills

Three agent skills for Claude Code. They make a coding agent decide what a
screen is for before writing it. They write the design system down before
building against it. They open the running product before calling it done.

The problem is not that an agent cannot write frontend code. It is that an
agent writes the ticket rather than the product. It satisfies the words of a
request, never opens the result, and ships a screen that is correct and
forgettable.

| Skill | Say | What it does |
|---|---|---|
| `ui-design` | "Make the pipeline health screen modern" | Writes `DESIGN.md` when the project has none, deals three directions and gets one picked, builds a mock when you want one, then builds the real thing |
| `ui-find` | "Find me a timeline component with expandable logs" | Searches the project, then 508 registries, reads the real code and the real licence |
| `ui-review` | "Audit this dashboard" | Measures the live render or the Storybook, walks the journey, checks it against your design principles. Changes no files unless asked |

## What ui-design does

Eleven stages that scale to the size of the change. A copy edit runs three of
them. A new surface runs all eleven, with the direction approved before any
code is written.

| | |
|---|---|
| 0 to 1 | Read what exists. Find or write `DESIGN.md`, and gate it with `pnpm dlx @google/design.md lint` |
| 2 to 3 | Name the business outcome and criteria checkable in a browser. Deal three visual directions and stop for the user to pick one |
| 4 to 5 | Map the journey and every state a single fixture hides. Search 508 component registries before writing a component |
| 6 to 7 | Mock or build direct. Build against the gates and the craft rules, most important screen alone and first, reviewed cold |
| 8 to 9 | Start the application, walk it as the customer with real clicks and typing, measure, fix in severity order, check for regressions |
| 10 | Report what was verified, by what means, and what was not |

Two of those stages are hard stops. The agent does not build past the
direction nobody picked, and does not call a mock finished without being told.

## The result it is built around

A script catches what is measurable. Only looking catches the rest. Building
the demo below produced seven faults:

| Found by measuring | Found by looking |
|---|---|
| 444px page in a 375px viewport | A figure whose entire point was invisible, because the planned window and the empty track were the same grey |
| Nav links at 23px, under the 24px floor | The right half of the first screen empty while the words sat left |
| | A fix for that which stretched the grid apart and wrapped `14:20` to `14:2 / 0` |
| | Five children in a four-column grid, so a value wrapped below its row |

Four of seven were invisible to the measurement. Two were caused by an
earlier fix. That is why the skill does both, and re-runs both after every
change.

## An audit, before and after

A plausible machine-built dashboard, and the same screen after the audit's
blocker and critical findings were fixed. Both measured in a real browser at
1440 and 375.

![Nothing on the screen said what to do first](docs/images/fig-1-hierarchy.png)

![Run status was a coloured dot and nothing else](docs/images/fig-2-status.png)

![At 375px the page scrolled sideways](docs/images/fig-4-mobile.png)

Every number below was measured in the browser at 1440 and 375.

| | Before | After |
|---|---|---|
| Text below its contrast floor | 37 | **0** |
| Targets below 24 × 24 | 3 | **0** |
| Controls with no accessible name | 3 | **0** |
| Placeholder used as the only label | 1 | **0** |
| Heading skips | 1 | **0** |
| Text below 12px | 13 | **0** |
| Distinct type sizes / radii / shadows | 10 / 8 / 2 | **4 / 3 / 0** |
| Page width at a 375px viewport | 780px | **375px** |

The worst contrast was 2.44:1 where 4.5:1 is required. The overflow traced to
`table { min-width: 760px }`. Three things the measurement could not see. Twelve equal tiles with nothing
dominant. Status carried by a coloured dot alone. Two identical blue buttons
side by side.

## A console, before and after the craft pass

Same screen, same content, same markup structure. The right column is what
`references/craft.md` changes.

At page scale the first three of these are a few pixels each, so here is one
panel corner at 5x:

![The craft pass, at 5x on one corner](docs/images/fig-3-craft.png)

The entrance cannot be shown in a still at all. Four frames of one page load,
frozen at exact times:

![The entrance, frozen at four moments](docs/images/fig-5-motion.png)

| Before | After | Why |
|---|---|---|
| `1px solid` border on cards and buttons | three-layer `box-shadow` in oklch: ring, lift, ambient | A border drawn for depth reads as a line and breaks on any other surface |
| one radius everywhere | `12px` panel, `8px` control, concentric | Equal radii on nested surfaces make the inner one look pinched |
| display figure at 88px on paper | 156px on one dark band | Scale contrast is the cheapest way to make a screen read as designed |
| no motion | staged enter at 100ms per region, route lines drawing left to right, count rising to 38 | Arrival once, and never on a repeated interaction |
| hover tints the row | hover also lifts that run's route line | The row and the line are the same run |
| no `:active` | `scale(.96)` | Below `0.95` reads as a flinch |

The last row of that table is the only one that is not polish. A run row and
a route line are the same run, so pointing at either marks both:

![A row and its route line are the same run](docs/images/fig-6-link.png)

`scale(.96)` on press has no honest still, so there is no picture of it here.

The whole screen, for context:

![The dispatch console at 1440px](docs/images/console-after.png)

Every control stays reachable at 375px without sideways scrolling, and each
run becomes its own block:

<img src="docs/images/console-mobile.png" width="375" alt="The console at 375px: the dark band stacks, and each run becomes a labelled block">

## Its public page

Built on the same direction. The product's own output is the signature
element, and a live control turns the sheet as the hour moves.

![Landing page](docs/images/landing.png)

Measured at 1440 and 375: 0 contrast failures, 0 targets under 24 × 24, 0
controls with no name, no sideways scroll. The whole conversion journey was
walked with real clicks and typing, with 0 console errors.

## Component discovery

Search the project first, then 508 shadcn-spec registries, with no account
and no MCP server:

```bash
node .claude/skills/ui-design/scripts/find-components.js \
  timeline stepper activity --limit 10
```

```
@uiarc     timeline   13  not stated on the site's pages that were read
    Follow what happened, newest first, grouped by day.
   deps: lucide-react, motion; files: 2
    read: pnpm dlx shadcn@latest view @uiarc/timeline
@reui      timeline   13  MIT
   deps: @base-ui/react, cn; files: 1
    read: pnpm dlx shadcn@latest view @reui/timeline
searched 24 registries for: timeline, stepper, activity
licences marked "check at the source" were not read; read one before shipping a component
```

The score is a text match, not a judgement. Candidates are then read: their
real code, states, keyboard handling, dependencies and licence. They are
ranked by fit, licence, stack, design system, accessibility and install cost,
in that order. A licence that is not stated anywhere is a blocker, not a
footnote.

## The design system comes first

A review with no stated principles is taste, and taste loses arguments. So
`ui-design` writes the principles down before it builds, into one `DESIGN.md`
at the project root, and `ui-review` measures against that file.

It has two layers. Tokens in YAML, following the `design.md` specification
from `google-labs-code/design.md`. That specification is the only one of
these formats with a working linter. Then prose sections, following the
longer list that `VoltAgent/awesome-design-md` uses. That list adds two
sections every real product needs and the specification lacks: responsive
behaviour, and the brief a future agent gets handed.

```bash
pnpm dlx @google/design.md lint DESIGN.md      # broken refs, missing primary,
                                          # any colour pair below 4.5:1
pnpm dlx @google/design.md export --format css-tailwind
```

Two routes in. Where the project already has tokens, a theme or a live
product, the file **records what is there**. Values come out of the code, or
off the rendered page as computed values. Never estimated from a screenshot.
Where there is nothing, the file is a proposal, and stays marked as one until
the user picks a direction.

## Install

```bash
git clone git@github.com:Dhodraj/ui-skills.git
cp -R ui-skills/.claude/skills/* ~/.claude/skills/
```

Or copy the three skill folders into any project's `.claude/skills/`.

Needs Node 20 or newer. Network for registry search, which falls back to a
cached `--curated-only` search without it. A Playwright MCP server for stages
8 and 9. Without one, renders fall back to headless Chrome, and every journey
claim is reported unverified.

Better with, never required: `blueprint` and `inspect` for the numeric
defaults and the render-and-look pass. `ui-ux-audit` for a deeper measuring
script. `taste-skill` for art direction on a public page. `customer-research`
for real customer evidence. Each is referenced rather than vendored.

Full instructions: [docs/install-and-invoke.md](docs/install-and-invoke.md).

## What is in here

```
.claude/skills/
  ui-design/                        the entry point, 11 stages
    references/
      design-system.md              the DESIGN.md format, and both routes in
      gates.md                      the floor, in three tiers
      craft.md                      the layer above the floor
      directions.md                 dealing three, and the approval gate
      product-thinking.md           outcome, customer, evidence vs invention
      journey-and-states.md         the states one fixture hides
      playwright.md                 browser mechanics
      regression.md                 telling your failures from existing ones
    scripts/                        registry search, page measurement, validation
    tests/                          36 offline tests, 39 with --network
  ui-find/                          natural language to a real component
  ui-review/                        measure, walk, judge against the system
docs/                               research, architecture decision, testing
                                    report, examples, maintenance
demo/                               what the skill produced, and the two
                                    earlier states of it
```

Look at the demo yourself:

```bash
cd demo && python3 -m http.server 8731
# then open http://127.0.0.1:8731/console.html
```

`demo/console-plain.html` is the same console before the craft pass. Put them
side by side.

## Honesty

The reports in `docs/` separate what was read at its source from what was
inferred, and what was executed from what was not. Known gaps are written
down rather than left out:

- `pnpm dlx shadcn` was never run in the session that built this; registry
  access was proven by fetching the JSON directly.
- No component was installed into a project, and no real application with a
  dev server, sign-in and live data was driven.
- `pnpm dlx @google/design.md lint` is a real gate on `DESIGN.md`, and it was
  confirmed published at version 0.4.0. It has not been run against a file
  this skill generated, because no such file exists yet.
- The gates and the craft rules are instructions, not mechanisms. An agent
  that skips those files still passes every automated check. The design
  system is the only part with a linter behind it.
- Dealing three directions is a stage with a hard stop, but nothing can force
  the three to differ on a real axis. Three shades of one idea would pass.
- The Storybook path in `ui-review` is written from the Storybook index and
  iframe URLs. It has not been run against a real Storybook.
- Walking a journey is not customer research. It finds friction. It says
  nothing about what customers do.

Every figure, run, depot and customer in the demo is sample data, and the
pages say so on their own face.

## Licence and attribution

MIT. [NOTICE](.claude/skills/ui-design/NOTICE) records what was adapted and
from where: `ui-ux-audit-skill`, `human-experience`, `customer-research`,
`taste-skill`, `impeccable`, `make-interfaces-feel-better`,
`emilkowalski/skills`, `google-labs-code/design.md`,
`VoltAgent/awesome-design-md`, `Nutlope/hallmark`, `nexu-io/open-design` and
`greensock/gsap-skills`. No file from any of them is copied; each entry says
what idea was taken and how it was changed.

Two sources were read and deliberately not adapted. `DavidHDev/react-bits`
and `DavidHDev/canvas-ui` are both **MIT + Commons Clause**. That permits use
inside a product. It forbids redistributing the components "alone, in a
bundle, or as a ported version". GitHub's sidebar reports both as
`NOASSERTION`, so the restriction is invisible unless the `LICENSE` file is
opened. `ui-find` names them as a worked example of why the label is not the
licence.

The reading behind every source, with its licence as read at the source, is
in [docs/source-research.md](docs/source-research.md).
