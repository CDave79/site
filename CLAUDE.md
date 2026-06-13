# CLAUDE.md

This file guides Claude Code (and other AI assistants) when working in this repository.

## 1. Repository Overview

This repository is a **GitHub Pages deployment target**, not a source code repository.

It contains **only the compiled static output** of a Next.js portfolio site for **Clifford LC**. The site is authored in a separate Next.js project and exported as static HTML/CSS/JS; the build artifacts are then published here so GitHub Pages can serve them.

There is **no application source code, `package.json`, component, or build tooling in this repo** — everything here is generated. Treat the contents as build output: read it to understand the shipped site, but do not hand-edit it (see §9).

## 2. File Tree

```
.
├── .nojekyll                 # Disables GitHub Pages' Jekyll processing so that
│                             #   _next/ (a leading-underscore dir) is served as-is.
├── index.html                # Home page — the single-page portfolio document.
├── 404.html                  # Custom not-found page served by GitHub Pages.
├── index.txt                 # Next.js RSC payload / prefetch data for the route.
└── _next/                    # Next.js build output (hashed, immutable assets).
    └── static/
        ├── css/              # Compiled Tailwind stylesheet(s), content-hashed.
        ├── chunks/           # JS bundles: framework, main, webpack, page chunks.
        └── 2oc3UFiPkcf3Ve6lw6mD-/   # Per-build directory keyed by the build ID.
```

**Build ID:** `2oc3UFiPkcf3Ve6lw6mD-`

The build ID changes on every production build. Asset paths under `_next/static/<buildId>/` and the hashed filenames under `css/` and `chunks/` are regenerated each time, so never reference them by hand or hardcode them elsewhere — they are cache-busted by design.

## 3. Inferred Source Architecture

The compiled output indicates the upstream source project is built with:

- **Next.js (App Router)** — the `_next/static/` layout, build-ID directory, and `index.txt` RSC payload are App Router signatures.
- **TypeScript** — the source is authored in TS (`.tsx`/`.ts`).
- **Tailwind CSS v3.4.19** — utility-first styling compiled into the hashed CSS under `_next/static/css/`.
- **Static export** — the project uses `output: 'export'` in `next.config`, producing fully static HTML with no server runtime. This is what makes GitHub Pages hosting possible (no Node server, no ISR, no API routes).

> These are *inferences from the build artifacts*. The authoritative source lives in the separate Next.js project, not here.

## 4. Site Sections (in document order)

The single-page site is composed of the following sections, in order:

1. **`#home`** — Hero section. Headline: **"Designing the Future of the Web."** with a primary call-to-action.
2. **`#about`** — About blurb plus skill badges: **React**, **Node.js**, **Tailwind CSS**, **PostgreSQL**, **Strapi CMS**, **TypeScript**.
3. **`#video`** — Product demo video player.
4. **`#projects`** — Project cards:
   - **E-Commerce System**
   - **AI Content Dashboard**
5. **`#contact`** — Contact section with an email call-to-action.
6. **Footer** — Closing footer with copyright and social links.

The fixed navigation links anchor to these section IDs (`#home`, `#about`, `#video`, `#projects`, `#contact`).

## 5. Navigation & Responsive Behavior

- **Fixed nav bar** pinned to the top of the viewport across all scroll positions.
- **Backdrop blur** — the nav uses a translucent background with `backdrop-blur` so content scrolls beneath it.
- **Dark mode toggle** — implemented via **React state** (a controlled toggle that adds/removes the `dark` class on the document root), **not** the CSS `prefers-color-scheme` media query. This gives the user explicit, manual control over the theme.
- **Mobile behavior** — the inline nav links are **hidden below the `md:` breakpoint** (`hidden md:flex`); on small screens the link list collapses.

## 6. Design System

### Color palette — blue / slate

- **Accent / brand:** blue scale (e.g. `blue-500`, `blue-600` for buttons and links, `blue-400` for accents in dark mode).
- **Neutrals:** slate scale (`slate-50`/`slate-100` light backgrounds, `slate-600`/`slate-700` body text, `slate-800`/`slate-900` dark-mode surfaces).

### Typography scale

- **Hero / H1:** large display sizes (`text-4xl`–`text-6xl`), bold, tight tracking.
- **Section headings (H2):** `text-3xl`–`text-4xl`, semibold/bold.
- **Card titles (H3):** `text-xl`–`text-2xl`, semibold.
- **Body:** `text-base`/`text-lg` with relaxed leading; muted slate text for secondary copy.

### Spacing conventions

- **Section rhythm:** generous vertical padding (`py-16`–`py-24`) to separate sections.
- **Container:** centered with horizontal padding (`mx-auto px-4`/`px-6`) and a max width.
- **Internal gaps:** flex/grid `gap-4`–`gap-8` for badge rows and card grids.

### Component patterns

- **Skill badge:** pill-shaped chip — rounded-full, subtle slate/blue background, small padding (`px-3 py-1`), `text-sm`, used for the `#about` skill list.
- **Project card:** rounded container (`rounded-xl`/`rounded-2xl`) with border/shadow, padded body, a title (H3), description, and a tag/preview area. Hover elevates (shadow/translate transition).
- **Primary button:** solid blue background (`bg-blue-600 hover:bg-blue-700`), white text, rounded, padded (`px-6 py-3`), used for hero CTA and contact CTA.
- **Video container:** responsive aspect-ratio wrapper (e.g. `aspect-video`) with rounded corners and overflow-hidden, holding the demo player.
- **Contact section:** centered heading, supporting copy, and a primary email CTA button.

## 7. Dark Mode Conventions

- Dark mode is **class-based** (Tailwind `darkMode: 'class'`): styles are written as `bg-white dark:bg-slate-900`, `text-slate-700 dark:text-slate-200`, etc. The React toggle swaps the `dark` class on `<html>`.
- Components pair every themed utility with its `dark:` counterpart (background, text, border).
- Theme changes are animated with **`transition-colors duration-500`** so the swap between light and dark fades smoothly rather than snapping.

When adding or updating UI in the source project, **always provide the matching `dark:` variant** and keep the `transition-colors duration-500` convention for color-changing elements.

## 8. Git Workflow

- **`main`** — production. Whatever is on `main` is what GitHub Pages publishes (and what the upstream server pulls to serve the site).
- **`claude/<slug>`** — feature/working branches for AI-assisted changes (e.g. `claude/claude-md-docs-0v4pe`). Branch from `main`, do the work, then open a PR back into `main`.

Never push directly to `main` without review; production deploys from it.

> **Preserve this file across publishes.** The publish/export pipeline overwrites `main` with fresh build output. `CLAUDE.md` is **not** part of that build output, so the pipeline must be configured to keep this file (and `.nojekyll`) rather than wiping it on each clean publish. If `CLAUDE.md` disappears after a deploy, the pipeline dropped it — re-add it and exclude it from the clean step.

## 9. AI Assistant Guidelines

When working in this repository:

- **Do not edit compiled files.** `index.html`, `404.html`, `index.txt`, and everything under `_next/` are generated build artifacts. Hand-edits will be overwritten on the next build and can break content hashes. Make real changes in the upstream Next.js source project and re-export.
- **Follow the established Tailwind patterns** when touching the source: blue/slate palette, the typography and spacing scales above, class-based dark mode with paired `dark:` variants, and `transition-colors duration-500`.
- **Update the remaining placeholders** in the source project:
  - Replace the placeholder contact email **`alex.doe@example.com`** with Clifford LC's real address.
  - Fill in the **`href="#"`** placeholder links for **GitHub** and **LinkedIn** in the footer/social section.
  - Populate the **empty Preview `<span>`s** on the project cards with real preview text/links.
  - Replace the **decorative/placeholder video** in `#video` with the actual product demo.

---

*This repo holds generated output only. The source of truth is the upstream Next.js project — edit there, build, and publish the result here.*
