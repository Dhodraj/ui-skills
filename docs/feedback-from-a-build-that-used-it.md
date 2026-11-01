# Feedback from a build that used it

Written 29 October 2026, after about twenty two hours of building a browser
application with these skills as the standard. Fifteen screens, several
rebuilt two and three times after the person who commissioned them drove the
running product and said what was wrong.

Everything below cost time or nearly shipped. None of it is a guess.

## What the skills got right, measured rather than assumed

**Driving the product found what reading the code did not.** Every fault that
mattered came from opening the running application. Not one came from a
review of a diff. The stage that insists on this is the stage that earned its
place.

**Searching the project before the registries was right, and underused.** The
answer to "which timeline component should this use" turned out to be a
component the product already had, built for a different screen. Every
registry candidate wanted a dependency the project had decided against. The
skill says to search the project first. A builder that skips that step writes
a second implementation of something it owns.

**Building every state rather than the happy path.** A screen built on one
fixture hides every other one, and that is exactly how a refusal state shipped
saying nothing useful. The state list is the most valuable thing in the
journey stage.

## Where it needs more

### "This feels clumsy" is a finding, and the skill has nowhere to put it

The most common thing the person said was not a fault. It was "this feels
clumsy", "this will be hard for people to understand", "this can be
modernised". Three of six surfaces in one round.

They named no defect, and that is the finding. A screen that is correct,
accessible, matches the system and still reads as clumsy is failing at
something the measurements do not reach.

What worked: the builder had to drive each screen, write down what it thought
was wrong, and only then change anything. Rearranging a screen before you can
say what was wrong with it produces a different screen, not a better one.

The audit stage grades severity on harm. It has no row for a screen that
harms nobody and is still not good enough. That row is where most real
feedback lands.

### An audit cannot see what a crashing gate hides

The project's own screen gate crashed before opening a single screen. It had
been red for hours while every other check was green, because the continuous
integration run did not include it.

When it was repaired, it immediately reported three real faults it had been
unable to reach. One was a control under the minimum target size, on three
widths.

A measurement stage should treat "the tool exited non zero before measuring"
as a distinct and louder outcome than "the tool found nothing". They are
opposite results and they look the same in a log.

### A screen gate reaches only the states it has a case for

The gate drove fourteen screens and had no case at all for a fifteenth, added
the day before. It said so, which was good. Nothing connects a new route to
the requirement that it be driven.

Worth a stage of its own. Derive the screen list from the application's own
routes, and fail on a route with no case. A hand written list does not keep
up.

### The test must cross the boundary the fault lives on

The worst defect of the build: one side wrote an identifier into a field, the
other side read that field by display name. They never matched, the request
refused silently, and a whole capability was unreachable.

Every test passed. The writing side's test asserted the field carried the
identifier. It never drove the reading side.

Each side's test proved that side's own behaviour and neither proved the
product. Testing guidance should say this plainly: a test that exercises one
side of a contract proves one side of a contract.

### A passing test is not evidence until it fails without the fix

After repairing the above, the fix was mutated back and the test failed. That
is the only thing that made the passing test mean anything.

The regression stage should require this for any fix that claims to close a
defect. It is one command and it is the difference between a test and a
decoration.

## Smaller things that cost time

**Keep the exact words.** Briefs that quoted what the person said produced
better work than briefs that summarised it. A paraphrase of a judgement loses
the judgement. "This feels clumsy" and "the layout could be improved" are not
the same sentence.

**A history that lists versions and cannot open one is a list of labels.**
This shipped twice, in two different places, because listing was read as the
requirement. If a screen names a thing, ask whether a person can open it.

**Section names outlive the navigation.** Five screens carried a hard coded
section name for a place that had been removed from the rail weeks earlier.
Renaming a place should come with a search for everything that names it.

**The first screen was the last one anybody looked at.** The sign in screen
stayed two fields on an empty page through the whole build, while every screen
behind it was rebuilt three times. It is the only screen every customer sees.
Any audit that works from the navigation will miss it, because it is not in
the navigation.

**A component decision needs writing down once.** One rule was settled early.
Copy a component's code into the repository, and never add the dependency. It
had to be re established three times, because it lived in a chat rather than
in the project's own notes. The skill reads a project's notes. It cannot read a
decision nobody wrote down.
