# ELIM TOUR

Website redesign and rebuild workspace for ELIM TOUR, Auckland, New Zealand.

## Codex: start here

Read these files in order:

1. [`AGENTS.md`](./AGENTS.md)
2. [`docs/NZELIMTOUR_CODEX_REDESIGN_BRIEF.md`](./docs/NZELIMTOUR_CODEX_REDESIGN_BRIEF.md)

The redesign brief contains the audited English/Korean site findings, canonical 9-product Bókun mapping, broken-route repairs, booking requirements, information architecture, visual direction, logo requirements, responsive/accessibility requirements, and acceptance criteria.

## Current production references

- English: https://www.nzelimtour.com/
- Korean: https://elim-tour-nz-korean.vercel.app/

## Critical rule

The Korean site's documented 9 Bókun product IDs and booking URLs are known-good mappings. Preserve and regression-test them during the rebuild.

## Workflow

Codex should create a feature branch, implement the brief, save before/after screenshots under `docs/redesign/`, document QA, then open a PR against `main` for review.
