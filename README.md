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
