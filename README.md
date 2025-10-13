# Codettes Bootcamp 2022 — Portfolio Page

This repository contains a small static portfolio page created for the Codettes Bootcamp 2022. The primary content is in the `page` folder; the portfolio's main Markdown file is `page/index.md`.

What this repo contains
- `page/index.md` — the portfolio page written in Markdown. This is the page you completed for the bootcamp.
- `page/_config.yml` — (if present) configuration for static site generators like Jekyll.
- `page/image/` — images and assets used by the portfolio page.

How to preview

1. Preview on GitHub Pages

- If this repository is published with GitHub Pages (the `gh-pages` branch is commonly used), the `page/index.md` will be rendered as part of the site automatically. Check your repository settings -> Pages to confirm the site URL.

2. Preview locally with Jekyll (recommended when `_config.yml` exists)

Prerequisites: Ruby and Bundler installed. Then from the repository root:

```bash
# install bundler and jekyll if you don't have them
gem install bundler jekyll

# if there's a Gemfile, install dependencies
bundle install || true

# serve the site (from repo root)
jekyll serve --source page --destination _site --watch

# open http://127.0.0.1:4000 in your browser
```

3. Preview the Markdown directly

- Many editors (VS Code, Typora, etc.) provide a Markdown preview. Open `page/index.md` and use the editor's preview.

Notes and suggestions
- If you want the page to appear at the repository root on GitHub Pages, consider moving the contents of `page/` to the repository root or configure Pages to use the `page/` folder.
- Fix any typos in the Markdown content (e.g., "protfolio" -> "portfolio", "coddets" -> "Codettes").
- Add a short author section and contact details to `page/index.md` if you'd like future viewers to reach out.

If you'd like, I can:
- open and fix small typos in `page/index.md` and create a commit
- add a simple GitHub Pages configuration
- create a short bio section in the portfolio

Last updated: 2025-10-13
