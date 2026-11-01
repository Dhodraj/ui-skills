# The same prompt, with and without the skill

One blunt prompt, given twice. Once to a stock Claude Code with nothing
installed, once to a Claude Code that has `ui-design`. Both builds are in this
repository, and so are the measurements.

## The prompt

Word for word, to both arms:

> Build a data platform observability console. It should show pipeline runs,
> data lineage, table freshness, and incidents.
>
> Write it as one self-contained HTML file at `<dir>/index.html`. Inline all
> CSS and any JavaScript. No build step, no external network requests, no CDN
> links, no web fonts fetched over the network. It must open directly in a
> browser and render fully offline.
>
> There is no existing codebase. Work only inside `<dir>/` and do not read
> files outside that directory.

No audience. No states. No design direction. No acceptance criteria. This is
the kind of prompt people really write, and the reason a skill has to supply
what the prompt leaves out.

## The two arms

| | Baseline | With the skill |
|---|---|---|
| Started with | `claude --safe-mode`: no CLAUDE.md, no skills, no plugins, no hooks, no MCP servers | The same prompt, plus `ui-design` and its reference files |
| Model | opus | opus |
| Output | one self-contained HTML file | one self-contained HTML file, plus the `DESIGN.md` the skill writes first |
| Could look at its own render | **No.** A fresh install has no browser tooling | Yes |
| Could ask the operator anything | No | No. Where a stage says to ask, it had to infer and say what it picked |

The baseline is a fresh install, deliberately. `--safe-mode` is Claude Code's
own switch for starting with every customization off. So the arm is not a
strawman anybody built. It is what a new user has on the first day.

Two things follow from that, and both favour the baseline being worse:

- **No browser.** Looking at the render is most of what the skill insists on,
  and a fresh install cannot do it. Where the comparison shows a fault the
  baseline could not have seen, that is noted rather than scored.
- **No CLAUDE.md.** The operating instructions on this machine already demand
  verification against the real thing. Leaving them in would have measured
  those instructions, not this skill.

## The run that was thrown away

The first baseline attempt was run as a subagent, and it was invalid. Agents
on this machine inherit `~/.claude/skills/`, which already held version 1 of
this skill under its old name, along with `dataviz` and others. That arm used
the `dataviz` colour validator by name and rendered its work at three widths.
It was a good build and it was not a baseline, so it was set aside rather than
published. Its output is kept at `demo/ab/discarded-contaminated-arm/` with
this note attached.

The lesson is worth more than the run: **a baseline is only a baseline once
you have listed what the environment hands it for free.**

## Two more things that went wrong, recorded

**The arms shared a browser.** The first two runs both reached for the same
Playwright MCP server, and one navigated the other's tab mid-measurement. The
skilled arm noticed, abandoned the shared browser, ran its own headless
Chrome, and redid the readings it could not trust. Any later run of this
comparison should give each arm its own browser rather than relying on an
agent to catch the collision.

**Both arms hit a rate limit mid-run.** The baseline died before writing a
byte and was started again from nothing. The skilled arm was resumed from its
own transcript rather than restarted, so its build is one continuous piece of
work, not two. Neither arm was given extra instructions on the way through.

## How the two were measured

Identical treatment, in the same browser session:

1. Each file served from its own directory over `127.0.0.1`.
2. Screenshot at 1440 × 900 and at 375 × 812.
3. `scripts/measure.js` run against each page at both widths, through
   `browser_evaluate`.
4. The numbers pasted into `demo/ab/figure.html`, which composes the figure
   from the real screenshots. Nothing in the figure is redrawn.

## What this is not

One prompt, one run each. It shows what the skill changed on this run, on this
subject, with this model. It is not an average, it is not a benchmark, and a
second run of either arm would differ. Where a difference below is small
enough that a re-run could reverse it, it says so.

## What came out

Both builds are in this repository: `demo/ab/stock/index.html` and
`demo/ab/with-skill/index.html`, with the design system the skilled arm wrote
at `demo/ab/with-skill/DESIGN.md`.

![The same prompt, at 1440](images/ab-1-wide.png)

![The same prompt, at 375](images/ab-2-phone.png)

### Measured at 1440

| | Baseline | With the skill |
|---|---|---|
| Text below its contrast floor | 23 | **0** |
| Text under 12px | 47 | **0** |
| Console errors on load | 1 | **0** |
| Distinct radius values | 8 | **6** |
| Interactive elements | 10 | 83 |
| Targets under 24 × 24 | 0 | 0 |
| Level 1 heading | 1 | **0** |

The last row goes the wrong way. The skilled arm shipped no level 1 heading
at all, and its own review did not catch it. That is a real defect against a rule
its own `gates.md` cares about. It is in the figure anyway. Leaving it out
would make the comparison a sales page.

The worst contrast failure in the baseline was 3.5:1 where 4.5:1 is required,
on the product name and the workspace label.

Both pages generate their own sample data, so counts move by one or two
between loads. Contrast ran 23 to 24 and the 12px count ran 47 to 49 across
reloads. Neither page scrolls sideways at 375.

### The number that looks wrong until you split it

`measure.js` counts 10 small targets in the baseline and 83 in the skilled
arm, which reads as the skilled arm being five times worse. It is not. The
script lumps two different things into one count: **under 24 × 24 is a WCAG
2.5.8 failure, under 44 × 44 is only Apple's comfort guidance.** Split apart:

| | Baseline | With the skill |
|---|---|---|
| Under 24 × 24, at either width | 0 | 0 |
| Reaching 44 × 44 at 1440 | 0 of 10 | 0 of 83 |
| Reaching 44 × 44 at 375 | 1 of 10 | 47 of 83 |

Neither page has a single WCAG target failure. The skilled arm grows its
controls at a phone width and the baseline barely does. This is a fault in the
measuring script rather than in either build. A count that merges a blocker
with a preference gets read as the blocker.

### What the skill changed that no script can see

The two screens answer different questions. The baseline reports four equal
figures: a success rate, a run count, a breach count, an incident count.
Nothing is larger than anything else, so nothing says what to do now.

The skilled arm names one figure and the consequence behind it:

> **4** tables past their freshness deadline.
> `analytics_prod.mart_finance_revenue_eu` is 1h 42m past its 07:00 deadline,
> so the 09:00 finance pack is not safe.

Then two actions, and a shared time axis every panel hangs off. A failed run,
the tables that went stale behind it, and the incident that records it all
line up at the same x.

### What the skill caught in its own work

The skilled arm reviewed its build with fresh eyes before finishing, and
found eight statements in its own interface that were false:

- the incident summary counted a mitigated incident as resolved;
- an incident was marked mitigated while its run was still degrading;
- a table showed a green "landed" state while lineage called the same table
  stale input;
- an incident title claimed six days against a five-hour span;
- a warning triangle sat permanently on a fresh timestamp;
- a table showed 0 rows and 0 partitions beside a load time;
- "queued behind a failure" was true of one run in four;
- acknowledge permissions did not follow the on-call the page itself stated.

None of those is a visual defect. No linter reports any of them. They are
what `gates.md` calls floor rules on honesty, and they are the part of this
comparison that would survive someone disliking the design.

### What the baseline could not do, and said so

Its closing words, unprompted:

> I haven't tested it in a browser: I wasn't allowed to run Node or a browser
> here, so I only read through the script by hand.

That is the honest answer from an install with no browser tooling. It is also
most of the gap: a fresh install cannot look at what it made.
