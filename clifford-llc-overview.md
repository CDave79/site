# Clifford LC — Project Overview & Infrastructure Plan

> Seed document for a Claude Project. Paste this in as knowledge so new chats
> start with full context. Update it as decisions change.

## 1. What Clifford LC is

A web + automation business run by Clifford LC. Two product lines:

- **Company presence** — the marketing site at clifford.lc (currently the
  static portfolio in the `site` GitHub repo). Expected to grow over time.
- **Client automations** — custom automation systems built for clients. These
  do **not** live on the company's own infrastructure; each client gets a
  dedicated, isolated environment.

## 2. The 3-layer architecture (keep these separate — never on one box)

| Layer | What it is | Where it lives now | Later |
|---|---|---|---|
| **1. Company site** (clifford.lc) | Marketing, grows over time | Static, free (GitHub Pages) | Small app host only when it needs a real backend |
| **2. Your backend / hub** | Your own tools, demos, internal CMS, where automations get *built* | One small VPS or app platform (Render / Railway / Hetzner / DO) | Same, scale up as needed |
| **3. Client automations** | Each client's running systems | One isolated environment **per client** (small instance) | Google Cloud (GCP) — its per-project model = natural per-client isolation + separate billing |

## 3. Decisions locked in

- **One client = one isolated environment.** Separate project, separate
  billing, separate data — from client #1. Isolation = security, compliance,
  and no "one client breaks, all break." This is the core principle that keeps
  us from re-architecting later.
- **Reusable template** for spinning up a new client environment, so each new
  client is copy-paste, not a rebuild.
- **GCP is the destination, not the starting point.** Don't pay its learning
  curve / cost-management overhead before there's revenue. First handful of
  clients go on Railway/Render/DO/Hetzner — faster and cheaper to operate.
- **Namecheap = registrar only** (domains, yours + clients'). Compute goes to
  Render/Railway/Hetzner/DO → GCP, not Namecheap shared hosting.

## 4. Now vs. later

- **Now:** company site stays free/static. Stand up one small backend (~$5–20/mo
  VPS, or a free/low Railway/Render tier) for your side. Land first clients on
  separate small instances — same isolation pattern, just smaller scale.
- **Later (GCP):** migrate client environments when scale or compliance demands
  it. Bill each client's instance into their retainer.

## 5. The repo (source of truth for code)

- `cdave79/site` — GitHub Pages deployment target. Holds **compiled static
  output only** (Next.js export). Do not hand-edit build artifacts; real changes
  happen in the upstream Next.js source project and get re-exported.
- See `CLAUDE.md` in that repo for full repo conventions (design system, dark
  mode, git workflow, placeholders still to fill).

## 6. Open items / placeholders still to finish on the site

- Replace placeholder contact email `alex.doe@example.com` with the real address.
- Fill in GitHub + LinkedIn footer links (currently `href="#"`).
- Populate empty project-card Preview text/links.
- Replace the placeholder `#video` demo with the real product demo.

## 7. How to use this Project

- Keep **strategy/planning** here (this doc + decisions as they evolve).
- Keep **code** in the git repo (version-controlled source of truth).
- Start new chats lean — they'll already know the business from this doc, so
  you don't re-explain the setup each time.
