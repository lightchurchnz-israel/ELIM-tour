# ELIM TOUR — Codex Instructions

## Mission
Build and redesign the ELIM TOUR website according to `docs/NZELIMTOUR_CODEX_REDESIGN_BRIEF.md`.

## Read first
1. Read the full redesign brief before editing code.
2. Inspect the current production sites:
   - English: https://www.nzelimtour.com/
   - Korean: https://elim-tour-nz-korean.vercel.app/
3. Capture baseline desktop/mobile screenshots before implementation.

## Non-negotiable booking safety
The Korean site's 9 Bókun mappings documented in the brief are the current source of truth. Do not change product IDs or booking URLs without verified evidence from Bókun. Regression-test every booking CTA after changes.

## Priorities
P0: booking integrity, broken routes, correct product mapping, real contact-form behavior.
P1: unified design system, logo, header/footer, tour cards, detail/booking UX, reviews, mobile UX.
P2: canonical data model, SEO, accessibility, performance.

## Design direction
Quiet premium New Zealand travel. Calm, local, trustworthy, nature-led. Avoid generic travel-agency tropes, loud gradients, airplane/map-pin logos, excessive luxury styling, and unnecessary animation.

## Logo
Create a canonical SVG logo system: horizontal, mark, white, one-color, and favicon variants. `ELIM` must remain legible at small header sizes. Use mountain/water/oasis/journey abstractions rather than literal airplane, globe, flag, or map-pin imagery.

## Required QA
Test desktop 1440px, tablet 1024px, mobile 390px. Click all navigation, all tour cards, all booking CTAs, Korean tour modal booking embeds, contact form, legal links, and external review links. Check console errors, horizontal overflow, keyboard navigation, focus behavior, accessibility, and internal 404s.

## Deliverables
Commit implementation plus `CHANGELOG.md`, before/after screenshots under `docs/redesign/`, link-check results, Bókun mapping verification, and a short Lighthouse summary. Use a feature branch and open a PR to `main` when complete.
