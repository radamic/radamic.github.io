# radamic.com

[![deploy](https://github.com/radamic/radamic.github.io/actions/workflows/deploy.yml/badge.svg)](https://github.com/radamic/radamic.github.io/actions/workflows/deploy.yml)
[![site](https://img.shields.io/badge/site-radamic.com-be6069)](https://radamic.com)
[![built with astro](https://img.shields.io/badge/built%20with-astro%206-191c23)](https://astro.build)

Public marketing site for **radamic** - an offensive security outfit delivering
red team operations, adversary emulation, exploit development, and security
research.

The site is a single, statically-rendered landing page. It ships **no client-side
JavaScript, no trackers, and no analytics** by design; that posture is a
deliberate part of the brand and must be preserved in any change (see
[Security & privacy posture](#security--privacy-posture)).

- **Production:** <https://radamic.com>
- **Hosting:** GitHub Pages (static)
- **Owner:** radamic - `contact@radamic.com`
- **Built & maintained by:** prodrom3
- **Status:** production

---

## Table of contents

- [Tech stack](#tech-stack)
- [Prerequisites](#prerequisites)
- [Quick start](#quick-start)
- [Available scripts](#available-scripts)
- [Project structure](#project-structure)
- [Editing content](#editing-content)
- [Build & deployment](#build--deployment)
- [Security & privacy posture](#security--privacy-posture)
- [Coding conventions](#coding-conventions)
- [Quality gates](#quality-gates)
- [Browser support](#browser-support)
- [Contributing](#contributing)
- [License](#license)

---

## Tech stack

| Concern       | Choice                                              |
| ------------- | --------------------------------------------------- |
| Framework     | [Astro 6](https://astro.build) (static output)      |
| Styling       | [Tailwind CSS v4](https://tailwindcss.com) via Vite |
| Language      | TypeScript (strict), Astro components               |
| Typography    | JetBrains Mono                                      |
| Hosting / CI  | GitHub Pages + GitHub Actions                       |
| Runtime (dev) | Node.js `>= 22.12.0`                                |

## Prerequisites

- **Node.js `>= 22.12.0`** (see `engines` in `package.json`). Use a version
  manager such as `nvm`, `fnm`, or `mise` to pin it.
- **npm** (ships with Node). The repo commits `package-lock.json`; use `npm ci`
  for reproducible installs.

## Quick start

```bash
git clone git@github.com:radamic/radamic.github.io.git
cd radamic.github.io
npm ci                # reproducible install from the lockfile
npm run dev           # http://localhost:4321
```

## Available scripts

| Script            | Description                                       |
| ----------------- | ------------------------------------------------- |
| `npm run dev`     | Start the dev server at `localhost:4321`          |
| `npm run build`   | Type-check and build the static site to `./dist`  |
| `npm run preview` | Serve the production build locally                |
| `npm run astro`   | Run the Astro CLI directly                        |

## Project structure

```text
.github/workflows/deploy.yml   CI: build + deploy to GitHub Pages on push to main
astro.config.mjs               site URL, sitemap + Tailwind (Vite) plugin
public/                        copied verbatim to the site root
  CNAME                        custom-domain pin (radamic.com)
  robots.txt, og.png           SEO + social share assets
  .well-known/security.txt     RFC 9116 security contact
  favicon.svg, favicon.ico
src/
  layouts/Layout.astro         page shell: head, meta/OG tags, JSON-LD, fonts
  components/                  hero, about, capabilities, work, trust, contact, header, footer
  pages/index.astro            single-page composition
  styles/global.css            Tailwind v4 entry + theme tokens
tsconfig.json                  extends astro/tsconfigs/strict
```

## Editing content

The page is composed from section components in `src/components/` and assembled
in `src/pages/index.astro`. Most copy lives in the component that
renders it:

- **Hero headline / positioning** - `src/components/Hero.astro`
- **About / company facts** - `src/components/About.astro`
- **Capabilities** - the `capabilities` array at the top of `src/components/Capabilities.astro`
- **Selected work** - the `work` array at the top of `src/components/Work.astro`
- **Contact details / PGP / scope** - `src/components/Contact.astro`
- **Global `<title>` and meta description** - the `Layout.astro` prop defaults

Theme colors, fonts, and the terminal aesthetic are centralized as CSS custom
properties in `src/styles/global.css` (`@theme` block). Change tokens there
rather than hard-coding colors in components.

## Build & deployment

Deployment is automated. Any push to `main` triggers
[`.github/workflows/deploy.yml`](.github/workflows/deploy.yml), which:

1. Checks out the repo and installs with `npm ci` (Node 22).
2. Runs `npm run build` to produce `./dist`.
3. Uploads the artifact and publishes it to GitHub Pages.

The workflow uses least-privilege permissions (`contents: read`, `pages: write`,
`id-token: write`) and `concurrency` to cancel superseded runs. The custom
domain is pinned by `public/CNAME`; do not remove it or the domain mapping
breaks on the next deploy.

**Rollback:** revert the offending commit on `main` (or re-run the workflow from
a known-good commit via *Actions > deploy > Run workflow*). There is no separate
staging environment - validate locally with `npm run build && npm run preview`
before merging.

## Security & privacy posture

This is a public site for a security company; the repo is treated accordingly.

- **No third-party runtime scripts, trackers, or analytics.** Preserve this. Any
  PR introducing a tag manager, analytics beacon, or external script should be
  rejected unless there is a signed-off reason.
- **No secrets in the repo.** This is a static marketing site - it must never
  contain credentials, internal tooling, client data, engagement artifacts, or
  API keys. CI requires none.
- **No third-party runtime requests.** The web font is self-hosted via
  `@fontsource-variable/jetbrains-mono`; there are no external fonts, scripts,
  or CDNs. Do not reintroduce any (it would leak visitor IPs and break the
  no-tracking promise).
- **Dependencies:** keep the surface minimal. Review `npm audit` on dependency
  bumps; Dependabot/renovate updates are welcome for Astro, Tailwind, and Vite.
- **Disclosure:** security contact and policy are published at
  `/.well-known/security.txt` (`security@radamic.com`; PGP key at `/pgp.txt`).

## Coding conventions

- **Components are presentational and static.** Prefer server-rendered Astro with
  zero client JS. If interactivity is ever required, justify it and keep it
  progressively enhanced.
- **Style with theme tokens.** Reference the CSS custom properties defined in
  `global.css` rather than literal hex values in markup.
- **TypeScript strict** is enabled via `astro/tsconfigs/strict`; keep the build
  type-clean.
- **Accessibility is a gate, not a nicety** - semantic landmarks, focus states,
  and WCAG AA contrast (see [Quality gates](#quality-gates)).

## Quality gates

Before merging to `main`, a change should:

- Build clean: `npm run build` (fails on type errors).
- Preview correctly at mobile and desktop widths: `npm run preview`.
- Preserve the zero-JS / no-tracker posture.
- Maintain WCAG 2.1 AA color contrast and keyboard operability.
- Not regress Lighthouse (target: 100 / 100 / 100 / 100 on a static page).

## Browser support

Modern evergreen browsers (last 2 versions of Chrome, Edge, Firefox, Safari) and
mobile Safari/Chrome. No IE11 support.

## Contributing

Internal contributors:

1. Branch from `main` (`feat/...`, `fix/...`, `content/...`).
2. Make the change; run `npm run build` locally.
3. Open a PR with a short description and before/after context for visual changes.
4. Merging to `main` deploys to production - review accordingly.

## License

The site **code** in this repository is available under the terms in
[`LICENSE.txt`](LICENSE.txt) (Creative Commons Attribution 3.0). The **radamic**
name, wordmark, brand assets, and site copy are proprietary and are **not**
covered by that license.
