# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Personal blog ("Curiouser²") by Andrew Buckingham, built with **Hexo 6.3** static site generator and deployed to GitHub Pages via GitHub Actions.

## Commands

- `npm run server` — Local dev server (default: http://localhost:4000)
- `npm run build` — Generate static site into `public/`
- `npm run clean` — Remove generated files (`public/`, `db.json`)
- `npx hexo new "Post Title"` — Create a new post in `source/_posts/`
- `npx hexo new draft "Title"` — Create a new draft in `source/_drafts/`

There are no tests or linting configured.

## Architecture

- **Static site generator**: Hexo, configured in `_config.yml`
- **Theme**: `ascent` — a custom fork at `themes/ascent/`, pulled in as a git submodule from `andrewbuckingham/hexo-theme-ascent` (branch `feature/google-tag-manager`). Theme-specific overrides go in `_config.ascent.yml`.
- **Deployment**: On push to `main`, GitHub Actions (`.github/workflows/pages.yml`) runs `npm install` + `npm run build` and deploys `public/` to `gh-pages` branch via `peaceiris/actions-gh-pages`.

## Content

- Posts live in `source/_posts/` as Markdown files with YAML front matter (`title`, `date`, `categories`, `tags`).
- `post_asset_folder: true` is enabled — each post can have a companion folder (same name as the post) for images and assets.
- Scaffolds in `scaffolds/` define front matter templates for posts, pages, and drafts.
- Categories used: `home-automation`, `stem`, `software-development`.
- Mermaid diagrams are enabled (`hexo-mermaid-diagrams`).
- The `hexo-sliding-spoiler` plugin is available for collapsible content.

## Key Config Notes

- Permalinks follow `:year/:month/:day/:title/` format.
- Post asset references use `marked.postAsset: true` so you can reference images with just the filename (e.g., `{% asset_img image.png "alt text" %}`).
