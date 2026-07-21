---
name: rgaa-accessibility
description: Use when implementing or reviewing any HTML/frontend feature, UI component, form, page structure, or content — to ensure compliance with RGAA 4.1.2 accessibility criteria. Trigger on phrases like "accessibility", "RGAA", "a11y", "accessible", "screen reader", or when creating any visible UI element.
---

# RGAA 4.1.2 — Accessible development

Reference source: https://accessibilite.numerique.gouv.fr/methode/criteres-et-tests/

## Fundamental principle

Prefer native semantic HTML over ARIA roles. ARIA never compensates for poorly structured HTML — it only enriches it when HTML alone is insufficient.
Prefer CSS over Javascript.

## Checklist by topic

### 1. Images

| Criterion | Rule |
|---------|-------|
| Informative image | Non-empty, relevant `alt` |
| Decorative image | `alt=""` (empty) + `role="presentation"` if SVG |
| Complex image (chart, map) | Short `alt` + detailed description via `aria-describedby` or adjacent link |
| Text image | Replace with CSS-styled text |
| Caption | `<figure>` + `<figcaption>` linked together |

```html
<!-- Image porteuse -->
<img src="chart.png" alt="Ventes 2024 : +12% vs 2023">

<!-- Image décorative -->
<img src="deco.png" alt="">

<!-- Image complexe -->
<img src="graph.png" alt="Évolution des ventes" aria-describedby="graph-desc">
<p id="graph-desc">En janvier 10k, février 12k…</p>
```

### 2. Colors

- **3.1** — Information must never be conveyed by color alone (add text, an icon, or a pattern)
- **3.2** — Minimum contrast ratios:
    - Normal text: **4.5:1**
    - Large text (18px+, or 14px+ bold): **3:1**
    - UI components (field borders, active icons): **3:1**
    - Decorative text or logo: exempt

### 3. Links

```html
<!-- Libellé explicite -->
<a href="/rapport.pdf">Télécharger le rapport annuel 2024 (PDF, 2 Mo)</a>

<!-- Libellé via aria-label si contexte insuffisant -->
<a href="/article/1" aria-label="Lire l'article : Titre de l'article">Lire</a>

<!-- Interdit : libellé vide ou non descriptif -->
<a href="/article/1">Cliquez ici</a>
```

### 4. Scripts / Interactive components

- Every custom component must be operable with the keyboard
- Dynamic DOM updates must be announced via `aria-live` or an appropriate role
- `aria-live="polite"` for non-urgent updates, `aria-live="assertive"` only for critical alerts
- For specific accessible components invoke the **`aria-components`** skill.
```html
<div role="status" aria-live="polite" aria-atomic="true">
  <!-- Injecter ici le message de confirmation -->
</div>
```

### 5. Mandatory elements

- Accessible-name and description attributes — `alt`, `title`, `placeholder`, `aria-label`, `aria-placeholder`, `aria-roledescription` — must be written in the page's **current locale**, exactly like visible text. A value left in another language (e.g. a hardcoded `aria-roledescription="carousel"` on a French page) breaks the experience for assistive-technology users just as much as untranslated visible copy would.

```html
<html lang="fr">

<!-- Changement de langue dans le contenu -->
<p>Le terme <span lang="en">accessibility</span> est central.</p>

<!-- Changement de sens de lecture -->
<p>Ce mot signifie « bonjour » en arabe : <span dir="rtl" lang="ar">مرحبا</span>.</p>
```

### 6. Structure

- Logical heading hierarchy: a single `<h1>`, no skipped levels (`h1` → `h2` → `h3`)
- Use `<ul>`, `<ol>`, `<dl>` for lists — never dashes inside a `<p>`
- Quotations: `<blockquote>` + `<cite>`

### 7. Presentation

- **200% zoom** — no loss of content or functionality
- **DOM order** — follows the logical reading order
- **Visible focus indication** — for every element that receives focus
- **Animations** — respect `prefers-reduced-motion`
- **Colors** — for every font color (color) declaration on an element likely to contain text, declare a background color (background, background-color), or at least one inherited from a parent, and vice versa

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

### 8. Forms

| Criterion | Rule |
|---------|-------|
| Label | Each field has a `<label>` associated via `for`/`id` or `aria-label` |
| Relevance | The label precisely describes what is expected |
| Required | `required` + `aria-required="true"` + visual indication |
| Errors | `aria-describedby` pointing to the error message |
| Groups | `<fieldset>` + `<legend>` for radio/checkbox groups |

```html
<label for="email">Adresse e-mail (exemple: nom@domaine.fr) <span aria-hidden="true">*</span></label>
<input
  type="email"
  id="email"
  required
  aria-required="true"
  aria-describedby="email-error"
  aria-invalid="true"
>
<p id="email-error" role="alert">Format attendu : nom@domaine.fr</p>

<fieldset>
  <legend>Civilité</legend>
  <label><input type="radio" name="civility" value="m"> M.</label>
  <label><input type="radio" name="civility" value="mme"> Mme</label>
</fieldset>
```

### 9. Navigation

- Logical **tab order** (follows the visual order)
- **Keyboard focus** always visible — never remove `outline` without an alternative
- **Skip links** at the start of the page pointing to the content and the footer, and before each slider to skip the slider

```html
<a href="#main-content" class="skip-link">Aller au contenu principal</a>

<a href="#footer" class="skip-link">Aller au pied de page</a>

<a href="#slider-end" class="skip-link">Passer le slider</a>

```

```css
:focus-visible {
  outline: 2px solid #005fcc;
  outline-offset: 2px;
}
```

### 10. Tables

```html
<table>
  <caption>Récapitulatif des commandes du mois</caption>
  <thead>
    <tr>
      <th scope="col">Produit</th>
      <th scope="col">Quantité</th>
    </tr>
  </thead>
  <tbody>…</tbody>
</table>
```

## Most common mistakes

| Mistake | Fix |
|--------|-----------|
| `<div onclick>` without a role or tabindex | Use `<button>` |
| `alt` identical to the file name | Describe the information conveyed |
| Placeholder as the only label | Add a real `<label>` |
| `display:none` to hide "accessible" content | Use `.sr-only` |
| Focus lost after closing a modal | Restore focus to the triggering element |
| Color alone to indicate an error | Add an icon + text |

## Utility class `.sr-only`

```css
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
```

## Complex interactive components

For specific patterns (modal, accordion, tabs, menu…): invoke the **`aria-components`** skill.