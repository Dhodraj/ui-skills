# Directions

Loaded at stage 3. It decides what the surface looks like, and it is a
different decision inside a live product than on a new surface.

## Which job this is

| Situation | The job |
|---|---|
| A new screen inside a live product | Match. Read the nearest screen, take its tokens, scale and patterns, and change nothing that is not the point of this work |
| A new surface with an existing brand | The brand fixes type and colour. Quality comes from hierarchy, rhythm, density and alignment inside it |
| A new product with nothing fixed | Decide a direction and write it down as values, before any component |
| A redesign | The old look is evidence, not a starting point. Keep the product truth, the content and the function; replace the look as a whole rather than polishing it |

The commonest failure is the third job being done when it was the first.
Inventing a second visual system inside a product that already has one costs
more than the screen is worth.

## Deal three directions, never one

For the third and fourth jobs, and only those. A single proposal is the
model's default aesthetic wearing a rationale. The user has nothing to compare
it against. So they either accept it, or ask for "something else" with no
vocabulary to say what.

Each direction gets four lines:

```
NAME        Control room
ANCHOR      One hue, taken from the subject: the amber of a late warning
TYPE        A condensed grotesque for figures, the body face for everything else
DENSITY     High. 32px rows, hairlines, no card ever
SIGNATURE   The late count is the largest thing on the screen, always
```

Rules for the three:

- **Separate them on a real axis.** Three shades of the same idea is one
  direction with three palettes. Move the density, or the structure, or what
  the screen makes dominant.
- **Take the anchor from the subject**, not the category. Look at the
  product's own world: its material, its data, its place, what it produces. A
  palette from there beats one chosen because other tools in the category use
  it.
- **Name the signature**: the one thing a customer would remember and
  describe to a colleague. A direction with no signature is a theme.
- **Say what each costs**: a licensed font, a photography budget, a component
  that must be built rather than found.
- **Never deal the same three twice.** Where a previous direction was picked
  in this repository, `DESIGN.md` records it; move away from it deliberately.

Then stop. Put the three to the user with the interactive question tool, and
do not build past it on an assumption. Where the user says "you pick", pick
one, say which and why in a line, and carry on.

On a yes, write the picked direction into `DESIGN.md` as values and lint it.
[design-system.md](design-system.md) holds the format. The two directions not
picked go in one line each under `Overview`, so a later session knows what was
already considered and rejected.

## The numbers

Defaults for building a screen. The brief can overrule any of them, and
saying which one it overrules is part of the proposal.

| What | Rule |
|---|---|
| Body text | 16px or larger, line height 1.5 to 1.6 |
| Line length | 45 to 75 characters for prose |
| Heading line height | 1.1 to 1.3 |
| Type sizes | five on a screen at most |
| Font weights | two, such as 400 and 600 |
| Text contrast | 4.5:1 for body and placeholder text, 3:1 above 18px |
| Boundary contrast | 3:1 for component edges and for focus rings |
| Tap targets | 24 × 24 minimum, 44 × 44 where touch is likely, 8px apart |
| Spacing scale | 4, 8, 12, 16, 24, 32, 48, 64, 96. Each step at least 25% above the last |
| Grouping | space inside a group smaller than space between groups |
| Headings | more space above a heading than below it |
| Primary action | one per view. Others are outlined or quiet |
| Greys | 8 to 10 of them. Text is never `#000` |
| Brand colour | 5 to 10 shades of each |
| Shadows | nothing at rest carries one. Separate surfaces by tone |
| Cards | never nested |
| Borders | no coloured side stripe wider than 1px |
| Gradients | none on text. Weight and size carry emphasis |
| Status | a word plus a shape or a position, never colour alone |
| States | empty, loading, one item, many items, error, disabled, hover |

Contrast ratios are WCAG 2.2 AA. 24 × 24 is WCAG 2.5.8. The 44px target is
Apple's Human Interface Guidelines.

## What to decide, and write down as values

- **Identity and audience.** Who the surface is for and what it should feel
  like to them, in one line each. A freight console and a children's app are
  not the same product even when both are tables.
- **The direction**, from the product's own world: its material, its data, its
  place, what it produces. A palette taken from the subject beats a palette
  taken from the category.
- **Information architecture**: what the screen is about, what is secondary,
  and what is one step away.
- **Type**: the scale as values, the two weights, the display and body
  families, and what carries emphasis.
- **Spacing and density**: the scale, and the density decision for this
  surface, made once.
- **Colour**: the roles — ground, ink, accent, surfaces, status — as values,
  with the light and dark variants if the product has both.
- **Layout**: the frame, the grid, the regions, and how each breaks at 1024
  and 375.
- **Composition and interaction patterns**: which components compose the
  screen, and how selection, filtering and detail work.
- **Motion**: where it carries meaning, and nowhere else. Transform and
  opacity, 150 to 300ms, honouring `prefers-reduced-motion`.
- **Accessibility**: the contrast floor, the focus treatment, the keyboard
  path through the screen.

## No default aesthetic

A product does not need, and does not get by default:

- a dark theme;
- a gradient, or glassmorphism;
- an animated or particle background;
- a bento grid, or a grid of equal cards;
- a coloured left stripe;
- a soft shadow on everything at rest;
- an icon in a tinted rounded square;
- a numbered section chip;
- a monospace caption under a centred headline.

Each of those is a choice that has to earn its place on this surface for this
audience. The reason to name them is that they are what a model reaches for
when nothing was decided, and together they are the look people now recognise
as machine-made.

The same applies in reverse. Taking the ornaments away is half the job. A page
stripped of its cards and rules and given nothing in their place reads as
unfinished. What replaces them is substance: larger type, whole fields of
colour, real pictures of this subject, real data, and every section carrying
its full content.

## Using a reference

A reference is read for its principles, not copied for its surface.

- Prefer a **finished screen that already solves this job** over a component
  gallery. A gallery leaves out what goes in the header, what a row does when
  it fails, and where the secondary action sits.
- Take from it: what it chose to make dominant, what it left out, how it
  handles the dense case and the failure, its density decision, how it labels
  things.
- Do not take: its palette, its brand, its illustration style or its layout
  wholesale into an unrelated product.
- Where the project keeps a reference list (`.claude/screen-references.md`),
  start there and add to it.
- Record which references were used, in the report.

## Critique: six kinds, kept apart

When judging an existing design, a finding belongs to exactly one of these,
and mixing them is why design feedback gets ignored:

1. **Product and information architecture** — wrong problem, or the system's
   internals on display instead of the customer's task.
2. **Journey and usability** — the customer cannot get from entry to outcome.
3. **Visual design** — hierarchy, rhythm, density, type, colour,
   distinctiveness.
4. **Interaction and state** — missing or lying states, controls with no
   response.
5. **Accessibility** — contrast, naming, keyboard, focus, colour-only status.
6. **Implementation** — the code behind one of the above.

Then recommend a specific change and say which customer problem it removes.
"Improve the hierarchy" is not a recommendation. "Make the late-run count the
one dominant figure, drop the other eleven tiles to a single row of labels"
is. `ui-review` holds the severity model and the report format.

## Preserve what is sound

Say what is working before changing anything, and keep it. A redesign that
silently drops a control a customer used every day has caused more harm than
the look it fixed. Where a decision looks wrong but deliberate — a dense
control row, a zoomed axis, a domain convention — ask rather than overrule.
