# Many Brains Corporate Website Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build and deploy the Many Brains, Inc. corporate single-page website to Vercel at manybrains.us, mirroring the Heather AI site's dark aesthetic, to satisfy Apple Developer Program org enrollment.

**Architecture:** Static Astro 6 site styled with Tailwind 4, copied/adapted from the existing `~/projects/heather-ai-website` codebase. One landing page composed of focused `.astro` components, plus `/privacy` and `/terms`. Builds to static HTML; Vercel auto-detects Astro and deploys on push to `main`.

**Tech Stack:** Astro 6, Tailwind CSS 4 (`@tailwindcss/vite`), `@astrojs/sitemap`, Node ≥ 22.12.0, Vercel hosting.

## Global Constraints

- Node engine: `>=22.12.0`.
- Dark theme tokens (verbatim, in `src/styles/global.css` `@theme`): `--color-bg-primary: #0a0a0a;` `--color-bg-surface: #111111;` `--color-accent: #E8734A;` `--color-accent-hover: #D4623C;`
- Font: `--font-sans: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;`
- Legal entity name, exact: **Many Brains, Inc.** — described as **a Delaware C-Corporation**.
- Production site URL: `https://manybrains.us`.
- Contact email: `hello@manybrains.us`.
- Five portfolio companies with exact outbound URLs and one-liners (see Task 6).
- External links use `target="_blank"` and `rel="noopener noreferrer"`.
- No backend, CMS, forms, blog, or analytics.
- Reference source for visual language and copyable components: `~/projects/heather-ai-website` (read-only; do not modify it).

## Verification Model

This is a static brochure site, not a logic-heavy app — there is no unit-test runner. The per-task gate is:
1. `npm run build` exits 0 (Astro type-checks + builds; this is the primary correctness gate).
2. Targeted `grep` assertions against build output in `dist/` confirm required content/links exist.
3. Final manual visual check via `npm run preview`.
Each task ends with a commit.

## File Structure

```
manybrains-corp-website/
  astro.config.mjs          # site=manybrains.us, sitemap, tailwind vite plugin
  package.json              # scripts + deps (Node >=22.12)
  tsconfig.json
  vercel.json               # (optional) build/static hints
  public/
    favicon.svg
    og-image.png
    logos/                  # one asset per portfolio company
  src/
    styles/global.css       # tailwind import + @theme tokens
    layouts/BaseLayout.astro
    components/
      Nav.astro
      Hero.astro
      BrainNetwork.astro     # adapted from heather-ai PatternNetwork.astro
      Approach.astro
      Portfolio.astro
      Contact.astro
      Footer.astro
    pages/
      index.astro
      privacy.astro
      terms.astro
  README.md
```

---

### Task 1: Scaffold the Astro + Tailwind project

**Files:**
- Create: `package.json`, `tsconfig.json`, `astro.config.mjs`, `src/styles/global.css`, `src/layouts/BaseLayout.astro`, `src/pages/index.astro` (temporary placeholder)

**Interfaces:**
- Produces: `BaseLayout.astro` accepting props `{ title: string; description: string; path?: string }` and a default `<slot />`. All pages import it.

- [ ] **Step 1: Create `package.json`**

```json
{
  "name": "manybrains-corp-website",
  "type": "module",
  "version": "0.1.0",
  "engines": { "node": ">=22.12.0" },
  "scripts": {
    "dev": "astro dev",
    "build": "astro build",
    "preview": "astro preview",
    "astro": "astro"
  },
  "dependencies": {
    "@tailwindcss/vite": "^4.2.2",
    "astro": "^6.1.5",
    "tailwindcss": "^4.2.2"
  },
  "devDependencies": {
    "@astrojs/sitemap": "^3.7.2"
  }
}
```

- [ ] **Step 2: Create `tsconfig.json`**

```json
{
  "extends": "astro/tsconfigs/strict",
  "include": [".astro/types.d.ts", "**/*"],
  "exclude": ["dist"]
}
```

- [ ] **Step 3: Create `astro.config.mjs`**

```js
// @ts-check
import { defineConfig } from 'astro/config';
import sitemap from '@astrojs/sitemap';
import tailwindcss from '@tailwindcss/vite';

// https://astro.build/config
export default defineConfig({
  site: 'https://manybrains.us',
  integrations: [sitemap()],
  vite: {
    plugins: [tailwindcss()]
  }
});
```

- [ ] **Step 4: Create `src/styles/global.css`**

```css
@import "tailwindcss";

@theme {
  --color-bg-primary: #0a0a0a;
  --color-bg-surface: #111111;
  --color-accent: #E8734A;
  --color-accent-hover: #D4623C;

  --font-sans: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
}

@layer base {
  body {
    background-color: var(--color-bg-primary);
    color: #ffffff;
    font-family: var(--font-sans);
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }
}
```

- [ ] **Step 5: Create `src/layouts/BaseLayout.astro`**

```astro
---
import '../styles/global.css';

interface Props {
  title: string;
  description: string;
  path?: string;
}

const { title, description, path = '' } = Astro.props;
const canonical = new URL(path, Astro.site).toString();
const ogImage = new URL('/og-image.png', Astro.site).toString();
---

<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <link rel="icon" type="image/svg+xml" href="/favicon.svg" />
    <link rel="canonical" href={canonical} />
    <title>{title}</title>
    <meta name="description" content={description} />
    <meta property="og:type" content="website" />
    <meta property="og:title" content={title} />
    <meta property="og:description" content={description} />
    <meta property="og:url" content={canonical} />
    <meta property="og:image" content={ogImage} />
    <meta name="twitter:card" content="summary_large_image" />
    <meta name="generator" content={Astro.generator} />
  </head>
  <body>
    <slot />
  </body>
</html>
```

- [ ] **Step 6: Create a temporary `src/pages/index.astro` placeholder**

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
---
<BaseLayout title="Many Brains, Inc." description="A portfolio of businesses, built by many brains." path="/">
  <main class="min-h-screen flex items-center justify-center">
    <h1 class="text-3xl">Many Brains</h1>
  </main>
</BaseLayout>
```

- [ ] **Step 7: Install and build**

Run: `npm install && npm run build`
Expected: install succeeds; build exits 0; `dist/index.html` created.

- [ ] **Step 8: Verify output**

Run: `grep -q "Many Brains" dist/index.html && echo OK`
Expected: `OK`

- [ ] **Step 9: Commit**

```bash
git add -A
git commit -m "Scaffold Astro + Tailwind project with BaseLayout"
```

---

### Task 2: Acquire and prepare portfolio logos

**Files:**
- Create: `public/logos/upscape.png`, `public/logos/heather-ai.svg`, `public/logos/yonderey.png`, `public/logos/tutorville.svg`, `public/logos/lifecareplanners.svg`
- Create: `public/favicon.svg`

**Interfaces:**
- Produces: five logo files at the paths above, plus a site favicon. Task 6 references these exact paths. Every logo must read clearly on the `#0a0a0a` background (white/light or transparent-on-light treatment).

- [ ] **Step 1: Create `public/logos/` and download the known-good raster logos**

```bash
mkdir -p public/logos
# Upscape — clean white PNG, ideal for dark backgrounds
curl -sL "https://images.squarespace-cdn.com/content/v1/68f6e0df7ff05e533b512ab8/709bf474-8404-47b0-874c-9cebc2f3497a/White+logo+-+no+background.png?format=750w" -o public/logos/upscape.png
# Yonderey — site icon mark
curl -sL "https://assets.softr-files.com/applications/b6d3d4ff-80b1-43d5-a0a9-b8344790d356/assets/390416b4-5321-473d-9c1e-54251b48f722.png" -o public/logos/yonderey.png
```

Verify both downloaded as images:
Run: `file public/logos/upscape.png public/logos/yonderey.png`
Expected: both report `PNG image data`. If either is HTML/empty, fall back to a wordmark SVG (Step 3 pattern).

- [ ] **Step 2: Copy the Heather AI mark from the local workspace**

```bash
cp /home/ross/projects/heather-ai-website/public/favicon.svg public/logos/heather-ai.svg
```
Run: `file public/logos/heather-ai.svg`
Expected: `SVG` / XML text. (This is the Heather AI brand mark already used in our workspace.)

- [ ] **Step 3: Create clean wordmark SVGs for companies without a usable logo (Tutorville, The Life Care Planners)**

These two sites only expose tiny generic favicons, so per the spec we use brand-styled text wordmarks. White text on transparent, so they sit uniformly on the dark cards.

`public/logos/tutorville.svg`:
```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 320 64" role="img" aria-label="Tutorville">
  <text x="0" y="46" font-family="-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif" font-size="44" font-weight="700" fill="#ffffff">Tutorville</text>
</svg>
```

`public/logos/lifecareplanners.svg`:
```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 520 64" role="img" aria-label="The Life Care Planners">
  <text x="0" y="46" font-family="-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif" font-size="40" font-weight="700" fill="#ffffff">The Life Care Planners</text>
</svg>
```

- [ ] **Step 4: Create the site favicon `public/favicon.svg`**

A simple node-network glyph in the brand accent (the "many brains" motif):
```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 32 32">
  <rect width="32" height="32" rx="7" fill="#0a0a0a"/>
  <g stroke="#E8734A" stroke-width="1.5">
    <line x1="8" y1="9" x2="16" y2="16"/>
    <line x1="8" y1="23" x2="16" y2="16"/>
    <line x1="24" y1="9" x2="16" y2="16"/>
    <line x1="24" y1="23" x2="16" y2="16"/>
  </g>
  <g fill="#E8734A">
    <circle cx="8" cy="9" r="2.5"/><circle cx="8" cy="23" r="2.5"/>
    <circle cx="24" cy="9" r="2.5"/><circle cx="24" cy="23" r="2.5"/>
    <circle cx="16" cy="16" r="3.5" fill="#ffffff"/>
  </g>
</svg>
```

- [ ] **Step 5: Verify all five logos + favicon exist**

Run: `ls -1 public/favicon.svg public/logos/{upscape.png,heather-ai.svg,yonderey.png,tutorville.svg,lifecareplanners.svg}`
Expected: all six paths listed, none missing.

- [ ] **Step 6: Commit**

```bash
git add -A
git commit -m "Add portfolio logos and site favicon"
```

---

### Task 3: BrainNetwork signature visual

**Files:**
- Create: `src/components/BrainNetwork.astro`
- Reference (read-only): `~/projects/heather-ai-website/src/components/PatternNetwork.astro`

**Interfaces:**
- Produces: `BrainNetwork.astro` — a self-contained animated SVG node-graph, no props, safe to drop into the hero. Must render server-side to static SVG markup (CSS/SMIL animation only; no external JS dependency).

- [ ] **Step 1: Read the reference component**

Run: `sed -n '1,260p' /home/ross/projects/heather-ai-website/src/components/PatternNetwork.astro`
Note its structure: node datasets, position helpers, `<svg>` with `<line>` edges + `<circle>`/`<text>` nodes, and a `<style>` block driving the activation animation.

- [ ] **Step 2: Create a simplified `src/components/BrainNetwork.astro`**

Adapt to an abstract "brains" network (no Heather-specific labels). Keep it dependency-free and decorative (`aria-hidden`). Build the node/edge arrays in the frontmatter and render them; drive a subtle pulse with CSS `@keyframes` in a scoped `<style>`. Target ~9–12 nodes across two/three loose clusters with connecting edges, accent-colored, on transparent background.

```astro
---
// BrainNetwork.astro — decorative animated node graph (the "many brains" motif).
// Pure SVG + scoped CSS animation, no client JS.
interface Node { id: string; x: number; y: number; r: number }
const nodes: Node[] = [
  { id: 'n1', x: 80,  y: 90,  r: 6 },
  { id: 'n2', x: 200, y: 50,  r: 5 },
  { id: 'n3', x: 330, y: 110, r: 7 },
  { id: 'n4', x: 150, y: 180, r: 5 },
  { id: 'n5', x: 280, y: 210, r: 6 },
  { id: 'n6', x: 420, y: 70,  r: 5 },
  { id: 'n7', x: 470, y: 190, r: 6 },
  { id: 'n8', x: 60,  y: 230, r: 5 },
  { id: 'n9', x: 360, y: 280, r: 7 },
];
const edges: [string, string][] = [
  ['n1','n2'], ['n2','n3'], ['n1','n4'], ['n4','n5'], ['n3','n5'],
  ['n3','n6'], ['n6','n7'], ['n5','n7'], ['n4','n8'], ['n5','n9'], ['n7','n9'],
];
const pos = Object.fromEntries(nodes.map(n => [n.id, n]));
---
<svg class="brain-net" viewBox="0 0 520 340" fill="none" aria-hidden="true">
  <g class="edges" stroke="#E8734A" stroke-opacity="0.25" stroke-width="1">
    {edges.map(([a, b]) => (
      <line x1={pos[a].x} y1={pos[a].y} x2={pos[b].x} y2={pos[b].y} />
    ))}
  </g>
  <g class="nodes">
    {nodes.map((n, i) => (
      <circle cx={n.x} cy={n.y} r={n.r} fill="#E8734A" style={`animation-delay:${i * 0.35}s`} />
    ))}
  </g>
</svg>
<style>
  .brain-net { width: 100%; height: auto; max-width: 620px; }
  .nodes circle { opacity: 0.45; animation: pulse 3.2s ease-in-out infinite; }
  @keyframes pulse {
    0%, 100% { opacity: 0.35; }
    50%      { opacity: 1; }
  }
  @media (prefers-reduced-motion: reduce) {
    .nodes circle { animation: none; opacity: 0.6; }
  }
</style>
```

- [ ] **Step 3: Build to confirm it compiles** (it is wired into the hero in Task 4; for now just ensure no syntax error by temporarily importing nothing — skip build here and verify in Task 4).

- [ ] **Step 4: Commit**

```bash
git add src/components/BrainNetwork.astro
git commit -m "Add BrainNetwork animated visual"
```

---

### Task 4: Nav and Hero

**Files:**
- Create: `src/components/Nav.astro`, `src/components/Hero.astro`
- Modify: `src/pages/index.astro` (replace placeholder body with Nav + Hero)

**Interfaces:**
- Consumes: `BrainNetwork.astro` (Task 3).
- Produces: `Nav.astro` (fixed top nav, anchor links `#approach`, `#portfolio`, `#contact`), `Hero.astro` (full-viewport hero with headline, sub, CTA, and embedded `BrainNetwork`).

- [ ] **Step 1: Create `src/components/Nav.astro`**

```astro
---
---
<nav class="fixed top-0 inset-x-0 z-50 backdrop-blur bg-bg-primary/70 border-b border-white/5">
  <div class="max-w-6xl mx-auto px-6 h-16 flex items-center justify-between">
    <a href="/" class="font-bold tracking-tight text-lg">Many&nbsp;Brains</a>
    <div class="hidden sm:flex items-center gap-8 text-sm text-white/60">
      <a href="#approach" class="hover:text-white transition-colors">Approach</a>
      <a href="#portfolio" class="hover:text-white transition-colors">Portfolio</a>
      <a href="#contact" class="hover:text-white transition-colors">Contact</a>
    </div>
  </div>
</nav>
```

- [ ] **Step 2: Create `src/components/Hero.astro`**

```astro
---
import BrainNetwork from './BrainNetwork.astro';
---
<section class="min-h-screen flex flex-col items-center justify-center text-center px-6 pt-28 pb-20">
  <div class="inline-block px-4 py-1.5 rounded-full border border-accent/30 bg-accent/[0.08] text-[12px] text-accent tracking-[1px] uppercase mb-8">
    AI-accelerated holding company
  </div>
  <h1 class="text-5xl md:text-7xl font-bold leading-[1.1] max-w-[900px] tracking-tight">
    A portfolio of businesses,<br /><em class="not-italic text-accent">built by many brains.</em>
  </h1>
  <p class="text-lg md:text-xl text-white/50 max-w-[680px] leading-relaxed mt-6">
    We operate a growing portfolio of companies — accelerated by AI, run by a small team
    doing the work of many.
  </p>
  <a href="#portfolio" class="mt-10 inline-block px-10 py-4 bg-accent hover:bg-accent-hover text-white font-semibold rounded-xl text-base transition-all hover:-translate-y-0.5">
    See the portfolio
  </a>
  <div class="mt-16 w-full flex justify-center opacity-90">
    <BrainNetwork />
  </div>
</section>
```

- [ ] **Step 3: Update `src/pages/index.astro`**

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
import Nav from '../components/Nav.astro';
import Hero from '../components/Hero.astro';
---
<BaseLayout title="Many Brains, Inc. — A portfolio of businesses, built by many brains." description="Many Brains operates a growing portfolio of companies, accelerated by AI and run by a small team doing the work of many." path="/">
  <Nav />
  <main>
    <Hero />
  </main>
</BaseLayout>
```

- [ ] **Step 4: Build and verify**

Run: `npm run build && grep -q "built by many brains" dist/index.html && grep -q "brain-net" dist/index.html && echo OK`
Expected: build exits 0 and prints `OK`.

- [ ] **Step 5: Commit**

```bash
git add -A
git commit -m "Add Nav and Hero with BrainNetwork visual"
```

---

### Task 5: Approach section

**Files:**
- Create: `src/components/Approach.astro`
- Modify: `src/pages/index.astro` (add `<Approach />` after `<Hero />`)

**Interfaces:**
- Produces: `Approach.astro` — section with `id="approach"` and three cards.

- [ ] **Step 1: Create `src/components/Approach.astro`**

```astro
---
const cards = [
  {
    title: 'AI-assisted operating model',
    body: 'Every company we run is built with AI in the loop — research, engineering, design, and operations, accelerated end to end.',
  },
  {
    title: 'Small team, large output',
    body: 'A lean group of operators ships the output of a much larger organization, with the help of many AI brains.',
  },
  {
    title: 'A portfolio of real businesses',
    body: 'Not experiments — operating companies serving real customers across nutrition, education, outdoor, legal, and home services.',
  },
];
---
<section id="approach" class="py-28 px-6 bg-bg-surface">
  <div class="max-w-6xl mx-auto">
    <h2 class="text-3xl md:text-4xl font-bold tracking-tight text-center">Why Many Brains</h2>
    <p class="text-white/50 text-center max-w-[640px] mx-auto mt-4">
      The common thread across everything we build.
    </p>
    <div class="grid md:grid-cols-3 gap-6 mt-14">
      {cards.map((c) => (
        <div class="rounded-2xl border border-white/10 bg-white/[0.02] p-7">
          <h3 class="text-lg font-semibold text-accent">{c.title}</h3>
          <p class="text-white/60 leading-relaxed mt-3 text-[15px]">{c.body}</p>
        </div>
      ))}
    </div>
  </div>
</section>
```

- [ ] **Step 2: Add to `src/pages/index.astro`**

Import and place after `<Hero />`:
```astro
import Approach from '../components/Approach.astro';
```
```astro
    <Hero />
    <Approach />
```

- [ ] **Step 3: Build and verify**

Run: `npm run build && grep -q 'id="approach"' dist/index.html && grep -q "Why Many Brains" dist/index.html && echo OK`
Expected: exits 0, prints `OK`.

- [ ] **Step 4: Commit**

```bash
git add -A
git commit -m "Add Approach section"
```

---

### Task 6: Portfolio grid with logos

**Files:**
- Create: `src/components/Portfolio.astro`
- Modify: `src/pages/index.astro` (add `<Portfolio />` after `<Approach />`)

**Interfaces:**
- Consumes: logo files from Task 2 (`/logos/*.{png,svg}`).
- Produces: `Portfolio.astro` — section with `id="portfolio"` and five outbound cards, each showing a logo.

- [ ] **Step 1: Create `src/components/Portfolio.astro`**

```astro
---
interface Company {
  name: string;
  logo: string;
  description: string;
  url: string;
}
const companies: Company[] = [
  {
    name: 'Heather AI',
    logo: '/logos/heather-ai.svg',
    description: 'AI nutrition coaching that gives real-time guidance for meals, dining out, and GLP-1 transitions.',
    url: 'https://getheatherai.com',
  },
  {
    name: 'Tutorville',
    logo: '/logos/tutorville.svg',
    description: 'A tutoring marketplace connecting students with vetted tutors for measurable results.',
    url: 'https://tutorville.co',
  },
  {
    name: 'The Life Care Planners',
    logo: '/logos/lifecareplanners.svg',
    description: 'An expert-witness directory connecting attorneys with qualified life care planning experts.',
    url: 'https://thelifecareplanners.com',
  },
  {
    name: 'Yonderey',
    logo: '/logos/yonderey.png',
    description: 'A marketplace pairing adventurers with certified mountain guides to plan expeditions.',
    url: 'https://yonderey.com',
  },
  {
    name: 'Upscape',
    logo: '/logos/upscape.png',
    description: 'Custom luxury landscape lighting design and installation for homeowners.',
    url: 'https://getupscaped.com',
  },
];
---
<section id="portfolio" class="py-28 px-6">
  <div class="max-w-6xl mx-auto">
    <h2 class="text-3xl md:text-4xl font-bold tracking-tight text-center">Our portfolio</h2>
    <p class="text-white/50 text-center max-w-[640px] mx-auto mt-4">
      Operating companies we build and run.
    </p>
    <div class="grid sm:grid-cols-2 lg:grid-cols-3 gap-6 mt-14">
      {companies.map((c) => (
        <a
          href={c.url}
          target="_blank"
          rel="noopener noreferrer"
          class="group rounded-2xl border border-white/10 bg-white/[0.02] p-7 flex flex-col hover:border-accent/40 hover:-translate-y-0.5 transition-all"
        >
          <div class="h-12 flex items-center">
            <img src={c.logo} alt={`${c.name} logo`} class="max-h-10 max-w-[180px] w-auto object-contain" loading="lazy" />
          </div>
          <p class="text-white/60 leading-relaxed mt-5 text-[15px] flex-1">{c.description}</p>
          <span class="text-accent text-sm font-medium mt-5 inline-flex items-center gap-1">
            Visit site <span class="group-hover:translate-x-0.5 transition-transform">&rarr;</span>
          </span>
        </a>
      ))}
    </div>
  </div>
</section>
```

- [ ] **Step 2: Add to `src/pages/index.astro`**

```astro
import Portfolio from '../components/Portfolio.astro';
```
```astro
    <Approach />
    <Portfolio />
```

- [ ] **Step 3: Build and verify all five links + logos render**

Run:
```bash
npm run build && \
for u in getheatherai.com tutorville.co thelifecareplanners.com yonderey.com getupscaped.com; do grep -q "$u" dist/index.html || echo "MISSING $u"; done && \
for l in heather-ai.svg tutorville.svg lifecareplanners.svg yonderey.png upscape.png; do grep -q "logos/$l" dist/index.html || echo "MISSING logo $l"; done && \
echo DONE
```
Expected: build exits 0, no `MISSING` lines, prints `DONE`.

- [ ] **Step 4: Commit**

```bash
git add -A
git commit -m "Add Portfolio grid with company logos"
```

---

### Task 7: Contact and Footer

**Files:**
- Create: `src/components/Contact.astro`, `src/components/Footer.astro`
- Modify: `src/pages/index.astro` (add `<Contact />` then `<Footer />`)

**Interfaces:**
- Produces: `Contact.astro` (section `id="contact"`, mailto CTA) and `Footer.astro` (legal line, portfolio links, copyright). `Footer.astro` is reused on legal pages (Task 8).

- [ ] **Step 1: Create `src/components/Contact.astro`**

```astro
---
---
<section id="contact" class="py-28 px-6 bg-bg-surface text-center">
  <div class="max-w-2xl mx-auto">
    <h2 class="text-3xl md:text-4xl font-bold tracking-tight">Get in touch</h2>
    <p class="text-white/50 mt-4 leading-relaxed">
      Partnerships, press, or just curious what we're building next?
    </p>
    <a href="mailto:hello@manybrains.us" class="mt-8 inline-block px-10 py-4 bg-accent hover:bg-accent-hover text-white font-semibold rounded-xl text-base transition-all hover:-translate-y-0.5">
      hello@manybrains.us
    </a>
  </div>
</section>
```

- [ ] **Step 2: Create `src/components/Footer.astro`**

```astro
---
const year = new Date().getFullYear();
const links = [
  { name: 'Heather AI', url: 'https://getheatherai.com' },
  { name: 'Tutorville', url: 'https://tutorville.co' },
  { name: 'The Life Care Planners', url: 'https://thelifecareplanners.com' },
  { name: 'Yonderey', url: 'https://yonderey.com' },
  { name: 'Upscape', url: 'https://getupscaped.com' },
];
---
<footer class="border-t border-white/10 px-6 py-14">
  <div class="max-w-6xl mx-auto flex flex-col md:flex-row md:items-center md:justify-between gap-8">
    <div>
      <div class="font-bold tracking-tight">Many Brains</div>
      <p class="text-white/40 text-sm mt-2 max-w-sm leading-relaxed">
        Many Brains, Inc. — a Delaware C-Corporation.
      </p>
      <p class="text-white/30 text-xs mt-3">&copy; {year} Many Brains, Inc. All rights reserved.</p>
    </div>
    <div class="flex flex-col gap-2 text-sm">
      <span class="text-white/40 text-xs uppercase tracking-wider mb-1">Portfolio</span>
      {links.map((l) => (
        <a href={l.url} target="_blank" rel="noopener noreferrer" class="text-white/60 hover:text-white transition-colors">{l.name}</a>
      ))}
    </div>
    <div class="flex flex-col gap-2 text-sm">
      <span class="text-white/40 text-xs uppercase tracking-wider mb-1">Company</span>
      <a href="mailto:hello@manybrains.us" class="text-white/60 hover:text-white transition-colors">hello@manybrains.us</a>
      <a href="/privacy" class="text-white/60 hover:text-white transition-colors">Privacy</a>
      <a href="/terms" class="text-white/60 hover:text-white transition-colors">Terms</a>
    </div>
  </div>
</footer>
```

- [ ] **Step 3: Add to `src/pages/index.astro`**

```astro
import Contact from '../components/Contact.astro';
import Footer from '../components/Footer.astro';
```
```astro
    <Portfolio />
    <Contact />
  </main>
  <Footer />
```
(Note: `<Footer />` goes *after* the closing `</main>`.)

- [ ] **Step 4: Build and verify**

Run: `npm run build && grep -q "Delaware C-Corporation" dist/index.html && grep -q "mailto:hello@manybrains.us" dist/index.html && echo OK`
Expected: exits 0, prints `OK`.

- [ ] **Step 5: Commit**

```bash
git add -A
git commit -m "Add Contact and Footer sections"
```

---

### Task 8: Privacy and Terms pages

**Files:**
- Create: `src/pages/privacy.astro`, `src/pages/terms.astro`
- Consumes: `BaseLayout.astro`, `Footer.astro`, `Nav.astro`.

**Interfaces:**
- Produces: two static legal pages at `/privacy` and `/terms`, linked from the footer.

- [ ] **Step 1: Create `src/pages/privacy.astro`**

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
import Nav from '../components/Nav.astro';
import Footer from '../components/Footer.astro';
const updated = new Date().toLocaleDateString('en-US', { year: 'numeric', month: 'long', day: 'numeric' });
---
<BaseLayout title="Privacy Policy — Many Brains, Inc." description="Privacy policy for Many Brains, Inc." path="/privacy">
  <Nav />
  <main class="max-w-3xl mx-auto px-6 pt-32 pb-24 prose-invert">
    <h1 class="text-4xl font-bold tracking-tight">Privacy Policy</h1>
    <p class="text-white/40 text-sm mt-2">Last updated: {updated}</p>
    <div class="mt-8 space-y-5 text-white/70 leading-relaxed">
      <p>Many Brains, Inc. ("Many Brains," "we," "us") operates this website at manybrains.us. This policy explains how we handle information in connection with this corporate website.</p>
      <h2 class="text-xl font-semibold text-white mt-8">Information we collect</h2>
      <p>This is a static informational website. We do not operate accounts, forms, or trackers on this site. If you email us at hello@manybrains.us, we receive the information you choose to send.</p>
      <h2 class="text-xl font-semibold text-white mt-8">How we use information</h2>
      <p>We use information you send us solely to respond to your inquiry. We do not sell personal information.</p>
      <h2 class="text-xl font-semibold text-white mt-8">Our portfolio companies</h2>
      <p>Products and services operated by our portfolio companies are governed by their own separate privacy policies, available on their respective websites.</p>
      <h2 class="text-xl font-semibold text-white mt-8">Contact</h2>
      <p>Questions about this policy? Email <a href="mailto:hello@manybrains.us" class="text-accent">hello@manybrains.us</a>.</p>
    </div>
  </main>
  <Footer />
</BaseLayout>
```

- [ ] **Step 2: Create `src/pages/terms.astro`**

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
import Nav from '../components/Nav.astro';
import Footer from '../components/Footer.astro';
const updated = new Date().toLocaleDateString('en-US', { year: 'numeric', month: 'long', day: 'numeric' });
---
<BaseLayout title="Terms of Use — Many Brains, Inc." description="Terms of use for the Many Brains, Inc. website." path="/terms">
  <Nav />
  <main class="max-w-3xl mx-auto px-6 pt-32 pb-24">
    <h1 class="text-4xl font-bold tracking-tight">Terms of Use</h1>
    <p class="text-white/40 text-sm mt-2">Last updated: {updated}</p>
    <div class="mt-8 space-y-5 text-white/70 leading-relaxed">
      <p>These terms govern your use of the Many Brains, Inc. website at manybrains.us. By using this site you agree to these terms.</p>
      <h2 class="text-xl font-semibold text-white mt-8">Use of the site</h2>
      <p>This website is provided for general informational purposes. Content is provided "as is" without warranties of any kind.</p>
      <h2 class="text-xl font-semibold text-white mt-8">Intellectual property</h2>
      <p>The Many Brains name, logo, and site content are the property of Many Brains, Inc. Portfolio company names and logos are the property of their respective owners.</p>
      <h2 class="text-xl font-semibold text-white mt-8">External links</h2>
      <p>This site links to portfolio company websites operated separately. We are not responsible for the content of external sites.</p>
      <h2 class="text-xl font-semibold text-white mt-8">Contact</h2>
      <p>Questions? Email <a href="mailto:hello@manybrains.us" class="text-accent">hello@manybrains.us</a>.</p>
    </div>
  </main>
  <Footer />
</BaseLayout>
```

- [ ] **Step 3: Build and verify**

Run: `npm run build && test -f dist/privacy/index.html && test -f dist/terms/index.html && grep -q "Privacy Policy" dist/privacy/index.html && grep -q "Terms of Use" dist/terms/index.html && echo OK`
Expected: exits 0, prints `OK`.

- [ ] **Step 4: Commit**

```bash
git add -A
git commit -m "Add Privacy and Terms pages"
```

---

### Task 9: OG image, deploy config, README, and final verification

**Files:**
- Create: `public/og-image.png`, `README.md`, `vercel.json` (optional)
- Verify: full build + sitemap.

**Interfaces:**
- Produces: social-share image referenced by `BaseLayout`, deploy notes, and a final green build.

- [ ] **Step 1: Create a simple OG image `public/og-image.png` (1200×630)**

Generate a dark branded card. If ImageMagick is available:
```bash
which convert && convert -size 1200x630 xc:'#0a0a0a' \
  -gravity center -fill white -font Helvetica-Bold -pointsize 72 \
  -annotate +0-30 'Many Brains' \
  -fill '#E8734A' -pointsize 32 -annotate +0+60 'A portfolio of businesses, built by many brains.' \
  public/og-image.png
```
If `convert` is not available, copy a placeholder so the reference resolves and note it for later replacement:
```bash
which convert || cp public/logos/upscape.png public/og-image.png
```
Run: `file public/og-image.png`
Expected: `PNG image data` (or image of some kind).

- [ ] **Step 2: Create `README.md`**

```markdown
# Many Brains Corporate Website

The corporate website for **Many Brains, Inc.** (a Delaware C-Corporation), live at
[manybrains.us](https://manybrains.us). Built with Astro + Tailwind, deployed on Vercel.

## Develop

```bash
npm install
npm run dev        # http://localhost:4321
```

## Build

```bash
npm run build      # outputs static site to dist/
npm run preview    # preview the production build locally
```

## Deploy

Hosted on Vercel. Astro is auto-detected (build: `astro build`, output dir: `dist`).
Every push to `main` deploys to production; pull requests get preview URLs.
Production domain `manybrains.us` is configured in the Vercel project settings.

## Structure

- `src/pages/` — `index.astro` (landing), `privacy.astro`, `terms.astro`
- `src/components/` — Nav, Hero, BrainNetwork, Approach, Portfolio, Contact, Footer
- `src/layouts/BaseLayout.astro` — HTML shell + SEO/OG meta
- `public/logos/` — portfolio company logos
```

- [ ] **Step 3: (Optional) Create `vercel.json`**

Only if needed; Astro static is auto-detected. A minimal explicit config:
```json
{
  "buildCommand": "astro build",
  "outputDirectory": "dist"
}
```

- [ ] **Step 4: Full build and verify sitemap + all pages**

Run:
```bash
npm run build && \
test -f dist/index.html && test -f dist/privacy/index.html && test -f dist/terms/index.html && \
ls dist/sitemap*.xml && \
grep -q "manybrains.us" dist/sitemap-index.xml 2>/dev/null || grep -rq "manybrains.us" dist/sitemap*.xml && \
echo BUILD_OK
```
Expected: prints `BUILD_OK`; sitemap files exist and reference manybrains.us.

- [ ] **Step 5: Manual visual check**

Run: `npm run preview` and open the local URL. Confirm: hero animation pulses; all five portfolio logos render legibly on dark; links open the correct external sites; footer shows "Many Brains, Inc. — a Delaware C-Corporation"; mobile layout (narrow viewport) looks right; privacy/terms pages load.

- [ ] **Step 6: Commit**

```bash
git add -A
git commit -m "Add OG image, README, and deploy config"
```

---

### Task 10: Push and deploy to Vercel

**Files:** none (deployment).

- [ ] **Step 1: Push to GitHub**

```bash
git push origin main
```

- [ ] **Step 2: Connect to Vercel**

In Vercel: New Project → import `Many-Brains/manybrains-corp-website` → framework preset auto-detects **Astro** → deploy. (This is a manual dashboard step; the user has a Vercel account.)

- [ ] **Step 3: Configure domain**

In Vercel project → Settings → Domains → add `manybrains.us` (and `www.manybrains.us` redirect). Update DNS per Vercel's instructions at the domain registrar.

- [ ] **Step 4: Verify production**

Visit `https://manybrains.us`. Confirm the site loads, logos render, and links work. The site is now ready to reference in the Apple Developer enrollment.

---

## Self-Review

**Spec coverage:**
- Stack (Astro 6 / Tailwind 4 / sitemap / Node 22, Vercel) → Task 1, 9, 10 ✓
- Dark theme + orange accent tokens → Task 1 (global.css) ✓
- BrainNetwork signature visual → Task 3 ✓
- Nav / Hero / Approach / Portfolio / Contact / Footer sections → Tasks 4–7 ✓
- Hero headline & sub (verbatim from spec) → Task 4 ✓
- Portfolio: 5 cards, logos, exact URLs & one-liners, `_blank`/`noopener` → Task 6 ✓
- Logos sourced from sites + wordmark fallback + uniform treatment → Task 2 + Task 6 card styling ✓
- Contact `hello@manybrains.us` mailto → Task 7 ✓
- Footer "Many Brains, Inc. — a Delaware C-Corporation" + © + portfolio links → Task 7 ✓
- Privacy + Terms → Task 8 ✓
- SEO/OG/favicon/sitemap, site=manybrains.us → Task 1 (meta), 2 (favicon), 9 (og, sitemap) ✓
- README + Vercel deploy → Task 9, 10 ✓
- Success criteria (build passes, logos render, links resolve, C-Corp identified, deployed) → covered by per-task verifies + Task 9/10 ✓

**Placeholder scan:** No "TBD"/"implement later". The only conditional is the OG image generation (ImageMagick may be absent) — handled with an explicit fallback, not a placeholder. Logo acquisition has concrete URLs + verification + documented fallback.

**Type consistency:** `Company` interface fields (`name`, `logo`, `description`, `url`) used consistently in Task 6. Logo file paths in Task 2 match references in Task 6 exactly (`heather-ai.svg`, `tutorville.svg`, `lifecareplanners.svg`, `yonderey.png`, `upscape.png`). `BaseLayout` props (`title`, `description`, `path`) consistent across index/privacy/terms.
