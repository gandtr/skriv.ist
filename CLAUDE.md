# CLAUDE.md

## Project Overview

**skriv.ist** is the marketing landing page for three reading products: [comics.skriv.ist](https://comics.skriv.ist) and [books.skriv.ist](https://books.skriv.ist) (free, local-only, no account), and Skrivist Cloud (subscription sync/TTS, not yet for sale — waitlist only). The page showcases all three (hero promo video, feature highlights, three product cards) and drives the primary CTA to [books.skriv.ist](https://books.skriv.ist). The original app at [app.skriv.ist](https://app.skriv.ist) still runs unchanged for existing accounts but is no longer promoted; a footer link keeps it reachable. Made by [Gand Technology Services](https://gand.tr).

- **Live URL:** https://skriv.ist
- **App:** https://app.skriv.ist
- **Hosting:** Cloudflare Pages (project `skriv-ist`, static files). Deploys are **manual**: dispatch the "Deploy" GitHub Actions workflow (`gh workflow run deploy.yml` or Actions UI). Pushes do not deploy.
- **Repo:** `gandtr/skriv.ist`

## Tech Stack

- Pure HTML + CSS (no frameworks, no build tools, no package manager)
- Google Fonts: Playfair Display (headings), Lato (body)
- Minimal inline JS (theme toggle + language switcher + locale/orientation-aware hero video + scroll-reveal)
- The marketing page lives in `index.html`; legal/submission pages are standalone static files; promo video assets live in `media/`

## Project Structure

```
├── index.html                 # Marketing landing page (HTML + CSS + JS)
├── privacy.html               # Privacy Policy (app-store submission required)
├── terms.html                 # Terms of Service (app-store submission required)
├── support.html               # Support & account deletion (app-store submission required)
├── media/                     # Hero promo video assets, per locale (en, ja, tr, es, pt, de, fr)
│   ├── skrivist-promo-{lang}.mp4           # landscape 16:9 (desktop)
│   ├── skrivist-promo-{lang}-vertical.mp4  # portrait 9:16 (mobile)
│   ├── captions-{lang}[-vertical].vtt      # WebVTT captions (derived from promo VO scripts)
│   └── poster-{lang}[-vertical].jpg        # poster frames
├── .github/workflows/deploy.yml  # Manual-dispatch Cloudflare Pages deploy
├── .project-instructions.md   # Design/SEO context for developers
├── robots.txt                 # Search engine directives (+ sitemap reference)
├── sitemap.xml                # Sitemap for https://skriv.ist/
├── 404.html                   # Real 404 page — disables Cloudflare Pages' SPA fallback
├── llms.txt                   # Plain-text product summary for LLM crawlers
├── favicon.ico, apple-touch-icon.png  # Rendered from media/favicon.svg (ImageMagick)
├── site.webmanifest           # Minimal web app manifest (name + icons)
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
- `--text-primary: #1a2b3c` / `--text-secondary: #5a6e7a` / `--text-tertiary: #5f7280`
- `--accent-primary: #6a9fc0` / `--accent-secondary: #7db88a`
- `--surface-primary: rgba(255,255,255,0.9)`
- `--button-text: #1a2b3c` (dark ink on the accent-gradient CTAs; white failed WCAG AA)

**Dark Palette:**
- `--bg-gradient-1: #0f1923` / `--bg-gradient-2: #141e28` / `--bg-gradient-3: #111d1a`
- `--text-primary: #e2e8f0` / `--text-secondary: #94a3b8` / `--text-tertiary: #8a99ad`
- `--accent-primary: #7cb5d6` / `--accent-secondary: #8ec99a`
- `--surface-primary: rgba(20,30,40,0.85)`
- `--button-text: #0f1923`

## Key Conventions

- **No build tools** - intentionally lightweight. Do not introduce bundlers, frameworks, or package managers.
- **Single-file architecture** - all HTML, CSS, and JS stay inline in `index.html`.
- **All colors via CSS custom properties** - never hardcode colors in rules. Use `var(--...)` so themes work.
- **Preserve the aesthetic** - refined Nordic minimalism. Layered shadows, 16px container radius, 10px input/button radius.
- **Top bar** - `.top-bar` in top-right of container holds the language picker and theme toggle side by side.
- **Theme toggle** - sun/moon SVG icons. `.theme-toggle` styles must override inherited `button` styles.
- **Language picker** - dropdown with 7 languages (EN, JA, TR, ES, PT, DE, FR). Stored in `localStorage` key `skrivist-lang`. All translatable text uses `data-i18n` attributes; placeholders use `data-i18n-placeholder`; accessible names use `data-i18n-aria`.
- **Primary CTA** - "Start reading, free" links to `books.skriv.ist`; the hero promo video and three product cards (Comics free / Books free / Cloud) support conversion. Cloud is not for sale yet — its card links to a `mailto:` waitlist address (`contact@skriv.ist`), not a signup form.
- **Mobile responsive** - media queries at the 600px breakpoint for mobile layout.
- **Hero promo video** - locale-aware (swaps per language) and orientation-aware: `updateHeroVideo()` serves the vertical 9:16 cut on mobile (≤600px) and the landscape 16:9 cut on desktop, keyed to the same 600px breakpoint as the CSS. Sources and posters follow `media/skrivist-promo-{lang}[-vertical].mp4` / `media/poster-{lang}[-vertical].jpg`.
- **SEO is configured** - canonical tag, OG/Twitter cards, JSON-LD schema, keywords meta, robots.txt + sitemap.xml, and 404.html are all in place. New static files must also be added to the "Assemble site bundle" step in `deploy.yml`. The www→apex 301 lives in a Cloudflare zone **Redirect Rule** (dashboard: Rules → Redirect Rules), NOT in a Pages `_redirects` file — Pages `_redirects` cannot match on hostname.

## Working With This Project

- No install or build step. Edit `index.html` directly.
- Deploying: merge to `master`, then dispatch the "Deploy" workflow (`gh workflow run deploy.yml`). Nothing deploys automatically.
- Test locally by serving the folder (e.g. `python3 -m http.server`) — the hero video and posters use absolute `/media/...` paths, so opening via `file://` won't load them.
- When adding colors, always add to both light and dark theme variable blocks (`:root`, `[data-theme="dark"]`, and `@media (prefers-color-scheme: dark)` block).
- When adding translatable text, add `data-i18n="keyName"` attribute to the element and add the key to all 7 language objects in the `T` translations object in the JS block. For placeholders, use `data-i18n-placeholder="keyName"`. For accessible names, use `data-i18n-aria="keyName"` (`applyLang` sets `aria-label`).

## i18n System

Client-side translation via a `T` object in the inline `<script>`. All translatable elements use `data-i18n` attributes that map to keys in `T`. Language preference persisted in `localStorage` key `skrivist-lang`.

**Supported languages:** English (en), Japanese (ja), Turkish (tr), Spanish (es), Portuguese (pt), German (de), French (fr)

**Adding a new language:**
1. Add a new key to the `T` object with all required string keys
2. Add a new `<button class="lang-option" data-lang="xx">` in the `.lang-dropdown` HTML
