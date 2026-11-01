---
name: ui-review
description: "Review a user interface that already exists and report what harms its customers, with evidence and severity. Use when asked to audit, review, critique or check a page, screen, dashboard, component or flow, to test a journey such as signup or checkout, to check a screen on a phone, or before claiming a change works. Also use when the user says a product looks off, looks AI-generated, feels dated or asks whether it is ready to ship. Works against a running application or a Storybook, measures the live render, walks the journey as a customer, checks the result against the project's design principles, and sorts findings by harm. Two modes: review only, which changes no files, and review and fix."
---

# UI review

Two modes, and the mode is settled before anything else.

| Mode | Triggered by | What it may do |
|---|---|---|
| **Review only** | "review", "audit", "critique", "what is wrong with", "is this ready" | Read, render, measure, walk, report. **It changes no files.** |
| **Review and fix** | "review and fix", "find and fix", "improve", or an explicit yes to an offer | Everything above, then the approved fixes, then verification |

State the mode in the first line of the reply. A review-only request never
becomes a redesign because the findings were bad. Where fixing is the obvious
next step, finish the report and offer it.

## 1. Find the design principles first

A review with no stated principles is taste, and taste loses arguments. Decide
what this interface is being measured against, in this order:

1. **`DESIGN.md` at the project root.** Its tokens and its Do's and Don'ts are
   the first standard. Run `pnpm dlx @google/design.md lint DESIGN.md` and treat
   what it reports as findings about the system itself.
2. **The project's own system in code**: a token file, a theme, a Tailwind
   config, a component library. Values off that system are drift.
3. **[gates.md](../ui-design/references/gates.md)**, the floor that holds for
   any product.
4. **[craft.md](../ui-design/references/craft.md)**, the layer above the floor.

Where the project has none of the first two, say so in the report and review
against 3 and 4 alone. Offer to write a `DESIGN.md` with `ui-design`; do not
write one inside a review.

## 2. Pick the target: running app, Storybook, or both

| Target | Use it for | How |
|---|---|---|
| **The running application** | Journeys, real data, real density, anything a customer meets | The project's own `dev` or `start` script |
| **Storybook** | A component's states and variants in isolation, including the ones the app never shows | `storybook dev -p 6006`, then one story per state |
| **A static page** | A mock, a landing page, an exported build | Serve the folder; never open it as a `file:` URL |

Find Storybook before assuming it is absent: look for `.storybook/`, a
`storybook` script in `package.json`, or `*.stories.*` files. Where both a
Storybook and a running application exist, use both. **Storybook alone cannot
close a review.** It shows components, never the journey, the real data or the
page they sit on together. Say which target each finding came from.

In Storybook, walk the story index rather than guessing names. Open
`/index.json` on the running Storybook for every story id. Then visit each as
`/iframe.html?id=<story-id>`. That URL renders the component alone, with no
surrounding chrome to measure around.

## 3. Measure the render

Measure at 1440, 1024 and 375.
[playwright.md](../ui-design/references/playwright.md) has the mechanics.

Pass the whole contents of `.claude/skills/ui-design/scripts/measure.js` as the
`function` argument of `browser_evaluate`, once per width. It returns overflow,
text contrast, target sizes, controls with no accessible name, heading order,
unsized media and text below 12px. It also returns a census of the type sizes,
radii, shadows and font families the page really uses.

Every number the script returns is a candidate, not a finding. Confirm it on
the render, decide whether it harms a customer, and fold one cause into one
finding.

## 4. Look at the screenshots, in this order

The script cannot see hierarchy, rhythm, crowding, visual weight or whether
the page is any good. Open every screenshot at every width and walk these
seven. Each one is a failure that renders wrong while the source reads fine.

1. **Placement.** Is the main element where it belongs? Centre what should be
   centred and align the rest to the grid. Is there a large empty region
   carrying nothing?
2. **Containment.** Does any text, number or label cross or touch the edge of
   its card, column or button? Right-aligned values and long identifiers fail
   here first.
3. **Duplication.** Does the screen offer the same action twice? Does a
   control the customer had before still exist?
4. **Reachability at 375.** Is every control on screen? Is each target at
   least 24 × 24, and 44 × 44 where a thumb is expected? Does anything need a
   horizontal scroll?
5. **Contrast.** Can you read every label against its own background,
   including the pressed, hovered and selected states?
6. **Rhythm.** Do the spacing and type sizes come from the product's scale, or
   were they chosen for this screen alone?
7. **State.** Render the states a customer will meet: empty, loading, one
   item, many items, error. A screen built on fixtures hides all but one.

Say which of the seven each finding is. Where you could not render, say so
rather than judging from the source.

## 5. Walk it as a customer

A DOM is not an experience. Skip this only for a single static element.

Write the journey down **before** opening the browser, so what you expected is
recorded independently of what happened:

```
JOURNEY: reassign a late run
ENTRY:   /runs, signed in as a dispatcher, 40 open runs seeded
GOAL:    the late run is assigned to another operator
STEPS:   1 find the late run  2 open it  3 choose an operator  4 confirm
MISTAKES TO TRY: confirm with nobody chosen; reassign a finished run;
         lose the connection mid-save
```

Then walk it, recording what the customer meets rather than what the code
intends:

```
STEP 2: open the late run
  SEES:     <what is on screen, from the snapshot or the screenshot>
  EXPECTS:  <what a customer would assume happens next>
  HAPPENS:  <what actually happened>
  EVIDENCE: <the screenshot file, or the snapshot line>
  FRICTION: none | low | high | blocker
  WHY:      <what caused it: a label, a missing state, a dead control>
```

Every row needs evidence that exists. A row with no screenshot and no snapshot
behind it does not go in the report. "The customer would feel confused" is
usable only when the row says what confused them.

**The states a happy path hides.** Walk each one, for the surface under test.
"Not reachable here" is a valid result as long as it is recorded.

- **Empty**: no rows yet, first use. Does it say what to do next?
- **Loading**: feedback inside a few hundred milliseconds, and does the layout
  hold its size?
- **One item**, and the **densest real case**: the real row count, the longest
  identifier, the deepest nesting.
- **Failure**: a request that fails, a validation that rejects, a timeout. Is
  the message specific, next to the thing that failed, and recoverable?
- **Recovery**: can the customer get back without starting over or reloading?
- **No permission**: what does a customer who may not do this see?

**The mistakes.** Submit the form empty, and with one field wrong. Press the
primary action twice. Go back mid-flow, then forward. Reload halfway through.
Paste a long value, a value with spaces, an emoji, a very large number. Open a
link in a new tab and carry on in the first.

**At 375.** Walk the whole journey at a phone width, not only look at it. A
journey that works on desktop and dead-ends on a phone is a blocker. Check
every control reachable without horizontal scrolling, targets at least
24 × 24 and comfortably 44 × 44, the primary action reachable with a thumb.

When the application cannot be started or reached, stop. Say which it was — no
server, no credentials, no data, a failing build — and mark every journey claim
unverified. Reading the source is not a substitute.

## 6. Sort every finding into its kind

The kinds matter because they go to different people and cost different
amounts to fix. Give each finding exactly one.

1. **Product and information architecture.** The screen solves the wrong
   problem. Or it shows the system's internals rather than the customer's
   task. Or it buries what the job needs behind what the database holds.
2. **Journey and usability** — the customer cannot get from the entry point to
   the outcome without confusion, a dead end or a detour.
3. **Design system** — values off the system in `DESIGN.md` or the token file:
   a hard-coded colour, an off-scale spacing, a third radius, a fourth font.
4. **Visual design** — hierarchy, rhythm, density, type, colour,
   distinctiveness. Nothing dominates; twelve equal tiles; a page that could
   belong to any other product.
5. **Interaction and state** — a missing empty, loading, error or recovery
   state; feedback that lies; a control with no response.
6. **Accessibility** — contrast, naming, keyboard operability, focus, status
   carried by colour alone, heading structure, reduced motion.
7. **Implementation** — the code behind one of the above: a hard-coded
   palette, a fixture that hides the dense case, a layout that cannot hold
   long content.

## 7. Severity, by harm to the customer

Never by how easy the fix is.

| Severity | Meaning |
|---|---|
| **Blocker** | Stops a core task, loses data, misleads: the primary action unreachable, a keyboard trap, a figure that misstates the data, a success message for a failure, a form that prints "Sent" with nothing behind it |
| **Critical** | Major harm on a common path: body or action text below 4.5:1, horizontal scrolling at 375, a missing empty or error state on a primary flow, a control with no accessible name, a placeholder used as the only label, an invented metric or testimonial |
| **Warning** | Degrades the experience: off-system spacing, values off a shared scale, weak hierarchy, duplicated actions, a forgettable public page |
| **Polish** | Refinement: a widow, nested corners, a few pixels of asymmetry |

Torn between two, take the lower and say why. An inflated review gets ignored.

Order the report by harm, then by how many customers meet it, then by how
often, then by business consequence. Where two findings share a cause, the
cause is the finding and the occurrences are its evidence.

## 8. What not to flag

- **Deliberate, consistent choices.** A dense 32px control row on a
  pointer-first console, a labelled zoomed axis, an established domain
  convention. Ask about it; do not score it.
- **Anything `DESIGN.md` explicitly allows.** The project's own system
  outranks the general floor. Where the two disagree, say so once and follow
  `DESIGN.md`.
- **Wiring a static page cannot have**: a `#` link, a form with no endpoint.
  List those once, unscored, as wiring to confirm. The exception is a result
  that is false, such as a filter that relabels the view but not the data.
- **House rules that are not standards.** 12px labels and table headers are
  normal in dense data interfaces. Flag reading copy below 16px on a reading
  page; do not invent a floor.
- **Missing dark mode**, unless the product has or promises one.
- **Taste.** Every finding names who it hurts and how.
- **The same cause twice.**

## 9. Finding format

```
C2. Run status shown by colour alone  (.run-row .status, 40 rows)
Kind:   accessibility
Found:  status is a coloured dot with no word or shape; green and amber
        measure 2.9:1 against the row background
Why:    a colour-blind dispatcher cannot tell a late run from a running one,
        which is the screen's whole job
Fix:    add the status word beside the dot and give each status its own shape
Evidence: shots/1440-runs.png, measure-1440.json contrastFailures[3]
```

One line each for polish items. Keep `Found` and `Why` to one or two lines.

## 10. The report

```
# UI review: <surface>
Mode: review only | review and fix
Measured against: <DESIGN.md | the token file | gates.md and craft.md alone>
Checked: <widths, states, journeys walked, target, browser>
Stood in for: <fixtures for live data, a seeded account, a local font>
Not checked: <what the environment could not reach>

Top three: <three ids, one line each>

## Blockers / Critical / Warnings / Polish
<findings in the format above, each with its kind>

## States
empty <result> · loading <result> · dense <result> · error <result>
· recovery <result> · permission <result | not reachable>

## What is sound
<the decisions worth keeping, so a later session does not undo them>

## Questions
<at most three, each with the assumption made meanwhile>
```

Record what is sound. A review that lists only faults invites a rewrite of
things that were right.

No score out of 100. A number invites arguing about the number instead of
fixing the blocker, and the severity list already carries the order.

## 11. In review-and-fix mode

1. Get the fix list agreed, or fix only blockers and critical findings and say
   that is what you did.
2. Identify the journeys that already work, before editing.
3. Fix one cause at a time. Re-measure and re-render after each.
4. Walk the journey again. A fix nobody re-walked is a claim, not a result.
5. Run the project's checks for the area changed, then the broader suite once.
   [regression.md](../ui-design/references/regression.md) has the rules,
   including how to tell your failures from the ones already failing.
6. Report each finding as fixed, left with a reason, or not verified.

Stop every server you started. Record the port and how to stop it while the
review runs.
