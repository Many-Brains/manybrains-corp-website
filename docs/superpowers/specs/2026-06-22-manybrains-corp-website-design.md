# Many Brains Inc. — Corporate Website Design

**Date:** 2026-06-22
**Status:** Approved
**Repo:** Many-Brains/manybrains-corp-website
**Production domain:** https://manybrains.us (hosted on Vercel)

## Purpose

Build the corporate website for **Many Brains, Inc.** (a Delaware C-Corporation). The
immediate driver is to satisfy the Apple Developer Program organization-enrollment
requirement that the company have a credible, public corporate website whose name and
details match the legal entity. The site also serves as the public face of the Many
Brains business portfolio.

Many Brains operates a portfolio of businesses with an AI-assisted operating model: a
small team produces the output of many, accelerated by "many AI brains" — the source of
the company name.

## Scope

A single-page marketing/brochure site plus two standard legal pages. No backend, CMS,
forms, blog, or analytics. Strictly the Many Brains corporate site — portfolio companies
keep their own separate sites and are only linked out to.

### In scope
- One landing page (`/`) with all primary content.
- `/privacy` and `/terms` — simple, standard static pages for legitimacy.
- SEO meta + Open Graph tags, favicon, sitemap.
- Vercel deployment configuration.
- README with dev/build/deploy notes.

### Out of scope (YAGNI)
- Blog, CMS, contact forms, backend/API, authentication, analytics, multi-page
  navigation beyond the legal pages.

## Tech Stack

Reuse the proven stack from the Heather AI website (`~/projects/heather-ai-website`),
whose design the owner wants to mirror:

- **Astro 6** — static output.
- **Tailwind CSS 4** (via `@tailwindcss/vite`).
- **@astrojs/sitemap** integration.
- **Node >= 22.12.0**.
- **Vercel** hosting — Astro is auto-detected; static build, zero server config.

Rationale: the codebase we want to visually mimic already exists in this stack, so the
visual language transfers directly; Astro static is fast, cheap, and reliable for an
Apple reviewer to load. Plain HTML was rejected as too much of a downgrade; Next.js was
rejected as heavier than needed for a static brochure site.

## Visual Design

- **Theme:** dark. Background `#0a0a0a`, surface `#111111`.
- **Accent:** warm orange `#E8734A` (hover `#D4623C`) — reused from Heather AI for
  family consistency across the portfolio.
- **Typography:** system font stack (matches Heather AI `global.css`).
- **Signature element:** adapt the existing `PatternNetwork.astro` animated SVG into a
  "Many Brains" neural-network hero visual — nodes light up and route signals across a
  graph, reinforcing the many-brains / AI-collaboration metaphor.

## Page Structure (`/`)

Sections, top to bottom:

1. **Nav** — "Many Brains" wordmark + anchor links: Approach, Portfolio, Contact.
2. **Hero** — network animation background/feature with headline and sub.
   - Headline: *"A portfolio of businesses, built by many brains."*
   - Sub: *"We operate a growing portfolio of companies — accelerated by AI, run by a
     small team doing the work of many."*
3. **Approach** ("Why Many Brains") — three cards:
   - *AI-assisted operating model*
   - *Small team, large output*
   - *Portfolio of real businesses*
4. **Portfolio grid** — five cards, each linking out (target `_blank`, `rel="noopener"`):
   - **Heather AI** — AI nutrition coaching that gives real-time guidance for meals,
     dining out, and GLP-1 transitions. → https://getheatherai.com
   - **Tutorville** — A tutoring marketplace connecting students with vetted tutors for
     measurable results. → https://tutorville.co
   - **The Life Care Planners** — An expert-witness directory connecting attorneys with
     qualified life care planning experts. → https://thelifecareplanners.com
   - **Yonderey** — A marketplace pairing adventurers with certified mountain guides to
     plan expeditions. → https://yonderey.com
   - **Upscape** — Custom luxury landscape lighting design and installation for
     homeowners. → https://getupscaped.com
5. **Contact** — `hello@manybrains.us` mailto CTA.
6. **Footer** — "Many Brains, Inc. — a Delaware C-Corporation", © current year,
   portfolio links, contact email.

## Components (Astro)

Each component has one clear purpose, mirroring the Heather AI structure:

- `layouts/BaseLayout.astro` — HTML shell, `<head>` SEO/OG meta, global styles import.
- `components/Nav.astro` — top navigation + wordmark.
- `components/Hero.astro` — headline, sub, CTA; embeds the network visual.
- `components/BrainNetwork.astro` — adapted from `PatternNetwork.astro`; the animated
  node-graph SVG.
- `components/Approach.astro` — the three "why" cards.
- `components/Portfolio.astro` — the five portfolio cards (data-driven from a small
  array of company objects).
- `components/Contact.astro` — contact CTA section.
- `components/Footer.astro` — legal line, links, copyright.
- `pages/index.astro` — composes the above.
- `pages/privacy.astro`, `pages/terms.astro` — standard legal copy.
- `styles/global.css` — Tailwind import + `@theme` tokens (bg, accent, font).

## Configuration

- `astro.config.mjs` — `site: 'https://manybrains.us'`, sitemap + tailwind vite plugin.
- `package.json` — same scripts as Heather AI (`dev`, `build`, `preview`, `astro`).
- Favicon + OG image in `public/`.
- `.gitignore` already present (node_modules, dist, .astro, env, etc.).

## Deployment

- Vercel project linked to this GitHub repo; framework preset = Astro (auto-detected).
- Production domain `manybrains.us` configured in Vercel.
- Every push to `main` deploys to production; PRs get preview URLs.

## Success Criteria

1. `npm run build` produces a static site with no errors.
2. Site renders correctly: hero animation, all five portfolio links resolve to the
   correct external sites, contact mailto works.
3. Footer clearly identifies "Many Brains, Inc." as a Delaware C-Corporation.
4. Deployed to Vercel and reachable at manybrains.us.
5. Legal name and details on the site match the C-Corp for Apple Developer verification.

## Testing

- `npm run build` must succeed (primary gate for a static site).
- Manual visual check via `npm run preview` / Vercel preview: layout, animation,
  link targets, mobile responsiveness.
