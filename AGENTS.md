# Agent Instructions

This repo is the source for `https://teezhiyao.github.io`, a Hugo personal site using the Toha theme through Hugo Modules.

## Stack And Commands

- Static site generator: Hugo extended.
- Theme: `github.com/hugo-toha/toha/v4` at `v4.14.0` from `go.mod`.
- Deployment: GitHub Pages via `.github/workflows/deploy-site.yaml`.
- Main config: `hugo.yaml`.
- Primary branch: `main`.
- Local Hugo helper scripts: `scripts/install-hugo` and `scripts/hugo`.
- Project-local Hugo binary, when installed: `.tools/hugo/hugo.exe`.

Common local commands:

```powershell
.\.tools\hugo\hugo.exe version
.\.tools\hugo\hugo.exe server
.\.tools\hugo\hugo.exe --minify
```

If Hugo has trouble with the module cache on Windows, use a writable cache directory:

```powershell
$env:HUGO_CACHEDIR='C:\Users\zhiya\AppData\Local\Temp\hugo-cache'
.\.tools\hugo\hugo.exe --minify
```

In the Codex sandbox, plain `git status` may fail with a dubious ownership warning. Use:

```powershell
git -c safe.directory=C:/Users/zhiya/Documents/GitHub/teezhiyao.github.io status --short
```

Do not globally modify Git config unless the user explicitly asks.

## Editing Map

- Site config, Toha feature flags, menus, permalinks, analytics, and markup settings: `hugo.yaml`.
- Site metadata such as title, tagline, copyright, and date format: `data/en/site.yaml`.
- Profile, hero text, contact info, and personal summary: `data/en/author.yaml`.
- Homepage sections and their ordering/navbar visibility: `data/en/sections/*.yaml`.
- Longform posts: `content/posts/*.md`.
- Archive notes and older writeups: `content/notes/*.md`.
- Local Toha partial overrides and custom layouts: `layouts/partials/**`.
- Static images and files served directly from the site root: `static/assets/**`.
- Resume source and fonts: `_resume/**`.

For ordinary content edits, prefer YAML or Markdown data files. Only edit `layouts/partials/**` when changing rendered structure or custom card/section layouts.

## Homepage Model

Homepage sections are data-driven from `data/en/sections/*.yaml`. Each section usually defines `section.id`, `section.name`, `section.enable`, `section.weight`, `section.showOnNavbar`, and `section.template`, plus section-specific data.

Current section files:

- `about.yaml`
- `accomplishments.yaml`
- `duolingo-streak.yaml`
- `education.yaml`
- `experiences.yaml`
- `featured-posts.yaml`
- `guiding-principles.yaml`
- `now.yaml`
- `past-projects.yaml`
- `projects.yaml`

Section order is controlled by `section.weight`.

## Content And Assets

Permalinks are configured in `hugo.yaml`:

- Posts: `/posts/:slug/`
- Notes: `/notes/:slug/`

Current post topics include OMSCS reflections/course notes, Appium/mobile testing, Hermes Agent and Obsidian setup, races/travel posts, guiding principles, and project writeups.

Current archive notes include AWS Cloud Practitioner certification, CDDC 2019, Hanyang exchange, ADSC internship, and Predix Hackathon 2018.

Hugo serves files under `static/` from the site root. Examples:

- `static/assets/predix-header.png` becomes `/assets/predix-header.png`.
- `static/assets/CDDC2019.jpg` becomes `/assets/CDDC2019.jpg`.
- `static/assets/sections/author.jpg` becomes `/assets/sections/author.jpg`.

There are also author images under `static/images/sections/author.jpg` and `assets/images/sections/author.jpg`. The author profile image in `data/en/author.yaml` is `images/sections/author.jpg`, which maps to the static image path.

## Build And Deploy

GitHub Actions deploys the site on push to `main` or manual workflow dispatch.

Workflow summary:

1. Checkout repo.
2. Configure GitHub Pages.
3. Install Hugo `0.146.5` extended.
4. Install Node.js `22`.
5. Run `hugo mod tidy`.
6. Run `hugo mod npm pack`.
7. Run `npm install`.
8. Run `hugo --minify --baseURL "${{ steps.pages.outputs.base_url }}/"`.
9. Upload `public`.
10. Deploy to GitHub Pages.

## Generated Files And Gotchas

- `.tools/`, `public/`, `resources/_gen/`, `hugo_stats.json`, `assets/jsconfig.json`, `node_modules/`, `package.json`, `package-lock.json`, and `package.hugo.json` are ignored.
- `assets/jsconfig.json` can contain machine-specific Hugo module cache paths.
- Use Hugo `0.146.5` or newer. Toha `v4.14.0` requires Hugo `0.146.0` or newer.
- Analytics is configured under `params.features.analytics` in `hugo.yaml`; Toha emits analytics only for production builds.
