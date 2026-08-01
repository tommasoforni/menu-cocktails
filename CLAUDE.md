# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

An interactive cocktail menu ("La Carta de Tragos") in two files, with no build step, no dependencies, no tests, and no package manager:

- `drinks.js` — the data: `INGREDIENTS`, `CATEGORIES`, `DRINKS`. This is the only file to touch when changing the menu.
- `index.html` — page chrome, the whole stylesheet, and the ~120-line inline script that renders the menu from that data.

Open `index.html` in a browser to preview. It is served as-is from GitHub Pages (`tommasoforni/menu-cocktails`), which is why the file must stay named `index.html` at the repo root.

**`drinks.js` is loaded with `<script src>`, not `fetch`.** That is deliberate: `fetch()` on `file://` is blocked by CORS, and a plain script tag isn't, so the page still works when opened straight off disk. Do not "modernize" this into a JSON file, an ES module, or an `import` — all three reintroduce the file:// restriction and break local preview.

The only external resource is the Google Fonts stylesheet (Playfair Display / Libre Baskerville / DM Sans). Everything else — CSS, the cat SVG in the header, the render logic — is inline.

## Content language

All user-facing text is Spanish (Argentine, informal: "trago", "almíbar", "revolver"). Keep new drink names, descriptions, and method text in that register. `<html lang="es">`.

## Adding a drink

Append to `DRINKS` in `drinks.js`; nothing in `index.html` needs to change.

```js
{
  name: "Aperol Spritz",
  cat: "burbujas",                    // must match a CATEGORIES key
  tag: "★ favorito",                  // optional; hidden under 500px
  summary: ["aperol", "prosecco", "soda"],   // optional, see below
  recipe: [["3 oz", "prosecco"], ["2 oz", "aperol"], ["1 oz", "soda"]],
  method: "Built · Copa de vino grande",     // always Technique · Glassware
  steps: "Llenar la copa con hielo…",
}
```

Every ingredient is referenced by its `INGREDIENTS` key, never by loose text. The key is what ties the summary line, the recipe rows, and bar stock together — an ingredient the pantry doesn't know about can never be marked as out. Each entry carries a `short` label for the summary line and a `full` label for recipe rows and pantry chips (`lima` → "lima" / "Jugo de lima fresco").

`summary` is a *curated* subset in presentation order, not a projection of the recipe: Fernet Spritz lists "Fernet Branca · prosecco" while its recipe also contains soda, and several drinks reorder the two. Omit it and the summary is derived from `recipe` order instead. The first letter is capitalized at render time — don't pre-capitalize the labels.

Quantities are strings using `oz` and typographic fractions (`½`, `¾`, `1½`, `4–6 oz`).

Drink numbers are computed from `DRINKS` order, so inserting mid-list renumbers automatically — no hand-editing. The number stays attached to the drink even when out-of-stock entries sort below the available ones.

## Adding a category

An entry in `CATEGORIES` (`key`, `icon`, `title`, `desc`) **plus** a five-rule color block in the stylesheet: `.cat-KEY` applied to `.category-icon`, `.category-title`, `.drink-name`, `.drink-tag`, and `.recipe-qty`. That CSS block is the only per-category thing not driven by data. Fade-in delays are generated at render (`0.1 + i * 0.15`s), so categories are no longer capped at five.

## Availability

A drink is unavailable when *any* recipe ingredient is out. Out-of-stock drinks are struck through, sorted to the bottom of their category, and annotated with what's missing — they stay visible on purpose, so guests see the drink exists rather than silently wondering.

State is a set of ingredient keys in `localStorage` under `carta-sin`, editable via the "modo barra" toggle in the footer (instant, no redeploy — that's the point of not putting stock in `drinks.js`). It mirrors to a `?sin=key,key` query param so the link can be shared with stock already applied. The param is the source of truth on load when present, and is filtered against `INGREDIENTS` keys before anything reaches the DOM — keep that filter if you touch `loadOut()`.

## Design constraints

Dark palette anchored on `#1a0e0a` background / `#f5e6d0` text / `#cf6024` accent; per-category colors override the text tones. Recipe panels animate open to their measured `scrollHeight` (set inline on click), so long recipes no longer clip — don't reintroduce a fixed `max-height` on `.recipe-panel.open`. The stylesheet also targets print (`@page { size: A4 }`, `print-color-adjust: exact` so the dark background survives, bar UI hidden) and a 500px mobile breakpoint that drops the tags and the recipe indent — check both when changing layout.
