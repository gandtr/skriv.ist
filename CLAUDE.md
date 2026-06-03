# CLAUDE.md

## Project Overview

**skriv.ist** is the marketing landing page for a minimalist, distraction-free ebook reader PWA. The app is live — the page showcases the product (hero promo video, feature highlights, pricing tiers) and drives signups to the app at [app.skriv.ist](https://app.skriv.ist). Made by [Gand Technology Services](https://gand.tr).

- **Live URL:** https://skriv.ist
- **App:** https://app.skriv.ist
- **Hosting:** GitHub Pages (static files, deploys on push to `master`)
- **Repo:** `gandtr/skriv.ist`

## Tech Stack

- Pure HTML + CSS (no frameworks, no build tools, no package manager)
- Google Fonts: Playfair Display (headings), Lato (body)
- Minimal inline JS (theme toggle + language switcher + locale/orientation-aware hero video + scroll-reveal)
- Everything lives in a single `index.html`; promo video assets live in `media/`

## Project Structure

```
├── index.html                 # The entire site (HTML + CSS + JS)
├── media/                     # Hero promo video assets, per locale (en, ja, tr, es, pt, de, fr)
│   ├── skrivist-promo-{lang}.mp4           # landscape 16:9 (desktop)
│   ├── skrivist-promo-{lang}-vertical.mp4  # portrait 9:16 (mobile)
│   └── poster-{lang}[-vertical].jpg        # poster frames
├── .project-instructions.md   # Design/SEO context for developers
├── CNAME                      # GitHub Pages custom domain config
├── robots.txt                 # Search engine directives
└── CLAUDE.md                  # This file
```

> Promo videos are produced in the sibling project `../skrivist-promo-video/` and its renders (`out/skrivist-promo-{lang}[-vertical].mp4`) are copied into `media/`.

## Design System

**Aesthetic:** Nordic-inspired, refined minimalism — warm and approachable, but calm. Frosted glass container, floating particles (with occasional ✦ sparkles), morphing organic icons that cradle line-art glyphs, soft frosted feature cards, layered shadows, and gentle choreographed motion (hero entrance + scroll-reveal). All motion respects `prefers-reduced-motion`.

**Typography:**
- Playfair Display (serif, italic 400) for h1 headline
- Playfair Display (serif, bold 700) for logo and feature-card headings (Title Case)
- Lato (sans-serif, 300/400) for body text and UI

**Theme System:** Light and dark themes via CSS custom properties on `:root`. Three-state logic:
- No `data-theme` attribute: follows OS preference via `prefers-color-scheme`
- `data-theme="light"`: forced light
- `data-theme="dark"`: forced dark
- Manual preference stored in `localStorage` key `skrivist-theme`
- Anti-flash script in `<head>` applies stored theme before render

**Light Palette (CSS custom properties):**
- `--bg-gradient-1: #dae8f3` / `--bg-gradient-2: #f5f7f5` / `--bg-gradient-3: #d5ecd0`
- `--text-primary: #1a2b3c` / `--text-secondary: #5a6e7a` / `--text-tertiary: #8a9aa6`
- `--accent-primary: #6a9fc0` / `--accent-secondary: #7db88a`
- `--surface-primary: rgba(255,255,255,0.9)`

**Dark Palette:**
- `--bg-gradient-1: #0f1923` / `--bg-gradient-2: #141e28` / `--bg-gradient-3: #111d1a`
- `--text-primary: #e2e8f0` / `--text-secondary: #94a3b8` / `--text-tertiary: #64748b`
- `--accent-primary: #7cb5d6` / `--accent-secondary: #8ec99a`
- `--surface-primary: rgba(20,30,40,0.85)`

## Key Conventions

- **No build tools** - intentionally lightweight. Do not introduce bundlers, frameworks, or package managers.
- **Single-file architecture** - all HTML, CSS, and JS stay inline in `index.html`.
- **All colors via CSS custom properties** - never hardcode colors in rules. Use `var(--...)` so themes work.
- **Preserve the aesthetic** - refined Nordic minimalism. Layered shadows, 16px container radius, 10px input/button radius.
- **Top bar** - `.top-bar` in top-right of container holds the language picker and theme toggle side by side.
- **Theme toggle** - sun/moon SVG icons. `.theme-toggle` styles must override inherited `button` styles.
- **Language picker** - dropdown with 7 languages (EN, JA, TR, ES, PT, DE, FR). Stored in `localStorage` key `skrivist-lang`. All translatable text uses `data-i18n` attributes; placeholders use `data-i18n-placeholder`.
- **Primary CTA** - "Start Reading for Free" links to the app at `app.skriv.ist`; the hero promo video and pricing tiers (Free / Maker / Member) support conversion. There is no email/waitlist form.
- **Mobile responsive** - media queries at the 600px breakpoint for mobile layout.
- **Hero promo video** - locale-aware (swaps per language) and orientation-aware: `updateHeroVideo()` serves the vertical 9:16 cut on mobile (≤600px) and the landscape 16:9 cut on desktop, keyed to the same 600px breakpoint as the CSS. Sources and posters follow `media/skrivist-promo-{lang}[-vertical].mp4` / `media/poster-{lang}[-vertical].jpg`.
- **SEO is configured** - OG/Twitter cards, JSON-LD schema, keywords meta, robots.txt are all in place.

## Working With This Project

- No install or build step. Edit `index.html` directly.
- Push to `master` to deploy (GitHub Pages builds automatically).
- Test locally by serving the folder (e.g. `python3 -m http.server`) — the hero video and posters use absolute `/media/...` paths, so opening via `file://` won't load them.
- When adding colors, always add to both light and dark theme variable blocks (`:root`, `[data-theme="dark"]`, and `@media (prefers-color-scheme: dark)` block).
- When adding translatable text, add `data-i18n="keyName"` attribute to the element and add the key to all 7 language objects in the `T` translations object in the JS block. For placeholders, use `data-i18n-placeholder="keyName"`.

## i18n System

Client-side translation via a `T` object in the inline `<script>`. All translatable elements use `data-i18n` attributes that map to keys in `T`. Language preference persisted in `localStorage` key `skrivist-lang`.

**Supported languages:** English (en), Japanese (ja), Turkish (tr), Spanish (es), Portuguese (pt), German (de), French (fr)

**Adding a new language:**
1. Add a new key to the `T` object with all required string keys
2. Add a new `<button class="lang-option" data-lang="xx">` in the `.lang-dropdown` HTML
