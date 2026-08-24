# Aditya Upadhyay — Forward Deployed Engineering

A single-page, scroll-driven interactive deck: *Aditya Upadhyay's guide to AI Forward Deployed Engineering — the blueprint to breaking into FDE in 30 days.*

This is meant to replace a classic slide deck (PPT/Keynote) for this material — same narrative arc, but interactive, scroll-snapped, and easy to share as a link.

## What's here

- `index.html` — the entire site. Self-contained: no build step, no external fonts, no analytics, no network calls. Everything (styles, script, favicon) is inlined in this one file.

That's it. Static hosting only needs this file.

## Run it locally

Any static file server works. Pick one:

```bash
# Node
npx serve .

# Python 3
python -m http.server 8080

# PHP
php -S localhost:8080
```

Then open the printed URL. (Opening `index.html` directly via `file://` also works since there are no external requests, but a local server is closer to production behavior.)

## Deploy to GitHub Pages

1. Push this folder to a GitHub repository.
2. In the repo: **Settings → Pages → Source**, select the branch (e.g. `main`) and root folder (`/`).
3. Save. GitHub will publish at `https://<username>.github.io/<repo>/`.

```bash
git init
git add index.html README.md LICENSE .nojekyll
git commit -m "Initial site"
git branch -M main
git remote add origin <your-repo-url>
git push -u origin main
```

`.nojekyll` is included so GitHub Pages serves the file as-is without running it through Jekyll.

## Deploy anywhere else

Since it's a single static HTML file with no dependencies, it deploys unchanged to:

- **Netlify / Vercel / Cloudflare Pages** — drag-and-drop the folder, or connect the repo (no build command needed; output directory is `/`).
- **Any static web host / S3 + CloudFront / nginx** — just copy `index.html` to the web root.

## Customizing

All copy, section order, and interactive behavior live inline in `index.html`:

- Design tokens (colors, type, spacing) are declared once at the top of the `<style>` block under `:root{...}` — edit there to restyle globally.
- The name in the top-left mark and the opening line ("Aditya Upadhyay's guide to...") are the two places branding appears — search for `Aditya Upadhyay` to find both.
- Each section is a `<section class="sec" id="sNN">` inside `<main class="deck" id="deck">`, in scroll order.

---

## Starting a brand-new deck in this same theme

This site's visual system (colors, type, spacing, motion, section pattern) is not specific to the FDE content — it's a reusable "deck theme." To build an entirely different presentation (new topic, new content) that looks and feels like this one, keep the skeleton below and swap in new copy.

### Design tokens (the theme, in one place)

| Token | Value | Use |
|---|---|---|
| `--paper` | `#FFFFFF` | light section background |
| `--ink` | `#171A22` | primary text on light |
| `--navy` / `--blue` | `#022B72` | brand accent, dark section background, links/emphasis |
| `--blue-b` | `#8AA6D8` | accent on dark sections (light navy) |
| `--mut` | `#6B7280` | muted/secondary text on light |
| `--mut-d` | `#9FB2D4` | muted/secondary text on dark |
| `--lite` | `#F3F6FB` | primary text on dark |
| `--line` / `--line-d` | navy @ 16% / white @ 24% | hairline borders, on light / on dark |
| `--fail` | `#B23A2E` | negative/failure state only (evals table, etc.) |
| `--sans` | Neue Haas Grotesk stack → system fallback | the only typeface family used anywhere |
| `--ease` | `cubic-bezier(.22,.61,.21,1)` | the one motion curve used for every reveal |

Type scale (all fluid via `clamp()`, so it self-adjusts across screen sizes — never hardcode a pixel size for a heading):

| Class | Role |
|---|---|
| `.kicker` | small uppercase eyebrow label above a heading |
| `.hero-h` | biggest headline, opening/title section only |
| `.h-xl` / `.h-l` / `.h-m` | section headline sizes, largest to smallest |
| `.sub` | body/subtext paragraph, muted color, max-width capped for readability |

### The section pattern every slide follows

```html
<section class="sec g-light" id="sNN">   <!-- or g-dark for a navy section -->
  <div class="wrap">
    <div class="kicker r">Eyebrow label</div>
    <h2 class="h-l r dl2">Headline goes here</h2>
    <p class="sub r dl4">Supporting sentence or two.</p>
    <!-- section-specific content: diagram, cards, list, etc. -->
  </div>
</section>
```

- `g-light` (white/navy-text) or `g-dark` (navy/white-text) — alternate these to pace the deck, same as the original.
- `.r` = "reveal on scroll into view"; stack `.dl1`…`.dl14` on top of it to stagger elements in sequence (each step ≈0.15–0.3s after the last).
- Every section is one full viewport tall (`scroll-snap-align`), so keep content to what reads as one screen/slide.

### Reusable content shapes (pick one per slide)

The deck never uses a generic bullet-slide — every section is one of these shapes. When briefing new content, say which shape you want:

| Shape | What it looks like | Example in this deck |
|---|---|---|
| **Statement** | Kicker + headline + one line of sub, centered or left-aligned. Nothing else. | S01 opening, S03 |
| **Dark statement** | Same, on navy ground, for a tonal break/pivot moment | S05, S09 |
| **Chain / flow** | Row of labeled nodes connected by arrows (a process or handoff) | S05 "chain" |
| **Split-screen** | Two labeled columns converging into a center node | S06 |
| **Clickable step cards** | A row of numbered cards; click any to open a detail modal (tag, title, punchline, bullet list, callout) | S07 |
| **Workflow/system diagram** | Custom SVG boxes + connective lines with small labels | S07/S08/S13 |
| **Animated "watch it run" console** | A scripted log/console with a progress bar and status steps, for showing a live process | `srun` |
| **Stage sequence** | 3–4 short labeled stages in a row, each with one line underneath, arrows between | S09 |
| **Multi-column process board** | Numbered steps in columns with an artifact/output card at the end | S10 audit-flow |
| **Comparison table + report card** | A grid/table on one side, a stats/score summary card on the other | S12 eval-grid |
| **Three-column phase breakdown** | Three short phase blocks side by side, each with a heading + 1–2 lines | S13 phases |
| **Closing loop** | A short cyclical list of steps with a looping arrow, ending the narrative | S14 |
| **Persistent overlay motif** | One fixed background element that animates/advances across several consecutive sections (here: the 30-day calendar strip) | S15–S19 |

### Content brief template

Copy this block once per slide, fill it in, and hand me the whole set (plus "same theme as the FDE-Roadmap deck") to generate a new single-file deck.

```
SECTION [number]
Chapter nav label (optional, only for section that should appear in the top nav): 
Background: light | dark
Shape: [pick one from the table above, or "custom"]
Kicker (eyebrow): 
Headline: 
Subtext (1–2 sentences): 
Supporting content:
  - bullets / steps / stats / node labels / table rows — whatever the shape needs
Interaction notes (optional): e.g. "click to expand", "animate in on scroll", "highlight X in accent color"
Source/reference (optional): where this content/claim comes from
```

**Worked example** (this is section 1 of the current deck, filled in):

```
SECTION 1
Chapter nav label: Why the role exists
Background: light
Shape: Statement (hero)
Kicker: Aditya Upadhyay's guide to
Headline: AI Forward Deployed Engineering
Subtext: The blueprint to breaking into FDE in 30 days.
Supporting content: (none — title section only)
Interaction notes: "SCROLL ↓" cue pulses at the bottom
```

Hand me a filled-out set of these and I'll build the new deck as its own single `index.html`, matching this same token system, type scale, motion, and component shapes — just with your new content and section count.
