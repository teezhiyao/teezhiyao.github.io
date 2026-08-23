# Tee Zhi Yao Personal Site

Source for `https://teezhiyao.github.io`.

This is a Hugo site using the Toha theme through Hugo Modules. Most homepage content is driven by YAML files under `data/en/sections`, while longer writing lives under `content/posts` and `content/notes`.

## Requirements

- Hugo extended `0.146.5` or newer
- Go `1.22` or newer
- Node.js `22` or newer

The GitHub Pages workflow pins Hugo `0.146.5` and Node.js `22`, so use those versions locally when possible.

## Common Commands

This repo can use the project-local Hugo binary at `.tools/hugo/hugo.exe`.

Install or refresh the repo-local Hugo version from Git Bash:

```bash
bash scripts/install-hugo
```

Run Hugo from Git Bash:

```bash
bash scripts/hugo version
bash scripts/hugo server
```

From PowerShell, run:

```powershell
.\.tools\hugo\hugo.exe version
.\.tools\hugo\hugo.exe server
```

If using a globally installed Hugo instead, run the site locally:

```powershell
hugo server
```

Build the site:

```powershell
hugo --minify
```

If Hugo has trouble writing its module cache on Windows, use a writable cache directory:

```powershell
$env:HUGO_CACHEDIR='C:\Users\zhiya\AppData\Local\Temp\hugo-cache'
hugo --minify
```

## Editing Guide

- Profile, hero text, contact info, and personal summary: `data/en/author.yaml`
- Site metadata: `data/en/site.yaml`
- Homepage sections: `data/en/sections/*.yaml`
- New longform posts: `content/posts/*.md`
- Archive notes and older writeups: `content/notes/*.md`
- Custom section/card layouts: `layouts/partials/**`
- Static images and files: `static/assets/**`

Files under `static/` are served from the site root. For example:

- `static/assets/predix-header.png` is available at `/assets/predix-header.png`
- `static/assets/sections/author.jpg` is available at `/assets/sections/author.jpg`

## Homepage Sections

The homepage sections are controlled by YAML files under `data/en/sections`. Section order comes from `section.weight`, and navbar visibility comes from `section.showOnNavbar`.

Currently active sections include About, Experience, Education, Now, Building, Past Projects, Certifications, and Featured Posts.

## Deployment

Deployment runs through GitHub Actions in `.github/workflows/deploy-site.yaml`.

The workflow runs on pushes to `main` and manual dispatch. It installs Hugo, resolves Hugo modules, installs generated npm dependencies, builds the site into `public`, and deploys to GitHub Pages.

## Extra Context

See `AGENTS.md` for a more detailed repo map and maintenance notes for coding agents.

## Repository Hygiene

Line endings and editor defaults are managed by `.gitattributes` and `.editorconfig`. Text files should stay UTF-8 with LF line endings, while images, fonts, and PDFs are treated as binary files.
