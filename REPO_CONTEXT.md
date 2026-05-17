# Personal Site Repo Context

This repo is the source for `https://teezhiyao.github.io`, a Hugo personal site using the Toha theme through Hugo Modules.

## High-Level Stack

- Static site generator: Hugo
- Theme: `github.com/hugo-toha/toha/v4`
- Theme version: `v4.14.0` from `go.mod`
- Deployment: GitHub Pages via GitHub Actions
- Main config: `hugo.yaml`
- Primary branch: `main`

## Important Files

- `hugo.yaml` - site config, theme/module config, menus, permalinks, Toha params, markup settings.
- `go.mod` / `go.sum` - Hugo module dependency on Toha.
- `.github/workflows/deploy-site.yaml` - GitHub Pages build and deploy pipeline.
- `data/en/site.yaml` - site metadata such as title, tagline, copyright, date format.
- `data/en/author.yaml` - hero/profile content, contact info, rotating title text, summary.
- `data/en/sections/*.yaml` - homepage section data and enable/disable state.
- `content/posts/*.md` - main posts.
- `content/notes/*.md` - archive notes.
- `layouts/partials/**` - local Toha partial overrides/custom section/card templates.
- `static/assets/**` - static images and logos served directly from the site root.
- `_resume/**` - LaTeX resume source and fonts.

## Homepage Content Model

The homepage is mostly data-driven from `data/en/sections/*.yaml`. Each section file usually contains:

- `section.id`
- `section.name`
- `section.enable`
- `section.weight`
- `section.showOnNavbar`
- `section.template`
- Section-specific content such as `experiences`, `degrees`, `projects`, or `accomplishments`

Section order is controlled by `section.weight`.

Currently active homepage sections:

- `about` - `data/en/sections/about.yaml`
- `experiences` - `data/en/sections/experiences.yaml`
- `education` - `data/en/sections/education.yaml`
- `now` - `data/en/sections/now.yaml`
- `projects` / "Building" - `data/en/sections/projects.yaml`
- `past-projects` - `data/en/sections/past-projects.yaml`
- `accomplishments` / "Certifications" - `data/en/sections/accomplishments.yaml`
- `featured-posts` - `data/en/sections/featured-posts.yaml`

Disabled draft section files were removed to keep the section directory focused.

## Content Sections

`content/posts/` is for current longform posts. There is currently one main post:

- `content/posts/hermes-agent-obsidian-setup.md`

`content/notes/` is used as an archive. Current note topics include:

- AWS Cloud Practitioner certification
- CDDC 2019
- Hanyang exchange
- ADSC internship
- Predix Hackathon 2018

Permalinks are configured in `hugo.yaml`:

- Posts: `/posts/:slug/`
- Notes: `/notes/:slug/`

## Local Template Overrides

The site mostly relies on Toha defaults, with local overrides/custom partials for selected sections and cards:

- `layouts/partials/sections/past-projects.html`
- `layouts/partials/cards/past-project.html`
- `layouts/partials/cards/accomplishments.html`

Use these when changing the rendered layout of custom sections. For ordinary content edits, prefer the YAML files under `data/en/sections`.

## Assets

Static assets live mostly under `static/assets`. Hugo serves files from `static/` at the site root, so:

- `static/assets/predix-header.png` becomes `/assets/predix-header.png`
- `static/assets/CDDC2019.jpg` becomes `/assets/CDDC2019.jpg`
- `static/assets/sections/author.jpg` becomes `/assets/sections/author.jpg`

There are also images under:

- `static/images/sections/author.jpg`
- `assets/images/sections/author.jpg`

The author profile image in `data/en/author.yaml` is `images/sections/author.jpg`, which maps to the static image path.

## Build And Deploy

GitHub Actions deploys the site on push to `main` or manual workflow dispatch.

Workflow: `.github/workflows/deploy-site.yaml`

Build pipeline:

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

## Local Build Notes

Local Hugo observed during context capture:

- `hugo v0.126.1`

Toha `v4.14.0` requires Hugo `0.146.0` or newer. A local build with Hugo `0.126.1` fails inside the Toha theme templates. CI should still be the source of truth because it pins Hugo `0.146.5`.

Recommended local build command once Hugo is updated:

```powershell
hugo --minify
```

If Hugo module cache permissions are an issue on Windows, set a writable cache directory:

```powershell
$env:HUGO_CACHEDIR='C:\Users\zhiya\AppData\Local\Temp\hugo-cache'
hugo --minify
```

## Git Notes

In the Codex sandbox, plain `git status` may fail with a dubious ownership warning because the repo is owned by the normal Windows user while commands run as the sandbox user.

Use a local one-command override for inspection:

```powershell
git -c safe.directory=C:/Users/zhiya/Documents/GitHub/teezhiyao.github.io status --short
```

Do not globally modify Git config unless the user explicitly asks.

## Known Gotchas

- `assets/jsconfig.json` can be generated with machine-specific Hugo module cache paths and is ignored.
- `package.hugo.json` is listed in `.gitignore`, but exists in the repo. It may be intentional from Hugo module packing, but avoid touching it unless needed.
- Generated build outputs such as `public/`, `resources/_gen/`, `hugo_stats.json`, `node_modules/`, `package.json`, and `package-lock.json` are ignored.

## Editing Guidance

- For profile, hero, contact, or summary changes, edit `data/en/author.yaml`.
- For homepage sections, edit the matching file under `data/en/sections`.
- For section order or navbar visibility, change `section.weight` and `section.showOnNavbar`.
- For new posts, add Markdown under `content/posts`.
- For archive/history notes, add Markdown under `content/notes`.
- For visual layout changes to custom cards/sections, edit `layouts/partials`.
- For static images, place files under `static/assets` and reference them as `/assets/<filename>`.
