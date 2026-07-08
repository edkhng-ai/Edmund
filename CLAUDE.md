# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

This repository contains a single static file, `index.html` — a one-page healthcare/clinic marketing site ("Willow Creek Family Clinic"). There is no build step, no package manager, and no test suite. All HTML, CSS (inline `<style>`), and JavaScript (inline `<script>`) live in that one file.

## Running the site

Open `index.html` directly in a browser (double-click, or a `file://` URL). No server, bundler, or dependency install is required or expected.

## Architecture

Everything is organized as clearly delimited sections within the single file, in document order:

- **`<style>` block** — CSS custom properties define the color palette (deep bark-green primary, willow-leaf green, creek teal, warm sun-amber accent) and shared tokens (`--color-primary`, `--color-willow`, `--color-creek`, `--color-sun`, etc.) at `:root`. Keep new styling consistent with these variables rather than hardcoding colors.
- **Navbar** (sticky) → **Hero** (`#home`) → **Services** (`#services`) → **Care Team** (`#team`) → **Your Visit** (`#visit`) → **Testimonials** (`#testimonials`) → **New Patient Guide / lead magnet** (`#guide`) → **Enquiry form** (`#contact`) → **Footer**, each a `<section>` with a matching id used for smooth-scroll navigation (`scroll-behavior: smooth` + anchor links). The top nav only links to Home/Services/Your Visit/Testimonials/Book Now to stay short; Team and Guide are reachable via scroll and footer links.
- A willow-branch SVG (`.willow-branch`) is the page's signature motif — a hand-authored line-art divider (ties to the "Willow Creek" name) reused between the hero and trust strip, and again above the footer. Each instance is inlined directly (not via `<symbol>`/`<use>`, since CSS descendant selectors don't reliably reach into `<use>` shadow trees for styling/animation). It "grows in" via a `pathLength="1"` + `stroke-dashoffset` CSS animation gated on the existing `.fade-in`/`.visible` scroll-reveal class, so it draws itself when scrolled into view.
- **`<script>` block** at the end of `<body>` handles five independent concerns, in order: footer year injection, mobile nav toggle, scroll-triggered fade-in via `IntersectionObserver` (applied to any element with the `.fade-in` class — this also triggers the willow-branch draw animation), enquiry form validation/submission, and New Patient Guide (lead magnet) form validation/submission.
- Both forms are validated and "submitted" entirely client-side: neither POSTs anywhere. On success each builds a plain data object (`enquiryData` / `leadMagnetData`) and `console.log`s it. The commented-out `fetch(...)` block directly above each `console.log` call is the intended spot to wire up a real backend endpoint later.

## Conventions to preserve when editing

- No frameworks, no build tooling, no external JS libraries — keep it that way; only the two Google Fonts (Lexend for headings, Source Sans 3 for body) are loaded externally.
- Icons are inline SVG (no icon font/library, no emoji as structural icons).
- Form field errors are surfaced inline via a `<span class="field-error">` per field plus `aria-invalid`/`aria-describedby` on the input — never use `alert()` for validation feedback.
- Respect `prefers-reduced-motion` (already handled globally in CSS) when adding new animations, including the willow-branch draw-in.
- SEO: keep the canonical link, Open Graph/Twitter meta tags, and the `MedicalClinic` JSON-LD block in `<head>` in sync with any copy/contact-info changes.
