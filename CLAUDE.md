# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Single-page marketing/booking site for Clinique JERICO (Ouagadougou, Burkina Faso). All user-facing copy is in **French**; keep new text in French and keep the code comments' French section headings consistent.

## Running

There is no build step, package manager, linter, or test suite. The whole site is one self-contained file, `index.html` (inline `<style>` and `<script>`, no external dependencies). Open it directly in a browser, or serve the folder:

```
python -m http.server 8000
```

## Architecture

`index.html` is organized top to bottom as: tokens (`:root` CSS variables) → per-section CSS → markup → one IIFE script. CSS and markup sections are marked with matching `/* ==== NAME ==== */` and `<!-- ==== NAME ==== -->` banners (BARRE D'URGENCE, HEADER, HERO, PÔLES, RENDEZ-VOUS, FOOTER).

The script (vanilla ES5-style JS, `var`, `"use strict"`) has three independent behaviors:

- **Mobile menu** — `#burger-btn` toggles `.open` on `#main-menu` below 860px.
- **Symptom checker** — the `#symptom-select` option `value`s are keys into the `SYMPTOM_DATA` object. Each entry has `type` (`"ok"` or `"danger"`), `title`, `text`, and optional `call`. Adding a symptom means adding both an `<option>` and a matching `SYMPTOM_DATA` key. `type` maps directly to the `.ok` / `.danger` CSS classes on `#symptom-result`.
- **Appointment form** — `#rdv-form` is client-side only: there is no backend, and nothing is sent or stored. Payment buttons (`.pay-option[data-pay]`) set `selectedPayment`, whose value must exist in `PAYMENT_LABELS` (`orange`, `moov`, `especes`). On submit, the script validates the fields and fills the `#confirmation-panel` `<dd>` elements.

## Things that are coupled or easy to break

- The ambulance number `+226 70 00 12 34` is hard-coded in several places: the emergency bar, every `call` string in `SYMPTOM_DATA`, and the footer. Update all of them together.
- The sticky `header` uses `top: calc(safe-area + 38px)` to sit under the sticky `.urgence-bar`. If you change the emergency bar's height, update that offset too.
- `--font-display`, `--font-body` and `--font-mono` name Manrope, Work Sans and IBM Plex Mono, but no font is loaded, so the site currently renders with the system fallbacks.
- The hero says "4 pôles", but the `#poles` grid only contains 3 cards (no Urgences card).
- The page is designed mobile-first for smartphone users (safe-area insets, `@media` breakpoints at 900/860/760/520/420px). Check changes at narrow widths.
