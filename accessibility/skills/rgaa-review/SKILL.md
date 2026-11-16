---
name: rgaa-review
description: Use when reviewing the front-end files of a change set for RGAA 4.1.2 accessibility — templates, layout files adding markup, JS, CSS and utility class sources.
---

# RGAA review

## Scope

The front-end files you are given — in modified files, only the markup the change introduced or
whose behavior it changed. Given no list: the uncommitted changes (`git diff HEAD`) and the
untracked files read in full; on a clean tree, the branch's commits since it left its base.
Read-only: edit nothing.

## Load the rules

1. Invoke `accessibility:rgaa-accessibility`.
2. Also invoke `accessibility:aria-components` when the change set holds an interactive widget:
   modal, drawer, off-canvas menu, accordion, tabs, dropdown, carousel, combobox, switch.
3. Take from the project's rules how its front end is built — templating, JS framework,
   translation function, focus helpers — before reading the files.

## Review

Check the markup against the rules, then against these traps, which the rules do not spell out:

- **Click handlers on non-interactive elements**: a click handler on a `div`, `span`, `li` or
  `img` instead of a `<button>` or a link.
- **Content hidden by styling only**: a closed panel or menu hidden with zero opacity or a
  translate stays in the tab order and in the accessibility tree. Content hidden from everyone is
  `display: none`, `hidden` or `inert`; `.sr-only` text is meant to be read and is not this case.
- **State not exposed**: a toggle whose trigger has no `aria-expanded` bound to the same state as
  the content it shows.
- **Focus in overlays**: a modal, drawer or off-canvas menu moves focus in on open, keeps it in,
  and returns it to the trigger on close — with the project's focus helper when it has one.
- **Focus outline removed**: an outline removed with no `:focus-visible` replacement.
- **Untranslated accessible text**: an `aria-label`, `alt`, `title` or visually hidden text
  hardcoded instead of going through the project's translation function, then escaped for its
  context.
- **Icon-only controls**: a button or link whose only content is an icon has an accessible name;
  a decorative icon next to text is hidden with `aria-hidden="true"`.
- **Silent updates**: a counter, message or result list updated by JS without a live region.

## What a diff cannot prove

Actual contrast ratios (unless both colors are literal values in the diff), 200% zoom, reading
order produced by CSS, and real screen reader output. Never report these as compliant or as
defects: list the ones the change makes worth checking under `manual checks`, one line each,
with the page or component to look at.

## Findings

Each finding: `file:line`, severity (critical / important / nit), the RGAA criterion (`11.1`)
when you are sure of it or the theme (`Forms`) otherwise, one-sentence defect, and what a
keyboard or screen reader user hits. When the caller gives an output format, use it and keep the
criterion and the `manual checks`. Nothing found is a normal answer.
