# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A single-page marketing/landing page (in German) for a "Functional & HIIT" fitness course taught by Corinne Rindisbacher at the Dojo Fighters Club Bern. There is no build system, package manager, or server — it's plain static HTML/CSS/JS meant to be opened directly in a browser or hosted as-is (e.g. GitHub Pages).

## Files

- `index.html` — the live/deployed page. Identical to `hiit_landingpage_mitpreise.html` (keep both in sync if one is edited, or treat `hiit_landingpage_mitpreise.html` as redundant).
- `hiit_landingpage_mitpreise.html` — duplicate source of `index.html`.
- `Functional & HIIT – Corinne Rindisbacher.html` — a browser "Save As" snapshot (references a local `_files/` folder and `file:///C:/Users/...` path). This is a backup/archive copy, not a page to edit.
- `Hintergrundbild.avif`, `Fitpic.JPG`, `foto_corinne1.jpg.jpg` — source image assets. They are **not** loaded via relative `<img src>`/`url()` paths; the hero background and trainer photo are inlined as base64 `data:` URIs directly inside the `<style>`/`<img>` tags of `index.html`. If you replace an image, you must re-encode it to base64 and paste it into the relevant `data:image/...;base64,...` string in `index.html` (and `hiit_landingpage_mitpreise.html`) — dropping in the file alone will not change what renders.
- `HIIT_Preise_Corinne_v3.pdf` — a pricing flyer, not referenced from the HTML.

There is no build/lint/test tooling in this repo — just open `index.html` in a browser to preview changes.

## Architecture of `index.html`

Single file, no external JS/CSS dependencies except Google Fonts (Bebas Neue, Barlow Condensed, Barlow). Structure:

- **`<style>` in `<head>`**: all CSS, using custom properties under `:root` (`--red`, `--black`, `--gray`, `--light`, etc.) for the red/black fitness-brand theme. Sections alternate background via `section:nth-child(even)`.
- **Sections in `<body><main>`**, in order: `#hero`, `#training` (workout phases), `#benefits`, `#trainer` (bio/credentials), `#details` (day/time/location/dates), `#preise` (pricing), `#anmeldung` (contact/signup). Each section is self-contained with its own markup and, for `#preise`, its own `<style>`/`<script>`.
- **`editable`/`contenteditable="false"` markup**: most text elements carry `class="editable" contenteditable="false"`. This looks like leftover scaffolding from an HTML editor/generator tool (e.g. a WYSIWYG export) — toggling `contenteditable` to `"true"` would make that element inline-editable in a browser. Preserve this pattern when adding similarly-styled text elements for consistency with the rest of the file.

### Pricing logic (`#preise` section, near the end of the file)

Pricing is **not hardcoded in the markup** — it's computed by JS from a config object:

```js
const KURS_CONFIG = {
  vollkurs_preis: 200,
  spaeteinstieg_preis: 20,  // flat per-session price for late joiners, regardless of entry date
  einzeleintritt: 22,
  anzahl_termine: 13,       // total Tuesday sessions incl. the free trial
  erster_termin: 'TT.MM.JJJJ',
  letzter_termin: 'TT.MM.JJJJ'
};
```

- Simplified in 2026: late-entry pricing used to be staggered per calendar week (11 different prices) with a 13-row accordion table (`#preise-table`); this was replaced with a single flat `spaeteinstieg_preis` and the accordion/table markup, CSS, and JS were removed entirely.
- An IIFE right after this config reads `KURS_CONFIG`, derives the per-session full-course price (`vollkurs_preis / (anzahl_termine - 1)`, since the first session is a free trial), and renders the 3 pricing boxes (`#preise-boxes`): Vollkurs, Späteinstieg (flat rate), Einzeleintritt.
- **To update the course dates/prices, edit only `KURS_CONFIG`** — the rendered boxes update automatically. Do this identically in both `index.html` and `hiit_landingpage_mitpreise.html` since they're kept as duplicates.

### Contact info

Trainer contact details (email, phone, social links) live in the `#anmeldung` section's `.contact-box`. Note the `mailto:` href (`deine@email.ch`) is a placeholder that doesn't match the displayed email text (`knockout@gmx.ch`) — worth flagging/fixing if touching this section.
