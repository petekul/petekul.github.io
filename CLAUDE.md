# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
npm run dev      # start dev server at http://localhost:4321
npm run build    # production build → dist/
npm run preview  # preview the built site locally
```

## Stack

- **Astro 5** with static output (zero JS shipped by default)
- **Tailwind CSS v4** via `@tailwindcss/vite` — CSS-first config, no `tailwind.config.js`. All customisation goes in `src/styles/global.css` using `@theme` directives.
- Deployed to **GitHub Pages** via `.github/workflows/deploy.yml` on every push to `main`.
- Live URL: `https://petekul.github.io`

## Architecture

Single page (`src/pages/index.astro`) that imports one component per section:

```
src/
  layouts/Layout.astro        — base HTML shell, imports global.css, OG meta tags
  components/
    Header.astro              — sticky nav (links to section IDs)
    Hero.astro                — About/bio — full-screen dark hero
    Experience.astro          — work timeline
    Skills.astro              — grouped skill pills
    Personal.astro            — interests/hobbies cards
    Contact.astro             — contact links + footer
  styles/global.css           — @import "tailwindcss"; base styles
```

All site content (job entries, skills, bio, links) lives as typed data at the top of each component file — no CMS or external data layer.

## Updating content

Each component has a clearly marked data block at the top (inside the Astro frontmatter `---`). To update:
- **Bio / role**: `src/components/Hero.astro` — `name`, `role`, `bio` variables
- **Work history**: `src/components/Experience.astro` — `jobs` array
- **Skills**: `src/components/Skills.astro` — `skillGroups` array
- **Personal interests**: `src/components/Personal.astro` — `interests` array
- **Contact links**: `src/components/Contact.astro` — `links` array (add LinkedIn handle)

## GitHub Pages setup (one-time)

1. Create repo `petekul/petekul.github.io` on github.com
2. `git remote add origin https://github.com/petekul/petekul.github.io.git`
3. `git push -u origin main`
4. On GitHub → repo Settings → Pages → Source: **GitHub Actions**

The workflow deploys automatically on every push to `main`.

## Style Guide

Warm Editorial theme. Keep `#D66853` as the primary brand colour in all future work.

| Role        | Token     | Hex       | Usage                              |
|-------------|-----------|-----------|------------------------------------|
| Primary     | `brand`   | `#D66853` | CTAs, links, timeline dots         |
| Warm        | `surface` | `#B85C38` | Hero & Contact section backgrounds |
| Accent      | `teal`    | `#C4956A` | Secondary text, borders, icons     |
| Muted       | `mid`     | `#8C7B6E` | Body text, labels, nav links       |
| Background  | —         | `#FAF7F2` | Page & light section backgrounds   |
| Alt section | —         | `#F0EAE0` | Skills section background          |