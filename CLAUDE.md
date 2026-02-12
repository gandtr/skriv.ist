# CLAUDE.md

## Project Overview

**skriv.ist** is a "coming soon" landing page for a minimalist, distraction-free ebook reader PWA. The site collects waitlist signups. Made by [Gand Technology Services](https://gand.tr).

- **Live URL:** https://skriv.ist
- **Hosting:** GitHub Pages (static files, deploys on push to `master`)
- **Repo:** `c0ze/skriv.ist`

## Tech Stack

- Pure HTML + CSS (no frameworks, no build tools, no package manager)
- Google Fonts: Playfair Display (headings), Lato (body)
- Minimal inline JS (theme toggle + language switcher + form handler)
- Everything lives in a single `index.html`

## Project Structure

```
├── index.html                 # The entire site (HTML + CSS + JS)
├── .project-instructions.md   # Design/SEO context for developers
├── CNAME                      # GitHub Pages custom domain config
├── robots.txt                 # Search engine directives
└── CLAUDE.md                  # This file
```

## Design System

**Aesthetic:** Nordic-inspired, refined minimalism. Frosted glass container, floating particles, morphing blob icons, layered shadows. Sharp but calm.

**Typography:**
- Playfair Display (serif, italic 400) for h1 headline
- Playfair Display (serif, bold 700) for logo
- Lato (sans-serif, 300/400) for body text and feature headings (uppercase)

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
- **Waitlist CTA is the primary goal** - the email signup form is the core conversion element.
- **Mobile responsive** - media queries at 600px breakpoint for mobile layout.
- **SEO is configured** - OG/Twitter cards, JSON-LD schema, keywords meta, robots.txt are all in place.

## Working With This Project

- No install or build step. Edit `index.html` directly.
- Push to `master` to deploy.
- Test locally by opening `index.html` in a browser.
- When adding colors, always add to both light and dark theme variable blocks (`:root`, `[data-theme="dark"]`, and `@media (prefers-color-scheme: dark)` block).
- When adding translatable text, add `data-i18n="keyName"` attribute to the element and add the key to all 7 language objects in the `T` translations object in the JS block. For placeholders, use `data-i18n-placeholder="keyName"`.

## i18n System

Client-side translation via a `T` object in the inline `<script>`. All translatable elements use `data-i18n` attributes that map to keys in `T`. Language preference persisted in `localStorage` key `skrivist-lang`.

**Supported languages:** English (en), Japanese (ja), Turkish (tr), Spanish (es), Portuguese (pt), German (de), French (fr)

**Adding a new language:**
1. Add a new key to the `T` object with all required string keys
2. Add a new `<button class="lang-option" data-lang="xx">` in the `.lang-dropdown` HTML
