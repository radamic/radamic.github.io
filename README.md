# radamic.github.io

Marketing site for [radamic](https://radamic.com), built with [Astro](https://astro.build) and Tailwind v4.

## development

```bash
npm install
npm run dev      # http://localhost:4321
npm run build    # ./dist
npm run preview  # preview prod build
```

## deploy

Pushes to `main` are built and deployed to GitHub Pages by `.github/workflows/deploy.yml`. The custom domain `radamic.com` is configured via `public/CNAME`.

## structure

```
src/
  layouts/Layout.astro       page shell (head, fonts, meta)
  components/                hero, about, capabilities, work, contact, header, footer
  pages/index.astro          single-page composition
  styles/global.css          tailwind v4 + theme tokens
public/
  CNAME                      custom domain pin
  favicon.svg
```
