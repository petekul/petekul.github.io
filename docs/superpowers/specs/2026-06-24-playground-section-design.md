# Playground Section Design

**Date:** 2026-06-24
**Status:** Approved

## Overview

Add a "Playground" section to the portfolio that showcases personal GitHub Pages projects. It sits as the penultimate section (between Skills and Contact) and has its own nav entry.

## Nav

Add `{ id: 'playground', label: 'Playground' }` to the `nav` array in `src/pages/index.astro`, between the existing `skills` and `contact` entries.

## New Component

**File:** `src/components/Playground.astro`

Follows the same pattern as all other section components: typed data block in the Astro frontmatter, a single `<section>` element as output.

### Data Shape

```typescript
interface PlaygroundProject {
  name:         string;   // display title
  description:  string;   // 1–2 sentence summary
  url:          string;   // full GitHub Pages URL
  screenshot?:  string;   // path relative to /public, e.g. /screenshots/guitar-assist.png
}
```

### Initial Data

- **Guitar Assist** — `https://petekul.github.io/guitar-assist`, no screenshot yet (placeholder)
- Two additional placeholder cards to fill the 2-column grid

### Card Anatomy

Each card is a single `<a href={url} target="_blank" rel="noopener noreferrer">` wrapper — the entire card is clickable.

Top to bottom inside the card:

1. **Screenshot area** (~180px fixed height, `overflow-hidden`, `rounded-t-xl`)
   - If `screenshot` is provided: `<img>` fills the area with `object-cover`
   - If no screenshot: diagonal gradient placeholder using `brand` and `teal` colours
   - In both cases a `<div>` is absolutely positioned at the bottom of the area with `bg-gradient-to-b from-transparent to-[card-bg]` to fade into the card body

2. **Content block** (padded, `p-5`)
   - Title: `font-bold text-surface`
   - Description: `text-sm text-mid leading-relaxed mt-1`
   - URL: `text-xs text-mid/60 mt-3 truncate` — displayed as decorative text (card itself is the link)

### Hover State

Same as existing project cards: `hover:border-brand hover:shadow-[0_4px_16px_rgba(45,49,66,0.12)] transition-all`

### Screenshot Placeholder

A CSS gradient using brand colours so empty slots look intentional:
`background: linear-gradient(135deg, var(--color-brand) 0%, var(--color-teal) 100%)`

### Dark Mode

The gradient fade overlay must match the card background. The card uses `bg-white`, which global.css overrides to `#262b42` in dark mode. Add a `<style>` block in the component to override the gradient `--to` colour in `[data-theme="dark"]`.

## Section Styling

```
<section id="playground" class="py-24 bg-bg">
```

`bg-bg` (white in light, `#1a1d2e` in dark) — alternates with Skills which uses `bg-bg-alt`.

Section heading: `"Playground"` styled as `text-3xl font-bold text-surface mb-12` — consistent with all other section headings.

Grid: `grid sm:grid-cols-2 gap-6` — matches the existing Projects section grid.

## index.astro Changes

1. Import `Playground` component
2. Add nav entry between `skills` and `contact`
3. Place `<Playground />` between `<Skills />` and `<Contact />`

## Out of Scope

- No filtering, search, or categorisation
- No lazy-loading or animation beyond existing hover transitions
- Screenshots are static assets managed manually in `/public/screenshots/`
