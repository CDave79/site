# CLAUDE.md

This file guides Claude Code (and other AI assistants) when working in this repository.

## 1. Repository Overview

This repository is the **GitHub Pages deployment target** for the **Clifford LC** marketing
site, served at **`clifford.lc`** (see `CNAME`).

Unlike a typical front-end project, there is **no build step and no framework here**. The site
is a **single hand-authored static `index.html`** with all CSS inline. What you see in the repo
is exactly what GitHub Pages serves — edit the files directly, commit, and the change ships.

> Note: there is no `package.json`, no `_next/`, no bundler, and no compiled output. Earlier
> versions of this file described a Next.js export; that is no longer accurate.

## 2. File Tree

```
.
├── index.html                # The entire public site (one page, inline CSS, tiny JS).
├── content/
│   └── home.json             # Editable homepage copy, loaded by index.html at runtime.
├── admin/
│   ├── index.html            # Sveltia CMS editor shell (loads the CMS from a CDN).
│   └── config.yml            # CMS config: backend + which fields map to content/home.json.
├── CNAME                     # Custom domain: clifford.lc
├── robots.txt                # Allows crawling; disallows /admin/.
├── clifford-llc-overview.md  # Business/infrastructure strategy doc (not part of the site).
├── mockup.html               # Local design mockup mirroring the live site — not linked from it.
├── .gitignore                # Ignores preview-dark.html (local-only viewing aid).
└── CLAUDE.md                 # This file.
```

## 3. How the Site Works

### Static page + runtime content hydration
- `index.html` ships with real fallback copy baked in. On load, a small inline script
  `fetch`es `content/home.json` and overwrites any element marked `data-cms="<key>"` with the
  matching value. If the JSON is missing or fails to load, the baked-in fallback text shows.
- **Keep the `data-cms` fallback text in `index.html` in sync with `content/home.json`.** The
  JSON wins at runtime, so if they disagree, the JSON value is what visitors actually see.
- The editable keys are: `hero_eyebrow`, `hero_headline`, `hero_subhead`, `cta_primary`,
  `contact_heading`, `contact_sub`.

### Content editing via Sveltia CMS (`/admin/`)
- `/admin/` loads **Sveltia CMS** (a Decap-compatible, git-based editor) from a CDN. It has
  **no server or database** — saving an edit commits straight to this repo's `main` branch.
- `admin/config.yml` defines the GitHub backend and the form fields that map onto
  `content/home.json`.
- **One-time setup still pending:** `base_url` in `admin/config.yml` is a placeholder
  (`https://REPLACE-WITH-YOUR-AUTH-WORKER.workers.dev`). The CMS login won't work until a
  Sveltia auth worker (Cloudflare) + a GitHub OAuth app are deployed and that URL is filled in.
  Guide: https://github.com/sveltia/sveltia-cms-auth

## 4. Page Sections (in document order)

`index.html` is a single page composed of:

1. **`#home`** — Hero. Eyebrow, headline (*"Your business is losing money."*), supporting copy,
   and two CTAs ("Start a conversation" → `#contact`, "See what's possible" → `#outcomes`).
2. **`#outcomes`** ("What We Do") — Six "Never…" outcome tiles (missed calls, lost leads,
   follow-ups, reviews, payments, reactivation).
3. **`#how`** ("How It Works") — Three step cards plus a 24/7 · <60s · 0 stats row.
4. **`#contact`** — Heading, supporting copy, and a `mailto:hello@cliffordlc.com` CTA.
5. **Footer** — Copyright and social links.

The sticky nav links anchor to `#home`, `#outcomes`, `#how`, `#contact`.

## 5. Design System

The site is styled with **plain CSS using CSS custom properties** — no Tailwind, no utility
classes. Theme values live as `--variables` on `.page`.

- **Dark mode is pure CSS, no JavaScript.** A visually-hidden checkbox (`#themeChk`) holds the
  state; `.theme-input:checked ~ .page { … }` swaps the variable values. The label `.switch` is
  an Apple-style toggle in the nav.
- **Color approach:** a light/blue/slate-leaning palette via variables (`--bg`, `--text`,
  `--heading`, `--muted`, `--border`, `--card`, `--accent` ≈ `#3f74d6`, plus badge/nav/switch
  tokens). Every theme value has a dark-mode counterpart.
- **Smooth theme transitions:** color-changing elements use `transition: … .5s ease` (or `.3s`–
  `.4s`) so light/dark fades rather than snaps. **Preserve this when editing.**
- **Layout primitives:** `.wrap` (max 720px) / `.wrap-wide` (max 1080px) centered containers,
  `section { padding: 96px 26px }`, flat rounded cards (`.feature`, `.card`, `.stat`) with
  `border-radius` 24–28px, pill `.btn` / `.badge`.
- **Type scale:** `h1` 54px, `h2` 34px, `h3` 20px, all with tight negative letter-spacing;
  `.eyebrow` is the uppercase accent kicker. Responsive breakpoints at 760/700/640/560px.

When editing UI: **work in the same CSS-variable system**, give every new themed value a
dark-mode counterpart in the `.theme-input:checked ~ .page` block, and keep the transition.

## 6. Open Items / Placeholders

- **CMS auth worker** — fill `base_url` in `admin/config.yml` (see §3) so `/admin/` login works.
- **Footer social links** — GitHub and LinkedIn are `href="#"` placeholders in `index.html`.
  Fill in the real URLs (or remove the links if there are no socials yet).
- The contact email is already real (`hello@cliffordlc.com`).

## 7. Editing Guidelines

- **Edit the files directly.** This repo *is* the published site — there's no separate source
  project and no build to run. A change to `index.html` (or `content/home.json`) is live once it
  reaches `main`.
- **Keep `index.html` fallback text and `content/home.json` in agreement** (see §3).
- **Don't touch third-party URLs** like the Sveltia CMS CDN script unless intentionally
  upgrading it.
- `mockup.html` and the git-ignored `preview-dark.html` are local design aids — not part
  of the live site; don't link to them from `index.html`.

## 8. Git Workflow

- **`main`** — production. GitHub Pages publishes whatever is on `main` to `clifford.lc`. Sveltia
  CMS also commits content edits directly to `main`.
- **`claude/<slug>`** — feature/working branches for AI-assisted changes. Branch from `main`, do
  the work, open a PR back into `main`.

Never push directly to `main` without review; production deploys from it.

---

*This repo is the live site itself — edit here, commit, and it ships. The `clifford-llc-overview.md`
strategy doc and the upstream "Agents" automation work live elsewhere and are out of scope for this
repository.*
