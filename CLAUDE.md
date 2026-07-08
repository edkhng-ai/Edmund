# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

This repository contains a single static file, `index.html` — a one-page healthcare/clinic marketing site ("Willow Creek Family Clinic"). There is no build step, no package manager, and no test suite. All HTML, CSS (inline `<style>`), and JavaScript (inline `<script>`) live in that one file.

## Running the site

Open `index.html` directly in a browser (double-click, or a `file://` URL). No server, bundler, or dependency install is required or expected.

## Architecture

Everything is organized as clearly delimited sections within the single file, in document order:

- **`<style>` block** — CSS custom properties define the color palette and shared tokens (`--color-primary`, `--color-teal`, `--color-accent`, etc.) at `:root`. Keep new styling consistent with these variables rather than hardcoding colors.
- **Navbar** (sticky) → **Hero** (`#home`) → **Services** (`#services`) → **Testimonials** (`#testimonials`) → **Enquiry form** (`#contact`) → **Footer**, each a `<section>` with a matching id used for smooth-scroll navigation (`scroll-behavior: smooth` + anchor links).
- **`<script>` block** at the end of `<body>` handles four independent concerns, in order: footer year injection, mobile nav toggle, scroll-triggered fade-in via `IntersectionObserver` (applied to any element with the `.fade-in` class), and enquiry form validation/submission.
- The enquiry form is validated and "submitted" entirely client-side: it does not POST anywhere. On success it builds an `enquiryData` object and `console.log`s it. The commented-out `fetch(...)` block directly above that `console.log` call is the intended spot to wire up a real backend endpoint later.

## Conventions to preserve when editing

- No frameworks, no build tooling, no external JS libraries — keep it that way; only the two Google Fonts (Inter, Poppins) are loaded externally.
- Icons are inline SVG (no icon font/library).
- Form field errors are surfaced inline via a `<span class="field-error">` per field plus `aria-invalid`/`aria-describedby` on the input — never use `alert()` for validation feedback.
- Respect `prefers-reduced-motion` (already handled globally in CSS) when adding new animations.
