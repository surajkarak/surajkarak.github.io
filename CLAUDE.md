# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

**Local development (preferred):**
```bash
bundle exec jekyll serve --livereload
```
Serves at `http://localhost:4000`. Use `--drafts` to also render posts in `_drafts/`.

Ruby version is pinned to 3.2.11 via `.ruby-version` — `rbenv` (recommended) will pick this up automatically. First-time setup on a fresh machine: `brew install rbenv ruby-build imagemagick && rbenv install 3.2.11 && gem install bundler && bundle install`. (Pinned to 3.2 because Jekyll 4.3.2 has a `Logger` incompatibility with Ruby 3.3+.)

**Docker alternative:**
```bash
docker-compose up
```
Serves at `http://localhost:8080` using the `amirpourmand/al-folio` image.

**Production build:**
```bash
JEKYLL_ENV=production bundle exec jekyll build --lsi
```

**Deploy to GitHub Pages** (interactive, requires clean working tree):
```bash
bin/deploy
```
This builds, purges unused CSS via PurgeCSS, then force-pushes `_site/` contents to the `gh-pages` branch.

**Install dependencies:**
```bash
bundle install
```

## Architecture

This is a [Jekyll](https://jekyllrb.com/) site using the [al-folio](https://github.com/alshedivat/al-folio) academic/portfolio theme.

### Key directories

| Path | Purpose |
|---|---|
| `_config.yml` | All site-wide settings — social links, feature flags, plugin config |
| `_pages/` | Top-level pages (about, blog, cv, projects, contact, repositories) |
| `_posts/` | Blog posts, named `YYYY-MM-DD-slug.md` |
| `_projects/` | Project write-ups; frontmatter controls display order (`importance`) and category (`work`, `tinkering`, `fun`) |
| `_data/` | Structured data files (e.g. CV data supplements `assets/json/resume.json`) |
| `_bibliography/papers.bib` | BibTeX entries; only items with `selected=true` appear on the publications page |
| `_layouts/` | Page templates (`default`, `about`, `post`, `page`, `cv`, `distill`, `bib`) |
| `_includes/` | Reusable partials (`head.html`, `header.html`, `footer.html`, `social.html`, etc.) |
| `_sass/` | SCSS source; CSS variables for theming are in `_sass/_base.scss` |
| `assets/json/resume.json` | JSON Resume format; drives the `/cv` page via `jekyll-get-json` |
| `assets/img/` | Images; jekyll-imagemagick auto-generates responsive WebP variants at 480/800/1400px |

### Content collections

- **`_projects/`** — each file's frontmatter sets `importance` (integer, lower = higher priority), `category`, and optionally `featured: true` to appear on the about page.
- **`_posts/`** — standard Jekyll posts. Tags and categories declared in frontmatter appear in the blog sidebar and archive pages.
- **`_news/`** — short announcements shown in the scrollable news feed on the about page.

### Feature flags

All optional features are toggled in `_config.yml` under the "Optional Features" section — including dark mode, analytics, Open Graph meta, masonry layout, MathJax, and progress bar. No code changes needed to enable/disable these; flip the boolean.

### CV / Resume data flow

`assets/json/resume.json` is loaded at build time by `jekyll-get-json` and exposed as `site.data.resume`. The `_layouts/cv.html` and `_includes/resume/` partials render it. To add a CV PDF download, set `cv_pdf: filename.pdf` in `_pages/cv.md` frontmatter and place the file in `assets/pdf/`.

### Navigation

Pages opt into the navbar by setting `nav: true` and `nav_order: N` in their frontmatter. Social icons in the navbar are controlled by `enable_navbar_social` in `_config.yml`.

### Theming

Light/dark mode uses CSS custom properties defined in `_sass/_base.scss`. The active theme class is toggled on `<html>`. Always use `var(--global-*)` variables rather than hardcoded colours when editing templates or inline styles.

## Pre-commit hooks

Configured via `.pre-commit-config.yaml`: trailing whitespace, end-of-file newline, YAML syntax check, and large-file guard. Run `pre-commit install` once after cloning to activate them.
