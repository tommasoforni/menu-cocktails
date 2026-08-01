# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single self-contained HTML file (`index.html`) that renders an interactive cocktail menu ("La Carta de Tragos"). No build step, no dependencies, no tests, no package manager. Open the file in a browser to preview; it is served as-is from GitHub Pages (`tommasoforni/menu-cocktails`), which is why the file must stay named `index.html` at the repo root.

The only external resource is the Google Fonts stylesheet (Playfair Display / Libre Baskerville / DM Sans). Everything else — CSS, the cat SVG in the header, the toggle behavior — is inline.

## Content language

All user-facing text is Spanish (Argentine, informal: "trago", "almíbar", "revolver"). Keep new drink names, descriptions, and method text in that register. `<html lang="es">`.

## Structure of a menu entry

The page is pure markup with no JavaScript file — expanding a recipe is done by an inline `onclick="this.classList.toggle('open')"` on each `.drink`, with `.drink.open .recipe-panel { max-height: 400px }` doing the reveal. A recipe taller than 400px will be clipped; raise that value if a drink needs more lines.

Categories are `<div class="category cat-NAME">` and their color scheme comes entirely from a five-rule block per category in the stylesheet (`.cat-NAME .category-icon`, `.category-title`, `.drink-name`, `.drink-tag`, `.recipe-qty`). Adding a category means adding that block plus a `.category:nth-child(N)` animation-delay rule — the fade-in stagger is hardcoded per index and currently covers 5 categories.

Each drink follows a fixed shape:

```html
<div class="drink" onclick="this.classList.toggle('open')">
  <div class="drink-row">
    <span class="drink-number">NN</span>          <!-- sequential across the whole menu, not per category -->
    <div class="drink-info">
      <div class="drink-name"><span class="arrow">▶</span> Name</div>
      <div class="drink-ingredients">A · B · C</div>  <!-- middot-separated, lowercase -->
    </div>
    <span class="drink-tag">★ clásico</span>      <!-- optional; hidden under 500px -->
  </div>
  <div class="recipe-panel"><div class="recipe-content">
    <div class="recipe-line"><span class="recipe-qty">2 oz</span><span class="recipe-item">…</span></div>
    <div class="recipe-method">
      <span class="method-label">Shaken · Copa cocktail</span>   <!-- technique · glassware -->
      Prose instructions.
    </div>
  </div></div>
</div>
```

`.drink-number` values are sequential across the entire menu (01–12), so inserting a drink mid-menu requires renumbering everything after it. Quantities use `oz` with typographic fractions (`½`, `¾`, `1½`), and `.method-label` is always `Technique · Glassware` (Shaken / Built / Layered).

## Design constraints

Dark palette anchored on `#1a0e0a` background / `#f5e6d0` text / `#cf6024` accent; per-category colors override the text tones. The stylesheet also targets print (`@page { size: A4 }`, `print-color-adjust: exact` so the dark background survives) and a 500px mobile breakpoint that drops the tags and the recipe indent — check both when changing layout.
