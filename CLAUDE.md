# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A single proprietary presentation, `index.html` — a scroll-snapped, interactive deck (Aditya Upadhyay's "AI Forward Deployed Engineering" 30-day guide) built to replace a classic PPT/Keynote deck. It was cloned from `https://learn.varickagents.com/fde-in-30-days` and rebranded; content and design are otherwise original to that source and preserved intentionally (see README.md for what was changed and why).

There is no application, no package manager, no build system, and no test suite. Everything — HTML, CSS, JS, favicon — is inlined in the one `index.html` file so it deploys as a static asset to any host.

## Commands

There is nothing to install or build. To preview locally, serve the directory with any static file server (there is no dev-only tooling — any of these work identically):

```bash
npx serve .
python -m http.server 8080
php -S localhost:8080
```

Opening `index.html` directly via `file://` also works — the page makes zero network calls (no external fonts, no analytics, no APIs).

There is no lint or test command. To sanity-check an edit didn't break structure, grep for balanced tags rather than relying on a build step:

```bash
grep -c '<section class="sec' index.html   # should stay 20 unless sections were added/removed
grep -c '<script>' index.html; grep -c '</script>' index.html   # must match (currently 3 and 3)
tail -n 5 index.html   # should end cleanly with </script>\n\n</body>\n</html>
```

## Architecture

Everything lives in one file, in this order:

1. **`<head>`** — meta tags, an inline SVG data-URI favicon (no external asset), and the page `<title>`.
2. **One big `<style>` block** — design tokens declared once under `:root{...}` (colors, the `--sans` font stack, the `--ease` motion curve), then per-section CSS. This is the single source of truth for the visual theme; never hardcode a color or font elsewhere.
3. **Fixed chrome** — `.hud` (top bar: name mark + present/fullscreen toggle + chapter nav), `.pager` (prev/next buttons), `.oprail` (progress rail shown only during the audit/evals/deployment sections).
4. **`<main class="deck">`** — 20 `<section class="sec" id="sNN">` elements in scroll order, each one full viewport tall via CSS scroll-snap. Sections alternate `g-light`/`g-dark` grounds. Within a section, content follows a consistent skeleton: `.wrap > .kicker + heading (.hero-h/.h-xl/.h-l/.h-m) + .sub`, then section-specific content (diagram, cards, table, etc.).
5. **Three `<script>` blocks**, each an IIFE, each independent (no shared globals besides DOM lookups):
   - **Deck engine** (largest block): drives scroll navigation (chapters nav, prev/next pager, keyboard), an IntersectionObserver-style `.sec.on` reveal-on-scroll state, and two **shared persistent overlays** that span multiple sections rather than living inside any single one: `#stage` (the "intelligence becomes accessible" animation across sections 2–4, scene-switched via `SCENES`/config maps like `CHAP`, `RAIL`, `DARK`) and `#cal` (the 30-day calendar strip that persists and re-scales across sections 15–19, via `CALS`/`CALWK`). Also owns the scripted "watch one run" agent-theater sequence (`srun` section) — a timed cue player (`playCues`) that fakes a live agent run with a console log, progress bar, and a human-approval gate.
   - **Step-card modal** (S07 "understand the current workflow"): a `DATA` object keyed by step id, with `openModal`/`closeModal` wiring the clickable `.stepcard` elements to a detail modal (tag/title/punchline/bullets/callout). Adding a new clickable step means adding an entry to `DATA` plus a `.stepcard` with a matching `data-step`.
   - **Present/fullscreen toggle**: a small, self-contained cross-browser Fullscreen API wrapper (standard + `webkit`/`moz`/`ms` prefixes) driving the button next to the name mark in the header. Feature-detects and hides itself entirely on browsers without element-fullscreen support (e.g. iPhone Safari) rather than showing a dead control.

Reveal timing is driven purely by CSS: elements get `.r` (fade/slide in) and are staggered with `.dl1`…`.dl14` delay classes; the JS only ever toggles the parent `.sec.on` class, never touches individual element timing.

## Working in this repo

- **This is a rebrand-and-preserve project, not a from-scratch site.** Content and copy were deliberately kept verbatim from the source deck except for branding (see README.md's "What's here" / customizing notes for exactly what was changed). Don't rewrite curriculum copy unless asked.
- **Design tokens are load-bearing.** Any new section or component should reuse the existing `:root` tokens and the fluid `clamp()` type scale (`.kicker`, `.hero-h`, `.h-xl/.h-l/.h-m`, `.sub`) rather than introducing new colors, fonts, or pixel-fixed sizes.
- **README.md is the authoritative reference for extending this into a new deck.** It documents the full token table, the section HTML skeleton, a catalog of 13 reusable content "shapes" already used in this deck (statement, dark statement, chain/flow, split-screen, clickable step-cards+modal, workflow diagram, animated console, stage sequence, multi-column process board, comparison-table+report-card, three-column phases, closing loop, persistent overlay motif), and a fill-in-the-blank content-brief template for briefing a brand-new deck in the same theme. Consult it before inventing a new section pattern.
- **Deployment is covered in README.md** (GitHub Pages via `.nojekyll`, or any static host) — this repo has no CI/CD and needs none.
- **Licensing:** `LICENSE` is an all-rights-reserved proprietary notice under Aditya Upadhyay's name, not an open-source license — keep that in mind if asked about redistribution or reuse.
