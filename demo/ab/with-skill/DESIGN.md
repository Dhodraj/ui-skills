---
name: Sluice
description: Observability console for the on-call data platform engineer during the batch window

colors:
  primary: "#0e1619"
  secondary: "#3a484c"
  muted: "#5a686c"
  accent: "#0e6a70"
  accent-deep: "#0a5257"
  accent-ink: "#f7fcfc"
  neutral: "#edf1f1"
  surface: "#fbfdfd"
  inset: "#e4e9e9"
  on-surface: "#0e1619"
  rule: "#cbd4d4"
  rule-soft: "#e0e6e6"
  edge: "#7e8d8f"
  error: "#a32c1c"
  risk: "#7a5000"
  ok: "#1b6043"
  band: "#0d1517"
  band-ink: "#e8eded"
  band-quiet: "#9aa8ab"
  accent-lift: "#4fb4ba"
  accent-lift-hover: "#7ccdd2"
  on-band-line: "oklch(1 0 0 / 0.28)"
  on-band-wash: "oklch(1 0 0 / 0.1)"
  hatch: "oklch(1 0 0 / 0.3)"
  wash-error: "#f6e7e4"
  wash-risk: "#f3ece0"
  wash-ok: "#e3eee9"
  error-lift: "#f08a74"
  risk-lift: "#d9a33c"
  ok-lift: "#4fbd8a"

typography:
  figure-xl: {fontFamily: ui-monospace, fontSize: 88px, fontWeight: 400, lineHeight: 0.92, letterSpacing: -0.035em}
  figure-md: {fontFamily: ui-monospace, fontSize: 28px, fontWeight: 400, lineHeight: 1.05, letterSpacing: -0.02em}
  body-md:   {fontFamily: ui-sans-serif, fontSize: 16px, fontWeight: 400, lineHeight: 1.5}
  heading:   {fontFamily: ui-sans-serif, fontSize: 16px, fontWeight: 600, lineHeight: 1.3}
  label:     {fontFamily: ui-sans-serif, fontSize: 13px, fontWeight: 400, lineHeight: 1.35}
  ident:     {fontFamily: ui-monospace, fontSize: 13px, fontWeight: 400, lineHeight: 1.3}
  micro:     {fontFamily: ui-sans-serif, fontSize: 12px, fontWeight: 400, lineHeight: 1.3}

spacing:
  base: 16px
  xs: 4px
  sm: 8px
  md: 16px
  lg: 24px
  xl: 40px
  xxl: 64px
  gutter: 24px

rounded:
  sm: 4px
  md: 8px
  lg: 12px
  full: 9999px

components:
  button-primary:
    backgroundColor: "{colors.accent}"
    textColor: "{colors.accent-ink}"
    rounded: "{rounded.sm}"
    padding: 8px
  button-primary-hover:
    backgroundColor: "{colors.accent-deep}"
    textColor: "{colors.accent-ink}"
  button-quiet:
    backgroundColor: "{colors.surface}"
    textColor: "{colors.secondary}"
    borderColor: "{colors.edge}"
    rounded: "{rounded.sm}"
    padding: 8px
  button-quiet-hover:
    backgroundColor: "{colors.inset}"
    textColor: "{colors.primary}"
  input:
    backgroundColor: "{colors.surface}"
    textColor: "{colors.primary}"
    borderColor: "{colors.edge}"
    rounded: "{rounded.sm}"
    padding: 8px
  panel:
    backgroundColor: "{colors.surface}"
    textColor: "{colors.primary}"
    rounded: "{rounded.lg}"
    padding: 0px
  table-row:
    backgroundColor: "{colors.surface}"
    textColor: "{colors.primary}"
  table-row-hover:
    backgroundColor: "{colors.inset}"
    textColor: "{colors.primary}"
  table-row-selected:
    backgroundColor: "{colors.inset}"
    textColor: "{colors.primary}"
    borderColor: "{colors.accent}"
  verdict-band:
    backgroundColor: "{colors.band}"
    textColor: "{colors.band-ink}"
    rounded: "{rounded.lg}"
    padding: 24px
  status-breach:
    backgroundColor: "{colors.surface}"
    textColor: "{colors.error}"
    rounded: "{rounded.sm}"
  status-risk:
    backgroundColor: "{colors.surface}"
    textColor: "{colors.risk}"
    rounded: "{rounded.sm}"
  status-ok:
    backgroundColor: "{colors.surface}"
    textColor: "{colors.ok}"
    rounded: "{rounded.sm}"
---

# Sluice

## Overview

Sluice is a console for the engineer on call for a data platform. They open it
in the middle of a batch window. They have one question, in three parts. What
broke overnight. What downstream data is wrong because of it. Can I promise
the finance team their numbers at 09:00.

The feeling is a tide table, not a cockpit. Dense, quiet, light, and ordered
by time. Nothing glows. One figure is large because one figure answers the
question; everything else is a row in a table, because rows compare and tiles
do not.

The direction is Batch window. Every panel on the screen hangs off one shared
time axis. A failed run, the tables that went stale behind it and the incident
that records it then line up in a single vertical read.

Two directions were considered and rejected. Lineage first made the dependency
graph the screen. That answers "why" well and "what now" badly, and it costs
the most to build at a phone width. Freshness ledger made one dense table of
every asset against its SLA. That answers "what is stale" and loses the
cause.

This system was written for a new surface with no brand and no existing
tokens. The values are a proposal, not a record of a live product.

## Colors

Light ground, near-black ink, one accent. The hues sit slightly toward cyan so
the greys read as cool storage rather than as paper.

| Token | Value | Role |
|---|---|---|
| `neutral` | `#edf1f1` | The page. Every panel sits on it |
| `surface` | `#fbfdfd` | Panels, rows and inputs |
| `inset` | `#e4e9e9` | Hovered and selected rows, bar tracks, the axis strip |
| `primary` | `#0e1619` | Ink. Headings, row primary text, figures |
| `secondary` | `#3a484c` | Body text inside a row |
| `muted` | `#5a686c` | Labels, timestamps, units. Still 5.08:1 on the page |
| `accent` | `#0e6a70` | The single most important action, the `now` cursor, the selected row's edge, the focus ring |
| `accent-deep` | `#0a5257` | Hover and press on the primary action |
| `error` | `#a32c1c` | An SLA already missed, a failed run, a severity-1 incident |
| `risk` | `#7a5000` | At risk inside this window: late and still running, freshness inside its last hour |
| `ok` | `#1b6043` | Landed inside its window |
| `edge` | `#7e8d8f` | The edge of anything a customer can click or type into. 3.38:1 on surface |
| `rule` | `#cbd4d4` | Table rules and panel dividers. Separation, never a control edge |
| `band` | `#0d1517` | The verdict band at the top, the only dark surface |
| `wash-error`, `wash-risk`, `wash-ok` | `#f6e7e4`, `#f3ece0`, `#e3eee9` | The fill behind a lineage node, and the sample notice. Never behind body text |
| `on-band-line`, `on-band-wash`, `hatch` | white at 28%, 10% and 30% | The only colours used on top of `band` or on top of a status bar |

The accent is the action colour and the selection colour. It is never a status
and never a fill: on the built console it covers well under 5% of the
viewport. Status is `error`, `risk` and `ok`, each of which also carries a
word and a glyph, so the status survives greyscale and colour blindness.

On the dark band the same roles switch to the lifted set: `accent-lift`,
`error-lift`, `risk-lift`, `ok-lift`, each measured over `band` at 7.5:1 or
better. The ink there is `band-ink`, the quiet text `band-quiet` at 7.53:1.

Every ratio above was measured with a WCAG relative-luminance calculation, not
estimated.

## Typography

No web font is fetched, so the families are the platform's own faces. The
display face is a decision not yet made: a licensed grotesque belongs in
`figure-xl` and `heading` once a brand exists. Until then the figures are set
in the platform monospace. That is the right idiom anyway. This screen is full
of run identifiers, partition counts and clock times, and they all have to
align down a column.

```css
--sans: ui-sans-serif, system-ui, -apple-system, "Segoe UI", Helvetica, Arial, sans-serif;
--mono: ui-monospace, "SF Mono", SFMono-Regular, Menlo, Consolas, "Liberation Mono", monospace;
```

| Level | Size | Family | Weight | Used for |
|---|---|---|---|---|
| `figure-xl` | 88px, 60px under 760px | mono | 400 | The breach count in the verdict band. One instance per screen |
| `figure-md` | 28px | mono | 400 | A panel's own headline number |
| `body-md` / `heading` | 16px | sans | 400 / 600 | Row primary text at 400, panel titles at 600 |
| `label` / `ident` | 13px | sans / mono | 400 | Labels and secondary text in sans, identifiers and times in mono |
| `micro` | 12px | sans | 400 | Axis ticks and units only. The floor; nothing is smaller |

Five sizes and two weights on the screen. Hierarchy comes from the jump
between 88px and 16px, not from a third weight. Everything that changes or is
compared down a column takes `font-variant-numeric: tabular-nums`.

## Layout & Spacing

A 4px half-step on an 8px scale: 4, 8, 16, 24, 40, 64. Gutter 24px. Maximum
width 1560px, centred, because the timeline earns width that prose would not.

The frame is one column of full-width panels, in the order the question is
asked. Verdict band, then the shared axis, then pipeline runs, then freshness,
then lineage, then incidents. Every panel takes the full width. Lineage was
tried as a narrow panel beside freshness and could not hold six columns of
readable node labels, so it has its own row.

The axis is the one sticky element, at the top edge. Nothing else sticks.

Density is high and decided once: 36px rows on a pointer device, 44px below
760px. 36px is the smallest row whose 4px hit-area inset reaches 44px without
two rows overlapping. Row padding is 8px horizontal. More space above a panel
title than below it.

Every panel's time region maps to the same function. The window start is 0%
and the window end is 100%. A bar in the runs table and a deadline mark in the
freshness table therefore sit at the same x for the same minute.

## Elevation & Depth

Panels are flat on the page, held by a 1px ring in `rule` and nothing else.
Depth is reserved for the two things that leave the page's plane: the verdict
band, and anything that floats over a row.

```css
--lift:
  0 0 0 1px oklch(0 0 0 / .055),
  0 1px 2px -1px oklch(0 0 0 / .06),
  0 2px 4px 0 oklch(0 0 0 / .035);
--lift-hover:
  0 0 0 1px oklch(0 0 0 / .085),
  0 2px 4px -2px oklch(0 0 0 / .08),
  0 8px 20px -6px oklch(0 0 0 / .09);
```

Three layers, pure black at low alpha, never a tinted near-black. On the dark
band the ambient layers are invisible, so it takes a single white ring at
`oklch(1 0 0 / .1)`. A hairline and a soft shadow never appear on the same
element.

## Shapes

Radii 4 / 8 / 12, nested concentrically: `outer = inner + padding`. A panel is
12px, a control inside a 4px inset is 8px, a chip or a bar is 4px, a pill is
`full`. Bars on the time axis take 4px on their leading and trailing ends, and a 3px
minimum width. A one-minute run is then a visible shape rather than a
hairline. A bar that starts before the window has a square left end, so a
clipped bar is not read as a short one.

## Components

Every state below is built, not assumed.

| Component | Rest | Hover | Active | Focus | Disabled | Loading | Error |
|---|---|---|---|---|---|---|---|
| Primary button | `accent` fill, `accent-ink` text, 4px | `accent-deep` | `scale(.96)`, 120ms | 2px `accent` ring, 2px offset, no fade | 40% opacity, `aria-disabled`, reason in its title | label swaps for a 3-dot pulse, width held | n/a |
| Quiet button | `surface`, 1px `edge`, `secondary` text | `inset`, `primary` text | `scale(.96)` | as above | as above | as above | n/a |
| Filter chip | `surface`, 1px `rule` | `inset` | n/a | as above | n/a | n/a | n/a |
| Filter chip, on | `band` fill, `band-ink` text | `band` | n/a | as above | n/a | n/a | n/a |
| Table row | `surface`, 1px `rule` under | `inset` under 120ms, no movement | n/a | 2px inset `accent` ring | n/a | skeleton bar at row height | row keeps its data, state cell says what failed |
| Table row, selected | `inset`, 3px `accent` left edge, title at 600 | holds | n/a | as above | n/a | n/a | n/a |
| Status chip | glyph plus word, status colour, no fill | n/a | n/a | n/a | n/a | n/a | n/a |
| Panel | `surface`, 1px `rule`, 12px | n/a | n/a | n/a | n/a | title holds, body is skeletons | message in place of the body with a retry control |
| Incident row | hairline above, 10px/16px padding, title at 16px/600 | `inset` | n/a | 2px `accent` ring | the button is absent where acknowledging cannot act | skeleton | panel message |
| Run bar | status colour at 100%, 4px | 1.5px ink ring when its row, its table or its incident is marked | n/a | n/a | n/a | track only | hatched fill for running and retrying, dashed outline for queued |
| Freshness bar | dashed outline to the deadline, solid status colour from the deadline to now | as the run bar | n/a | n/a | n/a | track only | the solid part is the overdue time and nothing else |

Rows are ordered by what needs answering, never by identifier. Runs go failed,
late, retrying, running, queued, cancelled, landed, and most recent first
inside each group. Incidents go open, acknowledged, mitigated, resolved, then
by severity. Freshness goes past SLA, at risk, due, landed.

A control that cannot act is not drawn. Some controls are different: a
customer expects them, but may not use them. Those are drawn disabled, with
the reason beside them in text and never only in a tooltip.

Selection is the wiring of this screen. Hovering or selecting a run marks
every table that run writes, every lineage node on its path and its incident
on the axis. The mark is a left edge plus a weight change, never colour on its
own.

## Responsive Behavior

Breakpoints at 1180px, 1024px, 760px and 420px. The page itself never scrolls
sideways between 320px and 1920px: `overflow-x: clip` on both `html` and
`body`, and every grid track is `minmax(0, 1fr)`.

- Above 1180px: every column shows. The verdict band is two columns.
- At 1180px: the team, domain and row-count columns leave both tables. The
  team is in the row detail instead. The verdict band becomes one column.
- At 1024px: the lineage diagram gives way to the same graph as a text
  outline, level by level. The outline is in the page at every width for a
  screen reader, and the diagram carries `aria-hidden`. Between 1024px and
  about 1180px the diagram scrolls inside its own panel. That is the one
  place on the page where anything scrolls sideways.
- At 760px: the bars leave both tables, because a four-hour axis in 300px
  tells a customer nothing. Runs keep pipeline, start, duration and state.
  Freshness keeps table, age, deadline and state. The verdict figure drops to
  60px and rows grow to 44px. The axis becomes one line: the window and now.
- At 420px: the state column widens to 30% so no status word is clipped, and
  the name column gives up the space.
- Targets are 44px below 760px. Above it a 36px row reaches a 44px hit area
  through a pseudo-element, and no two hit areas overlap.

## Do's and Don'ts

- Do keep one dominant figure. The breach count is the only thing on the
  screen set above 28px.
- Do give every status a word and a glyph as well as its colour.
- Do set every figure, clock time and identifier in mono with tabular figures.
- Do map every time region through the one window function, so two panels
  agree about where 07:30 is.
- Do say when the data was fetched, and say it louder once it is over two
  minutes old.
- Do take every figure off the screen while its source is loading or failed.
  That means the verdict count, the panel counts, the counts on the filter
  chips and the marks on the axis. A number with no source behind it is the
  worst thing this console can show.
- Do keep a panel working when another panel's source is down. Only the panel
  that lost its source shows a message.
- Don't use the accent for a status, a heading or a fill. It marks the one
  action and the current selection.
- Don't put a panel inside a panel, or a card inside a card.
- Don't add a third weight or a sixth type size to make something stand out.
  Move it up the page instead.
- Don't animate a row hover, a tab change or a filter. Those repeat; an
  entrance that repeats costs attention every time.
- Don't introduce a font the file does not list, or a colour outside the token
  block.
- Don't show a filtered-empty table as zeros. Name the filter that emptied it
  and offer to clear it.

## Agent Prompt Guide

Quick reference: ground `#edf1f1`, surface `#fbfdfd`, ink `#0e1619`, muted
`#5a686c`, accent `#0e6a70`, breach `#a32c1c`, risk `#7a5000`, ok `#1b6043`,
dark band `#0d1517`. Sans for prose, mono for every number. 88 / 28 / 16 / 13
/ 12. Spacing 4 / 8 / 16 / 24 / 40 / 64. Radii 4 / 8 / 12.

Ready prompts:

- "Add a column to the runs table. 13px mono, tabular figures, right aligned,
  `muted` header in sans at 13px, and it leaves the table below 760px."
- "Add a panel for schema changes. `surface`, 1px `rule`, 12px radius, title
  at 16px/600, rows at 34px, and it hangs off the same window function as the
  runs table."
- "Add an empty state to this panel. Say what the panel is for, give one real
  control as the next action, and set it in body text, not in a hint."
