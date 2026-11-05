# Reviewing a change

Loaded when the review is scoped to a change rather than to a screen. The
question is no longer "is this interface good" but "did this change make it
worse". Those produce different reports, and mixing them buries the answer the
author asked for.

Report what the change caused. Three findings that predate it are a courtesy.
Thirty are a different review, and one nobody asked for.

## 1. Resolve the scope before reading anything

Stop at the first match.

| Order | Condition | Review |
|---|---|---|
| 1 | `HEAD` is ahead of `git merge-base origin/<default>_HEAD` | That range and the uncommitted changes, counted separately |
| 2 | The working tree is dirty | The uncommitted changes |
| 3 | Neither | Nothing. Go to section 2 |

Record the base commit as `$BASE` and name it in the report. Every command
below uses it.

```bash
BASE=$(git merge-base "origin/$(git symbolic-ref --short refs/remotes/origin/HEAD | cut -d/ -f2)" HEAD)
git diff --name-only "$BASE" -- . ':!*lock*' ':!*__snapshots__*' ':!*/dist/*'
```

Exclude lockfiles, snapshots, generated output, vendored code and binaries,
and say what you excluded. Where the exclusions empty the scope, that is case
3.

## 2. With no change, ask

A clean tree with nothing ahead of the base means there is nothing to review.
**Never fall back to `HEAD~1..HEAD` on your own.** The last commit is whatever
happened to land, often a merge or somebody else's work.

State the facts, offer these, and wait:

- the open pull request on this branch, first where there is one;
- `HEAD~1..HEAD`, named by short SHA and subject so the choice is visible;
- a target the user names;
- a whole-surface review, which is the ordinary `ui-review` and does not use
  this file.

Never report a review of nothing as passing.

## 3. A changed file is not a surface

A diff is evidence. The review subject is the set of surfaces those files
render in.

Expand one hop to the direct importers. Expand a second hop only for design
tokens, theme values and shared primitives, where one line reaches the whole
product. Review at most five consumers across both hops, and say how many you
left. An unstated cutoff reads as completeness.

## 4. Read the removed side

A regression is invisible in the result. It exists only in the `-` lines.

```bash
git diff -U0 "$BASE" -- . ':!*lock*' \
  | grep -E '^-' | grep -vE '^--- (a/|/dev/null)' \
  | grep -E 'aria-|role=|alt=|htmlFor|scope=|focus|outline|tabindex|prefers-|lang=|dir=|text-wrap|line-clamp|overflow-wrap|tabular-nums|min-width|minmax'
```

| Removed | What it costs |
|---|---|
| `aria-label`, `aria-labelledby`, `aria-describedby`, `aria-live`, `role=` | A control or region lost its name, description or announcement |
| `alt=`, `<label`, `for=`, `htmlFor`, `scope=` | An image, field or cell lost its programmatic association |
| `<button>`, `<a>`, `<nav>`, `<main>`, `<ul>` now a `div` or `span` | Keyboard and assistive-technology behaviour traded for styling |
| `:focus-visible`, `outline`, `tabindex` | The focus ring went, or the element left the tab order |
| `prefers-reduced-motion`, `prefers-contrast` | Motion or colour now ignores the system preference |
| `min-width: 0`, `minmax(0, 1fr)`, `overflow-wrap` | The guard against a min-content blowout went. Gates rule 15 |
| A token swapped for a literal, or for a lighter value | The contrast pair may now fail. Measure it |
| `text-wrap`, `line-clamp`, `tabular-nums`, `font-feature-settings` | Wrapping, truncation or numeral alignment changed silently |
| `left` / `right` in place of logical properties, or `lang=` and `dir=` gone | Direction-aware layout or language metadata dropped |
| A user-facing string deleted or shortened | A label, error or empty state lost what it carried |

**A signal is a lead, not a finding.** It is a regression only where nothing
in the change replaces it. These replace it, so drop the lead:

- `aria-label` giving way to `aria-labelledby` pointing at visible text;
- a `role` dropped because the element became the native equivalent;
- `outline` replaced by a ring on the same `:focus-visible` selector;
- `tabindex="0"` dropped from an element that is now natively focusable;
- a literal replaced by a token of the same value;
- a physical property replaced by its logical counterpart, which is the fix;
- a string moved into the translation catalogue rather than deleted.

Read the surrounding hunk before deciding. `-U0` hides the element the
attribute came from, which is the only thing that makes it mean anything.

## 5. Status every finding by cause, not by position

| Status | Meaning |
|---|---|
| Introduced | The change created it |
| Regression | The change weakened something that was correct |
| Pre-existing | It was there before and the change did not worsen it |

A finding a changed line causes is `Introduced` or `Regression` wherever it
surfaces, including in an untouched consumer of a changed token. A finding the
change neither created nor worsened is `Pre-existing`, even three lines from a
hunk.

```bash
git blame -L <line>,<line> "$BASE"..HEAD -- <path>
```

A `^` prefix marks a line unchanged since the base. `Not Committed Yet`, or a
commit inside the range, belongs to the change.

Cap `Pre-existing` at three, highest harm first, each stated plainly as not
this change's responsibility.

## 6. Hold the change to what it claims

Read the pull request title and body, the linked issue and the commit
messages. Then look for what is absent, which a surface review cannot find
because it inspects only the states that exist.

- A new variant, size or theme applied to some states but not to all of
  hover, focus, active, disabled, loading and selected.
- A new user-facing string with no entry in the translation catalogue.
- A new component with no empty, loading, error or narrow-width state.
- A control added to one surface but not to the siblings carrying its peers.

Whether the change does too much is a process question. Do not raise it.

## Never touch the working tree

A change review is read-only, the checkout included. `git fetch` writes only
to `.git` and is allowed. `gh pr checkout`, `git checkout`, `git switch` and
`git stash` rewrite the files the author has open, so none of them is ever
allowed. Fetch the ref and read it with `git show`.

To render a target that is not the current checkout, use an isolated worktree
and remove it when done:

```bash
git worktree add /tmp/review-<n> refs/remotes/pr/<n>
git worktree remove /tmp/review-<n>
```

Where no render was possible, mark every visual claim unverified rather than
judging from the diff.

## Attribution

The change-scope model on this page is adapted from `jakubkrehel/skills`
(MIT). [NOTICE](../../ui-design/NOTICE) records what was taken and what was
changed.
