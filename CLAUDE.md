# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Single-page marketing/booking site for Polyclinic JERICO (Ouagadougou, Burkina Faso). All user-facing copy is in **French**; keep new text in French and keep the code comments' French section headings consistent.

## Running

There is no build step, package manager, linter, or test suite. The whole site is one self-contained file, `index.html` (inline `<style>` and `<script>`, no external dependencies). Open it directly in a browser, or serve the folder:

```
python -m http.server 8000
```

## Architecture

`index.html` is organized top to bottom as: tokens (`:root` CSS variables) → per-section CSS → markup → one IIFE script. CSS and markup sections are marked with matching `/* ==== NAME ==== */` and `<!-- ==== NAME ==== -->` banners (BARRE D'URGENCE, HEADER, HERO, PÔLES, SPÉCIALITÉS, RENDEZ-VOUS, FENÊTRE DE PAIEMENT, FOOTER).

The script (vanilla ES5-style JS, `var`, `"use strict"`) has three independent behaviors:

- **Mobile menu** — `#burger-btn` toggles `.open` on `#main-menu` below 860px.
- **Symptom checker** — the `#symptom-select` option `value`s are keys into the `SYMPTOM_DATA` object. Each entry has `type` (`"ok"` or `"danger"`), `title`, `text`, and optional `call`. Adding a symptom means adding both an `<option>` and a matching `SYMPTOM_DATA` key. `type` maps directly to the `.ok` / `.danger` CSS classes on `#symptom-result`.
- **Appointment booking** — a 3-step flow that runs only in the browser: there is no backend, and nothing is sent or stored.
  1. Submitting `#rdv-form` validates the patient fields, stores them in `rdv`, fills the `#recap-*` summary, and opens the separate `<dialog id="payment-dialog">` with `showModal()`.
  2. In the dialog, payment buttons (`.pay-option[data-pay]`) set `selectedPayment`, whose value must exist in `PAYMENT_LABELS` (`orange`, `moov`, `especes`). `#pay-back`, `#pay-close`, a click on the backdrop and Escape all close the dialog without losing the form data.
  3. `#pay-confirm` closes the dialog and fills the `#confirmation-panel` `<dd>` elements inside the form.

  The dialog's padding lives on `.pay-dialog-inner`, not on the `<dialog>` itself. That is what lets `e.target === payDialog` detect backdrop clicks.

## Things that are coupled or easy to break

- The logo mark (a cross whose vertical bar ends in a "J", with a leaf) exists in several copies: `logo/jerico-mark.svg`, `logo/jerico-logo.svg`, the inline SVG in the header and footer `.mark` spans, and the URL-encoded favicon `<link rel="icon">`. The PNGs in `logo/` are exports of the SVGs. If the mark changes, update every copy and re-export the PNGs.
- The ambulance number `+226 70 00 12 34` is hard-coded in several places: the emergency bar, every `call` string in `SYMPTOM_DATA`, and the footer. Update all of them together.
- The sticky `header` uses `top: calc(safe-area + 38px)` to sit under the sticky `.urgence-bar`. If you change the emergency bar's height, update that offset too.
- `--font-display`, `--font-body` and `--font-mono` name Manrope, Work Sans and IBM Plex Mono, but no font is loaded, so the site currently renders with the system fallbacks.
- The `#poles` heading says "Quatre pôles", but the grid only contains 3 cards (no Urgences card).
- Each `.spe-card` has an inline SVG illustration (64×64 viewBox, `stroke="currentColor"`). Shapes with `class="s"` are filled white, and `.s-stroke` gets a white stroke.
- The 14 specialties are listed twice: as `.spe-card`s in `#specialites` and in the "Spécialités" `<optgroup>` of `#rdv-specialite`. The hero stat "14 Spécialités médicales" also depends on that count. Keep all three in sync.
- The page is designed mobile-first for smartphone users (safe-area insets, `@media` breakpoints at 900/860/760/520/420px). Check changes at narrow widths.
