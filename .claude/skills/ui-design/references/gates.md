# Gates

The floor. Read this immediately before the first UI edit, with
[craft.md](craft.md).

Every rule here exists because a model reaches for it by default. None is a
matter of taste; each one names a real customer harmed, or a real claim that
turns out to be false. The three-tier shape and several of the rules are
adapted from `Nutlope/hallmark` (MIT) and `nexu-io/open-design` (Apache-2.0).
[NOTICE](../NOTICE) records what came from where.

## The three tiers

| Tier | Meaning |
|---|---|
| **Floor** | Never waivable. Nothing ships against an open Floor rule |
| **Reflex** | The default a model falls into. Waivable, in a comment next to the code, naming the rule and a reason specific to this page. "It looks better" is not a reason |
| **Finish** | Answered, never blocking. Say what you did about each |

A waiver that names a Floor rule is not a waiver. Fix the code.

Where the project's `DESIGN.md` explicitly allows something a Reflex rule
forbids, **`DESIGN.md` wins**. Say so once in the report. Floor rules are not
overridden by a design system, because they are about truth and access rather
than style.

## Floor: honesty

The most expensive defects on this list. Each one puts a false statement in
front of a customer.

1. **No invented facts.** Never a metric, a testimonial, a customer count, a
   logo wall, a review score or a case-study figure that nobody produced.
   Where the real number is missing, leave a labelled empty slot and say in
   the report that it is empty.
2. **No fake send.** A form with nothing behind it does not print "Sent" or
   "Thanks". Leave the endpoint empty and say on the page that it is not
   connected yet.
3. **No false result.** A filter that relabels the view without filtering the
   data, a status that does not track the system, a success message for a
   failure. These are blockers, not polish.
4. **No invented customer research.** A persona, a quotation or a usability
   finding that nobody produced never goes in a report. See
   [product-thinking.md](product-thinking.md).

## Floor: access

5. **Contrast.** Body text 4.5:1. Large text, icons, focus rings and field
   edges 3:1.
6. **Focus.** Every interactive element has a visible `:focus-visible` state
   whose ring clears 3:1, and it appears instantly. A focus ring that fades in
   has already failed the keyboard user it exists for.
7. **Names.** Every control has an accessible name. A placeholder is not a
   label.
8. **Never status by colour alone.** A colour-blind customer gets the word, or
   the shape, or both.
9. **Reduced motion.** Every animation has a `prefers-reduced-motion: reduce`
   path. Setting duration to zero is enough. This is a vestibular-disorder
   accommodation, not a preference.
10. **Content without scripting.** Content is visible at rest. Hide it only
    under a class the script adds, never the other way round, or a failed
    bundle leaves a blank page.
11. **Targets.** 24 × 24 minimum. 44 × 44 wherever a thumb is expected.
12. **Decorative graphics** carry `aria-hidden`, and meaningful ones carry a
    label.
13. **Every gesture has a pointer-free path to the same outcome.** A swipe, a
    drag, a pinch or a long press is an addition, never the only way. A row
    that deletes only by swiping, or a sheet that closes only by dragging,
    locks out the keyboard and the screen reader.

## Floor: layout that breaks

14. **No horizontal scrolling at any width from 320 to 1920.** Put
    `overflow-x: clip` on both `html` and `body`. Never `hidden`, never
    `100vw`, which includes the scrollbar and so causes the overflow it
    appears to prevent.
15. **Image-bearing grid tracks use `minmax(0, 1fr)`**, never a bare `1fr`,
    and long headings carry `overflow-wrap: break-word` with `min-width: 0`.
    A bare `1fr` refuses to shrink below its content and pushes the page wide.
16. **Hero height in `dvh` or `svh`, never `vh`.** On a phone, `vh` measures
    the viewport without the browser's own chrome, so the first screen is
    taller than the screen.
17. **One sticky element per edge.** Two `position: sticky; top: 0` siblings
    cover each other.
18. **No text painted under another element.**
19. **No button label or primary navigation link that wraps** at any width.

## Floor: the system

20. **Every colour and every font goes through a token.** No literal colour
    and no `font-family` past the token block. This is what makes a theme
    change possible and a review mechanical.
21. **Never `transition: all`.** Name the properties. And never animate
    `width`, `height`, `top` or `left`: each forces layout on every frame.
    Animate `transform` and `opacity`.
22. **One containment layer.** No card inside a card.

## Reflex: the look a model defaults to

Each of these is what the training data holds, not what the product needs.

23. **The purple-to-blue gradient**, and its relatives: blue to cyan, indigo
    to pink, and any of them clipped to text. Use one anchor hue and solid
    ink. A flat surface with deliberate type beats a gradient every time.
24. **The framework default palette.** `indigo-500`, `violet-600`,
    `slate-900` and the rest of the Tailwind defaults as the accent colour.
    Mix a colour instead, in OKLCH.
25. **Pure `#000` and pure `#fff`** outside the token block. Tint the paper
    and the ink toward the anchor hue, even slightly. Pure black on pure white
    is the only palette no designer chose.
26. **Emoji as icons.** Use a single icon set, drawn at a consistent stroke
    width, taking `currentColor`. Never two icon libraries in one product.
27. **Three equal columns of icon-above-heading tiles**, each icon in its own
    rounded coloured square. Vary the widths and heights, or set the icons
    inline.
28. **The template page**: hero, three features, call to action, footer, in
    that order, with every section separated by the same gap. Decide the
    sections from what the visitor needs, and vary how they are separated.
29. **The default system face as the display face.** Inter, Roboto, Open
    Sans, Poppins and Lato are the body-text defaults of the last decade. A
    display face is a decision; make one.
30. **Tracked capitals as a label.** Small text set uppercase and letter-
    spaced reads as a generated eyebrow. Use the body face, one step smaller,
    in sentence case.

    > This contradicts `open-design`'s craft rules, which require at least
    > `0.06em` letter-spacing on capitals. Both cannot hold. The rule here is
    > about not reaching for capitals in the first place; theirs is about
    > setting them well once chosen. Where `DESIGN.md` calls for capitals, set
    > them with the tracking and waive this rule.

31. **Decorative chrome with no content**: blurred floating orbs, aurora
    blobs, wave dividers, rotated vertical text, scroll hints, a "BETA" pill,
    a mono-capitals strip, a `01 / 04` counter.
32. **Template punctuation**: middle dots joining metadata, an arrow glued to
    link text, a coloured dot that means nothing. Use a line break, a column
    or a hairline.
33. **More than three font families**, or more than three weights.
34. **The same hover on everything.** One signal per element, applied to the
    container, not three effects at once.
35. **Overshoot easing on a state change.** A bounce says "look at me" where
    the customer asked for a result.
36. **Placeholder images from an external service**: `unsplash.com`,
    `placehold.co`, `picsum.photos`. Ship a real asset, or an empty slot the
    page admits is empty.
37. **A drawn browser window, phone frame or terminal.** Capture the real
    product instead. One device frame holding a real screenshot is allowed.
38. **Prose measure outside 45 to 75 characters.** Set `max-width: 65ch`.
39. **Running text below 16px**, or any text below 12px.
40. **The accent used everywhere.** It is a signal, not a fill. Past roughly
    5% of the viewport it stops signalling anything.

## Reflex: copy

41. **Buzzwords**: seamless, robust, empower, supercharge, elevate,
    effortless, unlock, unleash, next-generation, world-class, best-in-class,
    industry-leading, mission-critical, future-proof. Say what the product
    does, in the nouns its customers use.
42. **Placeholder names and clichéd companies**: Jane Doe, Acme, Nexus, lorem
    ipsum, "Feature one / Feature two".
43. **The rebuttal cadence** used more than once: "Not a tool. A platform."
44. **The stock second action**: "Learn more" beside "Get started", where
    neither says what happens next.
45. **A section heading that is a category label**: Features, Our story, Why
    us. Say what the visitor gets there.
46. **The swap test.** If a headline would work unchanged on a competitor's
    page, it says nothing. Rewrite it.

## Finish: answer each, never blocked by it

- `text-wrap: balance` on display headings, `pretty` on body.
- Tabular figures wherever numbers are compared down a column.
- One filled primary button recipe, not two.
- `::selection`, `caret-color` and `scrollbar-color` themed rather than left
  at the browser's blue.
- No orphan on the last row of a grid.
- No one-word last line in a heading.
- A hairline border and a wide soft shadow on the same element: pick one.

## Before handing back

Walk the Floor list against what you built, not from memory of having
intended to follow it. Then say, in the report:

- Floor rules: all clear, or which one and why it is still open.
- Reflex rules waived: each with its reason.
- Finish: what you did.
