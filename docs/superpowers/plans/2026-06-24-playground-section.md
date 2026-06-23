# Playground Section Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a "Playground" section that showcases personal GitHub Pages projects as clickable cards with a screenshot preview area and gradient fade.

**Architecture:** One new Astro component (`Playground.astro`) follows the exact same pattern as all other section components — typed data block in frontmatter, single `<section>` output. Two edits to `index.astro` wire it into the nav and the page layout.

**Tech Stack:** Astro 5, Tailwind CSS v4 (CSS-first, `@theme` directives in `src/styles/global.css`), static output (zero JS shipped).

## Global Constraints

- No `tailwind.config.js` — all customisation goes in `src/styles/global.css` using `@theme` directives
- Brand colour `#D66853` is referenced as `brand` token; teal = `#C4956A`; surface = `#2d3142`; mid = `#4f5d75`; bg = `#ffffff`; bg-alt = `#f0eae0`
- Dark mode is toggled via `data-theme="dark"` on `<html>` — custom dark overrides live in `global.css` and component `<style>` blocks
- Card dark mode: `global.css` overrides `.bg-white` to `#262b42` in dark mode — the gradient fade overlay must use `#262b42` as its end colour in dark mode
- No JS in components; no new dependencies

---

### Task 1: Create `src/components/Playground.astro`

**Files:**
- Create: `src/components/Playground.astro`

**Interfaces:**
- Consumes: nothing from other tasks
- Produces: `<section id="playground">` — consumed by Task 2 via `import Playground`

- [ ] **Step 1: Create the file with the exact content below**

`src/components/Playground.astro`:

```astro
---
interface PlaygroundProject {
  name:        string;
  description: string;
  url:         string;
  screenshot?: string;
}

const projects: PlaygroundProject[] = [
  {
    name:        "Guitar Assist",
    description: "An interactive guitar chord and scale reference tool. Look up chords, find scales, and explore fretboard patterns — built for quick reference while practising.",
    url:         "https://petekul.github.io/guitar-assist",
  },
  {
    name:        "Project Two",
    description: "Placeholder — replace with a real project description.",
    url:         "https://petekul.github.io/project-two",
  },
  {
    name:        "Project Three",
    description: "Placeholder — replace with a real project description.",
    url:         "https://petekul.github.io/project-three",
  },
];
---

<section id="playground" class="py-24 bg-bg">
  <div class="px-8 lg:px-14">
    <h2 class="text-3xl font-bold text-surface mb-12">Playground</h2>

    <div class="grid sm:grid-cols-2 gap-6">
      {projects.map(({ name, description, url, screenshot }) => (
        <a
          href={url}
          target="_blank"
          rel="noopener noreferrer"
          class="group rounded-xl bg-white border border-teal/20 hover:border-brand hover:shadow-[0_4px_16px_rgba(45,49,66,0.12)] transition-all flex flex-col overflow-hidden"
        >
          <div class="relative h-44 overflow-hidden">
            {screenshot ? (
              <img
                src={screenshot}
                alt={name}
                class="w-full h-full object-cover object-top"
              />
            ) : (
              <div class="screenshot-placeholder w-full h-full" />
            )}
            <div class="gradient-fade absolute inset-x-0 bottom-0 h-16 pointer-events-none" />
          </div>

          <div class="p-5 flex flex-col flex-1">
            <h3 class="text-base font-bold text-surface leading-snug">{name}</h3>
            <p class="text-sm text-mid leading-relaxed mt-1 flex-1">{description}</p>
            <p class="url-text text-xs mt-3 truncate">{url.replace('https://', '')}</p>
          </div>
        </a>
      ))}
    </div>
  </div>
</section>

<style>
  .screenshot-placeholder {
    background: linear-gradient(135deg, var(--color-brand) 0%, var(--color-teal) 100%);
  }

  .gradient-fade {
    background: linear-gradient(to bottom, transparent, white);
  }

  [data-theme="dark"] .gradient-fade {
    background: linear-gradient(to bottom, transparent, #262b42);
  }

  .url-text {
    color: color-mix(in srgb, var(--color-mid) 60%, transparent);
  }
</style>
```

- [ ] **Step 2: Verify the build succeeds**

Run: `npm run build`

Expected: build completes with no errors. The Playground component is not yet in the page — that's fine, it will be wired up in Task 2.

- [ ] **Step 3: Commit**

```bash
git add src/components/Playground.astro
git commit -m "feat: add Playground section component"
```

---

### Task 2: Wire Playground into `src/pages/index.astro`

**Files:**
- Modify: `src/pages/index.astro`

**Interfaces:**
- Consumes: `src/components/Playground.astro` (Task 1) — default export is the `<Playground />` component
- Produces: live section visible at `#playground` on the page

Three changes are needed in `index.astro`. Make them all before running the build.

- [ ] **Step 1: Add the import**

At line 6, after the existing `import Skills` line, add:

```diff
 import Skills     from '../components/Skills.astro';
+import Playground from '../components/Playground.astro';
 import Contact    from '../components/Contact.astro';
```

- [ ] **Step 2: Add the nav entry**

In the `nav` array (around line 15), insert `playground` between `skills` and `contact`:

```diff
   { id: 'skills',     label: 'Skills'     },
+  { id: 'playground', label: 'Playground' },
   { id: 'contact',    label: 'Contact'    },
```

- [ ] **Step 3: Place the component in the page**

In the `<main>` content area (around line 145), insert `<Playground />` between `<Skills />` and `<Contact />`:

```diff
       <Skills />
+      <Playground />
       <Contact />
```

- [ ] **Step 4: Verify the build succeeds**

Run: `npm run build`

Expected: build completes with no errors, no TypeScript warnings.

- [ ] **Step 5: Verify visually in the dev server**

Run: `npm run dev`

Open `http://localhost:4321` and check:
- "Playground" appears in the left-side nav between "Skills" and "Contact"
- Clicking the nav link scrolls to the Playground section
- Three cards render in a 2-column grid (3rd card wraps to its own row on the left)
- Each card shows the brand→teal diagonal gradient placeholder in the screenshot area
- The gradient fades smoothly into the white card body
- Card URL text (`petekul.github.io/guitar-assist` etc.) shows in muted small type at the bottom
- Hovering a card shows the `brand` border and lift shadow
- Toggle dark mode (moon icon) — gradient fade end colour changes from white to the dark card colour (`#262b42`) with no visible seam
- Clicking a card opens the GitHub Pages URL in a new tab

- [ ] **Step 6: Commit**

```bash
git add src/pages/index.astro
git commit -m "feat: wire Playground section into nav and page layout"
```
