# The baseline run that was thrown away

This file is here as evidence, not as a result. It is not in the comparison
figure and no claim in the README rests on it.

It was meant to be the "without the skill" arm. It was run as a subagent on
2026-11-01, and subagents on that machine inherit `~/.claude/skills/`. That
directory already held version 1 of this skill, under its old name
`product-experience-engineering`, together with `component-find`,
`journey-walk`, `experience-audit` and `dataviz`.

So this arm had the skill. Its own report names the `dataviz` colour
validator. It rendered its work at 320, 1480 and 1920, in light and dark, then
fixed three faults that only looking could find. That is the skill working,
which is the opposite of a baseline.

The run was replaced by one started with `claude --safe-mode`, which disables
CLAUDE.md, skills, plugins, hooks and MCP servers. See
[docs/ab-test.md](../../../docs/ab-test.md) for the method.

The point worth keeping: a baseline is only a baseline once you have listed
what the environment hands it for free.
