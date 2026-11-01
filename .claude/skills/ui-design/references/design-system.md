# The design system file

One file, `DESIGN.md`, at the project root. It has two layers that do
different jobs:

- **Tokens**, in YAML, which are the values anything mechanical reads: the
  linter, the Tailwind export, and you at stage 7.
- **Prose**, in markdown sections, carrying what the tokens cannot: the mood,
  the guardrails, what happens at a phone width, and how to brief an agent on
  this product.

The token layer follows the `design.md` specification from
`google-labs-code/design.md` (Apache-2.0), because it is the only one of these
formats with a working linter. The section list is the longer one that
`VoltAgent/awesome-design-md` (MIT) uses. It adds two sections the
specification lacks and every real product needs: responsive behaviour, and
the brief an agent gets handed.

`DESIGN.md` at the project root is read by other tools too, which is most of
the reason to put it there rather than inside this skill. `Nutlope/hallmark`
treats it as the locked system that overrides its own defaults.
`nexu-io/open-design` injects it ahead of every other instruction.
`shadcn/improve` reads it alongside `PRODUCT.md` and the repository's ADRs
when it audits a codebase. A file written here is understood by all three.

## The shape

````markdown
---
name: Acme Data Platform
description: Operational console for data engineers on call

colors:
  primary: "#1A1C1E"
  secondary: "#6C7278"
  accent: "oklch(62% 0.18 250)"
  accent-ink: "#FFFFFF"
  neutral: "#F7F5F2"
  surface: "#FFFFFF"
  on-surface: "#1A1C1E"
  error: "#B4420F"

typography:
  h1:      {fontFamily: Public Sans, fontSize: 48px, fontWeight: 600, lineHeight: 1.1, letterSpacing: -0.02em}
  h2:      {fontFamily: Public Sans, fontSize: 32px, fontWeight: 600, lineHeight: 1.15}
  body-md: {fontFamily: Public Sans, fontSize: 16px, fontWeight: 400, lineHeight: 1.6}
  body-sm: {fontFamily: Public Sans, fontSize: 14px, fontWeight: 400, lineHeight: 1.5}
  label:   {fontFamily: Public Sans, fontSize: 13px, fontWeight: 500, lineHeight: 1.4}

spacing:
  base: 16px
  xs: 4px
  sm: 8px
  md: 16px
  lg: 32px
  xl: 64px
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
    padding: 12px
  button-primary-hover:
    backgroundColor: "{colors.primary}"
---

# Acme Data Platform

## Overview
## Colors
## Typography
## Layout & Spacing
## Elevation & Depth
## Shapes
## Components
## Responsive Behavior
## Do's and Don'ts
## Agent Prompt Guide
````

## Rules the format imposes

- Sections that are present must appear **in the order above**. Any may be
  omitted. A duplicate section heading or a duplicate top-level YAML key
  rejects the file.
- A token reference is `{path.to.token}`, for example `{colors.primary}`.
  Outside `components` it must resolve to a single value, never to a group.
- A dimension is a number plus `px`, `em` or `rem`. Nothing else is valid.
- A colour may be any valid CSS colour: hex, `rgb()`, `hsl()`, `oklch()`,
  `oklab()`, `lab()`, `color-mix()`. Hex is the safe default; `oklch()` is
  better for an accent you want to tune by lightness and chroma.
- `lineHeight` as a bare number is a multiplier, which is what you want.
- Where a section does not apply, record it rather than leaving a hole:
  `omitted: [{section: rounded, reason: "The brand book defines no radii"}]`.

## What goes in the prose sections

| Section | What it must answer |
|---|---|
| **Overview** | What this product is, who uses it, and the feeling. Dense or spacious, warm or cool, playful or utilitarian. This is the fallback whenever no token covers a decision |
| **Colors** | Each colour's **role**, not only its value. Which one is the single most important action. What never gets the accent |
| **Typography** | The full hierarchy as a table, and the rule for which level a thing takes |
| **Layout & Spacing** | The grid, the maximum width, the gutter, and the scale. Say the step size and the half-step |
| **Elevation & Depth** | The shadow system, or, for a flat product, what replaces it: hairlines, a tinted surface, contrast |
| **Shapes** | The radius language, and the rule for nesting |
| **Components** | Buttons, inputs, cards, navigation, tables, with **every state**: rest, hover, active, focus, disabled, loading, error |
| **Responsive Behavior** | Breakpoints, the minimum target size, and what collapses at a phone width. This is where most generated screens fail |
| **Do's and Don'ts** | The guardrails, each specific enough to catch a real mistake |
| **Agent Prompt Guide** | A quick colour and type reference, and two or three ready prompts. This section exists so a future session needs to read one file, not ten |

Write `Do's and Don'ts` as things an agent could actually violate:

> - Do use the accent for one action per screen, and no more.
> - Do keep every status readable without colour: a word or a shape too.
> - Don't put a card inside a card.
> - Don't mix rounded and square corners in one view.
> - Don't introduce a font the file does not list.

## Gate the file

```bash
pnpm dlx @google/design.md lint DESIGN.md
```

Published as `@google/design.md`, version 0.4.0 on 2026-07-27, under
Apache-2.0. It exits 1 on an error. Eleven rules, of which four catch real
defects before any code is written:

| Rule | Catches |
|---|---|
| `broken-ref` (error) | A `{token}` reference that resolves to nothing |
| `contrast-ratio` (warning) | A component whose background and text pair falls below **4.5:1** |
| `missing-primary` (warning) | Colours defined with no `primary` |
| `missing-typography` (warning) | Colours defined with no type scale at all |

The rest report section order, orphaned tokens, typo'd keys and counts.

Turn the approved file into code rather than retyping it:

```bash
pnpm dlx @google/design.md export --format css-tailwind   # a Tailwind v4 @theme block
pnpm dlx @google/design.md export --format dtcg           # W3C design tokens JSON
```

Check in the generated file alongside `DESIGN.md`. A token that exists in one
and not the other is the drift this file exists to stop.

## Route A: codify a system that already exists

The project has tokens, a theme or a brand. The file records what is there.
**Read values; never estimate them.**

1. Find the source of truth: a token file, `tailwind.config`, a theme object,
   a `:root` block, a Figma export.
2. Where the only source is the live product, render it and read the computed
   values rather than guessing from a screenshot:

   ```js
   // via browser_evaluate, on the real page
   () => {
     const seen = {color: {}, size: {}, radius: {}, family: {}};
     for (const el of document.querySelectorAll("*")) {
       const s = getComputedStyle(el);
       for (const [k, v] of [["color", s.color], ["size", s.fontSize],
                             ["radius", s.borderRadius], ["family", s.fontFamily]]) {
         if (v && v !== "0px" && v !== "none") seen[k][v] = (seen[k][v] || 0) + 1;
       }
     }
     return Object.fromEntries(Object.entries(seen).map(([k, v]) =>
       [k, Object.entries(v).sort((a, b) => b[1] - a[1]).slice(0, 12)]));
   }
   ```

   The counts matter: a value used 400 times is the system, a value used once
   is drift. Record the first; list the second under `Do's and Don'ts` as
   something to remove.
3. Name each token for its **role**, never its appearance. `accent`, not
   `blue`. A product that renames `blue` to green ships a lie in its token
   file.
4. Write what you found, then lint. Where the lint reports a contrast failure,
   that is a finding about the product, not about the file. Record it; do not
   quietly improve the number.

## Route B: a new system

No brand, no tokens, nothing to codify. The file is a proposal until the user
picks a direction at stage 3 of `ui-design`.

Start from these defaults and change them for a reason, not for variety:

| Thing | Default | Why |
|---|---|---|
| Spacing | 4px half-step on an 8px scale: 4, 8, 16, 24, 32, 64 | Everything lands on a shared grid without arithmetic |
| Body | 16px at line-height 1.6, measure 45–75ch | Below 16px, reading copy gets refused on a phone |
| Type levels | 9 to 15, built on a ratio near 1.25 | Fewer reads as flat, more is unmaintainable |
| Radii | 4 / 8 / 12, concentric when nested | Equal radii on nested surfaces make the inner one look pinched |
| Container | 1200–1500px, centred, gutters that grow | Past 1920 the measure must not grow with the window |
| Contrast | 4.5:1 body, 3:1 large text and controls | WCAG 2.2 AA |
| Target size | 24 × 24 minimum, 44 × 44 comfortable | WCAG 2.5.8 and Apple's guidance |

These are starting values, not a house style. The direction the user picks at
stage 3 overrides any of them, and `Do's and Don'ts` records the override so
the next session does not undo it.

## The one thing this file is for

Every screen built after it is measured against it. `ui-review` reads it
first and treats drift from it as a finding. That only works if the file
describes the product that exists. When a build has to depart from it, change
the file in the same piece of work. **The change is not done while
`DESIGN.md` contradicts the product.**
