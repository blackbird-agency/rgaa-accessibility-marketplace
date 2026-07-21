---
name: aria-components
description: Use when creating or implementing interactive UI components such as modal, dialog, accordion, tabs, menu, dropdown, carousel, combobox, tooltip, radio group, switch/toggle, or any custom widget requiring keyboard navigation and ARIA roles. Trigger on phrases like "modal", "accordion", "tabs", "dropdown menu", "interactive component", "widget", "dropdown", "radio group", "switch", "toggle", "carousel".
---

# ARIA Components — WAI-ARIA APG Patterns

Reference source: https://www.w3.org/WAI/ARIA/apg/patterns/

## Universal rule

Every custom interactive component must satisfy three conditions:
1. **Role** — the user knows what it is (`role="dialog"`, `role="tab"`)
2. **State** — the user knows what state it is in (`aria-expanded`, `aria-selected`)
3. **Property** — the user knows how to use it (`aria-controls`, `aria-labelledby`)

Every `aria-label` / `aria-roledescription` value used in the examples below is a placeholder — translate it into the site's current locale like any other text (see the **`rgaa-accessibility`** skill).

---

## Modal / Dialog

Pattern: https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/

### Roles and properties

HTML component:

```html
<button aria-controls="modal">Open modal</button>
<dialog id="modal" aria-labelledby="dialog-title" aria-modal="true">
    <h2 id="dialog-title">Confirm delete</h2>
    <p id="dialog-desc">This is an irreversible action</p>
    <!-- content -->
    <button>Cancel</button>
    <button>Delete</button>
</dialog>
```


Alternative component:
```html
<div
  role="dialog"
  aria-modal="true"
  aria-labelledby="dialog-title"
  aria-describedby="dialog-desc"
>
  <h2 id="dialog-title">Confirm delete</h2>
  <p id="dialog-desc">This is an irreversible action</p>
  <!-- content -->
  <button>Cancel</button>
  <button>Delete</button>
</div>
```

For a critical alert (mandatory confirmation): `role="alertdialog"`.

### Keyboard

| Key | Behavior |
|--------|-------------|
| `Tab` | Next tabbable element (circular loop within the dialog) |
| `Shift+Tab` | Previous tabbable element (circular loop) |
| `Esc` | Closes the dialog |

### Required behaviors

- On open: focus placed on the first interactive element (or on the least destructive button)
- Focus stays **trapped** inside the dialog as long as it is open
- The outside content is **inert** (`inert` attribute on the rest of the DOM)
- On close: focus returns to the **triggering element**

```js
// Piège de focus minimal
dialog.addEventListener('keydown', e => {
  if (e.key !== 'Tab') return;
  const focusable = [...dialog.querySelectorAll('button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])')];
  const first = focusable[0];
  const last = focusable[focusable.length - 1];
  if (e.shiftKey && document.activeElement === first) {
    e.preventDefault(); last.focus();
  } else if (!e.shiftKey && document.activeElement === last) {
    e.preventDefault(); first.focus();
  }
});
```

---

## Accordion

Pattern: https://www.w3.org/WAI/ARIA/apg/patterns/accordion/

```html
<h3>
  <button
    aria-expanded="false"
    aria-controls="panel-1"
    id="header-1"
  >Section 1</button>
</h3>
<div
  id="panel-1"
  role="region"
  aria-labelledby="header-1"
  class="hidden"
>
  <!-- contenu du panneau -->
</div>
```

### Keyboard

| Key | Behavior |
|--------|-------------|
| `Enter` / `Space` | Opens/closes the panel |
| `Tab` / `Shift+Tab` | Navigation between focusable elements |
| `↑` / `↓` (optional) | Focus on the previous/next header |
| `Home` / `End` (optional) | First / last header |

### ARIA states

- `aria-expanded="true"` when the panel is visible
- `aria-disabled="true"` if the panel cannot be collapsed
- `hidden` on the panel to hide it (alternative: `display:none`)

---

## Tabs

Pattern: https://www.w3.org/WAI/ARIA/apg/patterns/tabs/

```html
<div role="tablist" aria-label="Product sections">
  <button role="tab" aria-selected="true"  aria-controls="panel-desc" id="tab-desc">Description</button>
  <button role="tab" aria-selected="false" aria-controls="panel-spec" id="tab-spec" tabindex="-1">Specs</button>
</div>

<div role="tabpanel" id="panel-desc" aria-labelledby="tab-desc">…</div>
<div role="tabpanel" id="panel-spec" aria-labelledby="tab-spec" class="hidden">…</div>
```

### Keyboard

| Key | Behavior |
|--------|-------------|
| `Tab` | Enters the tablist on the active tab, then exits |
| `←` / `→` | Previous/next tab (circular loop) |
| `↑` / `↓` | Same when orientation is vertical |
| `Enter` / `Space` | Activates the tab (if manual activation) |
| `Home` / `End` | First / last tab |

### Key points

- Only one tab has `tabindex="0"` at a time, the others have `tabindex="-1"` (roving tabindex)
- `aria-selected="true"` on the active tab, `false` on the others
- Automatic activation on focus is recommended (avoids the double keystroke)

---

## Menu / Menubar

Pattern: https://www.w3.org/WAI/ARIA/apg/patterns/menubar/

```html
<nav role="navigation" aria-label="Main navigation">
  <ul role="menubar">
    <li role="none">
      <a role="menuitem" href="/produits">Products</a>
    </li>
    <li role="none">
      <button
        role="menuitem"
        aria-haspopup="true"
        aria-expanded="false"
        aria-controls="submenu-services"
      >Services</button>
      <ul role="menu" id="submenu-services" aria-label="Services">
        <li role="none"><a role="menuitem" href="/conseil">Advice</a></li>
      </ul>
    </li>
  </ul>
</nav>
```

### Keyboard

| Key | Behavior |
|--------|-------------|
| `←` / `→` | Focus between menubar items |
| `↑` / `↓` | Focus between items of a submenu |
| `Enter` / `Space` | Activates the item or opens the submenu |
| `Esc` | Closes the submenu, returns to the trigger |
| `Tab` / `Shift+Tab` | Exits the menu (does NOT navigate between items) |

---

## Button

Pattern: https://www.w3.org/WAI/ARIA/apg/patterns/button/

```html
<!-- Simple button -->
<button>Send</button>

<!-- Toggle button -->
<button aria-pressed="false">Dark mode</button>

<!-- Open menu button -->
<button aria-haspopup="menu" aria-expanded="false" aria-controls="menu-id">
  Options
</button>

<!-- Icon only button -->
<button aria-label="Close the window">
  <svg aria-hidden="true">…</svg>
</button>
```

### Key points

- Always `<button>` or `<input type="button">` — never `<div>` or `<span>`
- `Space` and `Enter` activate the button (native with `<button>`)
- Toggle: the label **does not change**; only `aria-pressed` changes (`true`/`false`)
- `aria-disabled="true"` (preferable to `disabled`) to keep focus and the announcement

---

## Disclosure

Pattern: https://www.w3.org/WAI/ARIA/apg/patterns/disclosure/

Simplified variant of the accordion for a single panel.

HTML component:
```html
<details>
  <summary>
      Ut velit mauris, egestas sed, gravida nec, ornare ut, mi.
  </summary>
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed non risus. Suspendisse lectus tortor, dignissim sit amet, adipiscing nec, ultricies sed, dolor
</details>
```

Alternative component:
```html
<button aria-expanded="false" aria-controls="details-panel">
  See details
</button>
<div id="details-panel" hidden>
  <!-- content -->
</div>
```

---

## Combobox (field with suggestions)

Pattern: https://www.w3.org/WAI/ARIA/apg/patterns/combobox/

```html
<label id="combo-label" for="country">Country</label>
<input
  type="text"
  id="country"
  role="combobox"
  aria-expanded="false"
  aria-controls="country-listbox"
  aria-activedescendant=""
>
<ul role="listbox" aria-labelledby="combo-label" id="country-listbox">
  <li role="option" id="opt-fr" aria-selected="true">France</li>
  <li role="option" id="opt-be">Belgium</li>
</ul>
```

### Keyboard

| Key | Behavior |
|--------|-------------|
| `↓` | Opens the list / next option |
| `↑` | Previous option |
| `Enter` | Selects the option |
| `Esc` | Closes the list without selecting |

---

## Radio Group

Pattern: https://www.w3.org/WAI/ARIA/apg/patterns/radio/

```html
<div role="radiogroup" aria-labelledby="delivery-label">
  <span id="delivery-label">Delivery methods</span>
  <button role="radio" aria-checked="true" tabindex="0">Pickup</button>
  <button role="radio" aria-checked="false" tabindex="-1">Home delivery</button>
  <button role="radio" aria-checked="false" tabindex="-1">Relay point</button>
</div>
```

With native `<input type="radio">` elements, the browser already provides this keyboard behavior — `role="radiogroup"`/`role="radio"` are only needed when building a custom, non-native widget.

### Keyboard

| Key | Behavior |
|--------|-------------|
| `Tab` | Enters/exits the group; focus lands on the checked button (or the first one if none is checked) |
| `Space` | Checks the focused button |
| `↓` / `→` | Moves to and checks the next button (circular loop) |
| `↑` / `←` | Moves to and checks the previous button (circular loop) |

### Key points

- Only one radio button in the group has `tabindex="0"` at a time (roving tabindex); the others have `tabindex="-1"`
- Moving focus with an arrow key immediately checks that button — there is no separate activation step
- The group itself needs an accessible name via `aria-label` or `aria-labelledby`

---

## Switch

Pattern: https://www.w3.org/WAI/ARIA/apg/patterns/switch/

```html
<button role="switch" aria-checked="false">
  Notify me by email
</button>

<!-- Alternative with a native checkbox -->
<input type="checkbox" role="switch" id="notif">
<label for="notif">Notify me by email</label>
```

### Keyboard

| Key | Behavior |
|--------|-------------|
| `Space` | Toggles the switch state |
| `Enter` | Toggles the switch state (optional, recommended) |

### Key points

- `aria-checked="true"`/`"false"` reflects the on/off state (a native `<input type="checkbox">` uses its `checked` attribute instead)
- As with a toggle button, **the label never changes** with state — only `aria-checked` does
- A switch takes effect immediately (no "Submit" step); don't use it for a choice requiring explicit confirmation
- For a group of switches: wrap them in `role="group"` with `aria-labelledby`, or use `<fieldset>`/`<legend>`

---

## Carousel

Pattern: https://www.w3.org/WAI/ARIA/apg/patterns/carousel/

```html
<a href="#carousel-end" class="sr-only sr-only-focusable">Skip carousel</a>

<section aria-roledescription="carousel" role="region" aria-label="Actuality">
  <div aria-live="off" aria-atomic="false">
    <ul role="presentation">
        <li role="group" aria-roledescription="slide" aria-label="1 of 2">Slide A</li>
        <li role="group" aria-roledescription="slide" aria-label="2 of 2" aria-hidden="true">Slide B</li>
     </ul>
  </div>
  <button aria-label="Previous Slide">‹</button>
  <button aria-label="Next slide">›</button>
</section>

<div id="carousel-end"></div>
```

### Key points

- If the carousel scrolls automatically: pause button required (`aria-label="Mettre en pause"`)
- `aria-live="off"` during automatic scrolling, `"polite"` after a user action
- Respect `prefers-reduced-motion`: disable autoplay
- Provide a **"skip carousel" link/button** right before the carousel, visible on focus (`sr-only` + `sr-only-focusable`), targeting an anchor placed right after it — lets keyboard and screen reader users bypass the slides instead of tabbing through each one

### Variant with slide-picker tabs

When the carousel exposes a row of picker controls (one per slide, e.g. dots or numbered buttons) instead of — or in addition to — previous/next arrows, that row follows the **Tabs pattern** (see the Tabs section above), and each slide becomes the corresponding tabpanel rather than a plain `group`:

```html
<a href="#carousel-end" class="sr-only sr-only-focusable">Skip carousel</a>

<section aria-roledescription="carousel" role="region" aria-label="News">
  <div aria-live="off" aria-atomic="false">
    <div role="tabpanel" id="slide-1" aria-labelledby="tab-1">Slide A</div>
    <div role="tabpanel" id="slide-2" aria-labelledby="tab-2" class="hidden">Slide B</div>
  </div>

  <button aria-label="Previous slide">‹</button>
  <button aria-label="Next slide">›</button>

  <div role="tablist" aria-label="Choose slide to display">
    <button role="tab" id="tab-1" aria-selected="true"  aria-controls="slide-1" tabindex="0">Slide 1</button>
    <button role="tab" id="tab-2" aria-selected="false" aria-controls="slide-2" tabindex="-1">Slide 2</button>
  </div>
</section>

<div id="carousel-end"></div>
```

- Each slide has `role="tabpanel"` instead of `role="group"`, and **no `aria-roledescription="slide"`** — the tab already gives it an accessible name
- The picker row is a real `tablist`/`tab` pair: `aria-selected`, `aria-controls`, and roving `tabindex` (`0` on the active tab, `-1` on the others), exactly like the Tabs pattern
- Keyboard: the picker row follows **Tabs pattern keyboard interaction** (`←`/`→` to move between pickers, `Home`/`End` for first/last), on top of the carousel's own prev/next buttons
- Don't mix both slide roles: a given carousel is either plain (`group`, arrows only) or tabbed (`tabpanel` + tablist picker) — not both at once

---

## Tooltip

Pattern: https://www.w3.org/WAI/ARIA/apg/patterns/tooltip/

```html
<button aria-describedby="tooltip-1">
  <svg aria-hidden="true">…</svg>
  <span class="sr-only">Delete</span>
</button>
<div role="tooltip" id="tooltip-1">Delete this element</div>
```

### Key points

- Appears on focus and on hover
- `Esc` closes the tooltip
- Never contains interactive elements
- For interactive content, use `role="dialog"` instead

---

## Quick dashboard — which pattern to choose?

| Need | Pattern |
|--------|---------|
| Hide/reveal a single block | Disclosure |
| Hide/reveal several sections | Accordion |
| Switch views without reloading | Tabs |
| Main navigation with submenus | Menubar |
| Contextual action on an item | Menu Button |
| Blocking window requiring an action | Dialog |
| Input with a list of suggestions | Combobox |
| Rich content on hover/focus | Tooltip |
| Scrolling through items in sequence | Carousel |
| Choose exactly one option among several | Radio Group |
| Toggle a single setting on/off, immediate effect | Switch |

---

## Related RGAA criteria

For general rules (contrast, language, headings, etc.): invoke the **`rgaa-accessibility`** skill.