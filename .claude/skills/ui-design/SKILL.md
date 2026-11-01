---
name: ui-design
description: "Design or redesign a product surface, from the design system up. Use when building, redesigning or modernising a SaaS screen, dashboard, data platform, landing page, onboarding flow or any customer-facing interface, and when the user says a product looks dated, generic or AI-generated. Writes a DESIGN.md design system when the project has none, proposes visual directions and gets one picked, builds a mock for approval when the user wants one, then builds the real thing and verifies it in a browser. Companions: ui-find, ui-review."
---

# UI design

An agent asked for a screen writes the screen. This skill closes the gap on either
side of that. First, deciding what the surface is for and what it should look
like. Then, finding out whether the thing built does the job.

Two rules govern everything below. **Name the customer outcome before the
first file. Drive the running product before saying it works.**

## Pick the depth first

| The change | Stages |
|---|---|
| Copy, a colour, one prop | 1, 7, then look at the result |
| One component, one state | 1, 4, 6, 7, 9 |
| A screen, a form, a flow | 0 to 10, briefly |
| A new surface, or "make this modern" | 0 to 10 in full, with stage 3 approved before stage 7 |
| "Audit this" | Use `ui-review`. Do not start editing |
| "Find me a component for…" | Use `ui-find` |

Say which depth you picked, in one line, before starting. A stage is skipped
only when the change cannot reach what that stage protects.

## 0. Preflight: read the project before asking anything

Read, in this order, and stop at the first that settles the question:

1. `DESIGN.md` at the project root. **A design system that already exists
   outranks every default in this skill and every taste of your own.** Follow
   it. Where the work needs something the file does not cover, add that to the
   file rather than inventing it in a stylesheet.
2. `PRODUCT.md`, `README.md`, `.claude/`, an existing brief.
3. The stack, the styling system and the component library in use.
4. The nearest existing screen, and the tests covering it.

A screen that contradicts the product's own system is a finding, not a licence
to start a third system.

## 1. The design system

[references/design-system.md](references/design-system.md) holds the format and
both routes into it.

| The project has | Do this |
|---|---|
| A `DESIGN.md` | Read it, follow it, and say so in one line |
| A real design system in code: tokens, a theme file, a Tailwind config | Codify it into `DESIGN.md` by reading the values out of the code, never by guessing them |
| A live product or a brand with no written system | Codify from the real thing: render it, read the computed values, record them |
| Nothing | Write one at stage 3, from the direction the user picks |

Never write a `DESIGN.md` that states values the product does not use. A
codified system is a record of what is there. A new system is a proposal, and
is marked as one until the user picks it.

Gate the file before building against it:

```bash
pnpm dlx @google/design.md lint DESIGN.md
```

It is a real published package. It catches broken token references, a missing
`primary`, typography that was never defined, and any component colour pair
below the 4.5:1 contrast floor. Fix what it reports before stage 7.

## 2. The customer outcome

[references/product-thinking.md](references/product-thinking.md) holds the
method. The business outcome, the customer and their job, and the smallest
useful version. Two lines each.

Write the outcome as something observable:

> A dispatcher with 40 open runs can see which run is late and reassign it
> without leaving the list.

Then acceptance criteria that can each be checked in a browser. "Looks good"
is not a criterion. "At 375px every run's status and its reassign control are
reachable without horizontal scrolling" is.

Where there is no evidence, write the assumption and label it. Never write a
persona, a quotation or a conversion figure that nobody produced.

## 3. Directions, and the user's pick

[references/directions.md](references/directions.md) holds the method and the
vocabulary.

Deal **three** directions, not one. A single proposal is the model's default
aesthetic wearing a rationale. Each direction gets a name, the anchor hue, the
type pairing, the density and the one memorable thing, in four lines.

Then stop and put them to the user with the interactive question tool. This is
an approval gate: do not build past it on an assumption. Where the user says
"you pick", pick one, say which and why in one line, and carry on.

On a yes, write or update `DESIGN.md` with the picked direction's real values,
and lint it.

## 4. The journey and its states

[references/journey-and-states.md](references/journey-and-states.md).

List the states this surface really has: empty, one item, many, the densest
real case, loading, partial failure, error, no permission. **A screen built on
one fixture hides every other state**, and that is the commonest reason a
finished screen fails on contact with a customer.

## 5. Find components before writing them

Run `ui-find`, or its script directly:

```bash
node .claude/skills/ui-design/scripts/find-components.js <terms> --limit 15
```

Search the project's own components first. A second implementation of a
component the product already has is a defect. Write a component from scratch
only after naming the candidates read and why each was unsuitable.

## 6. Mock, or the real product

Ask which, in one line, unless the user already said. Both routes build real
HTML and CSS; the difference is where the file lands and what it is wired to.

| Route | When | Where it goes |
|---|---|---|
| **Mock** | A new surface, a redesign, a stakeholder to convince, or the user asks for one | A standalone page under `mock/`, with the real design tokens and sample data that says on its face that it is sample |
| **Direct** | A change inside a live product, or the user says build it | The product's own files |

A mock is approved, then rebuilt in the product's components. Say that before
building it, so nobody expects the mock file to ship.

## 7. Build

Build the states from stage 4, not only the happy path. Use the product's own
tokens and components.

Read [references/gates.md](references/gates.md) and
[references/craft.md](references/craft.md) immediately before the first UI
edit, every time, including a small one.

`gates.md` is the floor: the defaults every model falls into, and what to do
instead. `craft.md` is the layer above it. Depth as a shadow rather than a
border. Concentric radii. Ease-out under 300ms. Entrances that fire once and
never on a repeated interaction. Tabular figures. Two parts of a screen
connected, so the customer sees they are one thing.

Build the most important screen, or the hero, **alone and first**. Review it
before building the rest, with fresh eyes: a subagent with no memory of the
decisions catches what you cannot. Then build the remainder.

## 8. Run it and look at it

Not a source review. Start the application, open it in a browser, and use it
from the real entry point as the customer would.

[references/playwright.md](references/playwright.md) holds the mechanics and
what to do when the application will not start here.

Look at the screenshots. Reading a file list is not looking.

## 9. Review and fix

Run `ui-review` against what you built. It measures the live render, walks the
journey, checks the result against `DESIGN.md`, and sorts findings by harm.

Fix in severity order. A change you cannot justify against the outcome in
stage 2 is scope nobody asked for. Say what you left alone and why.

Then [references/regression.md](references/regression.md): identify the
journeys that already worked before touching the code, and separate failures
your change caused from failures that were already there.

## 10. Report

- The customer outcome, and whether it is met.
- The direction picked, and by whom.
- What was built or changed, by file.
- What was verified, by what means, at which widths, in which states.
- What was not verified, and why.
- Findings fixed, and findings left with their reason.
- Assumptions that are still assumptions.

## Honesty, which outranks every stage above

- Never report a measurement you did not take or a screen you did not render.
- Name every stand-in. A fixture for live data, a static page for the
  application, a local font for the brand's.
- When the application cannot be started or reached, say so and mark every
  journey claim unverified. Do not describe the source as though you
  watched it run.
- Never invent a metric, a testimonial, a logo wall or a customer count. Where
  the real figure is missing, leave a labelled slot and say it is empty.
- A form with nothing behind it says so on its face. A page that prints
  "Sent" without sending is a defect, not a placeholder.
- Walking a journey yourself finds friction. It does not measure what
  customers do.

## What this skill borrows, and from where

[NOTICE](NOTICE) records it. Component sources and their licences are in
[sources.json](sources.json); `scripts/check-sources.js` validates that file
and `tests/run.sh` tests the scripts.
