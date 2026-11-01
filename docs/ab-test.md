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
