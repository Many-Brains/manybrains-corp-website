# Tech-Enabled Services Positioning + Portfolio Additions

**Date:** 2026-06-25
**Status:** Approved (assets supplied by user)

## Goal

Two updates to the Many Brains corporate site:

1. Give Many Brains a defined niche — **tech-enabled services** — threaded throughout the
   site's positioning copy.
2. Add three prior ventures (Bestowed, Brio, The Food Fix) to the portfolio, marked with
   status badges since they are acquired/closed rather than operating.

## 1. Positioning: "tech-enabled services"

Thread the niche through the existing copy (no new section). The current portfolio already
fits this framing — coaching apps, marketplaces, and concierge services are all
software-scaled human services — so the framing is honest.

- **Hero badge** (`Hero.astro`): `AI-accelerated venture studio` →
  `Tech-enabled services · venture studio`
- **Hero subhead** (`Hero.astro`): rework to name the niche, e.g.
  *"We build tech-enabled service businesses — software-scaled versions of expert, human
  services — take them to market, and scale what earns traction."*
- **H1**: unchanged — *"A venture studio, built by many brains."* (brand line)
- **Approach intro** (`Approach.astro`): add the niche to the supporting line under
  "How we work".
- **Meta** (`index.astro`): update `title` and `description` to lead with tech-enabled
  services.

## 2. Portfolio additions

Same grid in `Portfolio.astro`. Extend the `Company` interface:

- `url` becomes optional.
- Add optional `status?: 'Acquired' | 'Closed'`.

Rendering changes:

- If `status` is set, show a small pill (top-right of card). `Acquired` uses the accent
  color; `Closed` uses a muted (`white/40`) treatment.
- If `url` is absent, omit the "Visit site →" link and render the card as a non-link
  `<div>` (no hover-link affordance).
- **Logo fallback:** if `logo` is absent, render a text-monogram tile (first letter on an
  `accent/10` rounded tile) so every card looks finished.

Section subhead: *"Businesses we've built and operate."* →
*"Businesses we've built, operated, and exited."*

Card order: the 5 operating businesses first, then:

| Name | Status | Logo | Description |
|------|--------|------|-------------|
| Bestowed | Acquired | `bestowed.png` | A subscription-commerce pioneer delivering nutritionist-vetted food, supplements, and personal-care products. Grew to 50,000 monthly subscribers with partners like Uber and Women's Health before a successful acquisition. |
| Brio | Closed | `brio.png` | DNA-personalized nutrition combining genetic and personality profiling with remote coaching and custom meal plans — built on an exclusive license to LifeNome's trait-assessment engine and a Mount Sinai–led advisory board. |
| The Food Fix | Closed | `foodfix.png` | Concierge nutrition coaching at scale — a nationwide network of registered dietitians delivering personalized plans, video consults, and between-session support through a modern, tech-enabled experience. |

## Files touched

- `src/components/Hero.astro`
- `src/components/Approach.astro`
- `src/components/Portfolio.astro`
- `src/pages/index.astro`
- `public/logos/brio.png`, `public/logos/foodfix.png`, `public/logos/bestowed.png` (added)

## Out of scope

- No new sections or layout changes beyond the portfolio card variants.
- No changes to Partner/Contact/Footer.
