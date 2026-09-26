# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A static, SEO-driven content site for **www.toiletinjapan.com** (see `CNAME`, so it's served by GitHub Pages from the repo root on `main`). The site is a set of travel guides about public toilets in Japan. It exists to funnel visitors to the **Sugu Toire** Android app (`com.tomat.sugutoire`) on Google Play.

There is no build system, package manager, framework, linter or test suite. Every page is a single hand-written `.html` file with its CSS in an inline `<style>` block. Nothing is shared between files. Pushing to `main` deploys.

To preview locally, run `python3 -m http.server 8000` in the repo root and open http://localhost:8000. Pages use root-relative favicon paths (`/favicon.ico`), so opening files directly with `file://` won't load the favicons.

## Page structure and conventions

- `index.html` is the hub. It has a hero section, a list of `.post-card` links to each guide, an SEO text block, and an `.app-card` Play Store CTA. Its JSON-LD is `WebSite`.
- Every other `*.html` page is an article that follows the same template:
  - a `← Back to Guides` header link to `index.html`
  - a hero image
  - `<article>` body
  - an `.app-card` CTA with a `.btn` linking to the Play Store (`#ff002b` red pill button)
  - a "Related Guides" list
  - a footer
- Article JSON-LD is `Article`, with author "Tomasz Matusik" and publisher "Sugu Toire" (logo at `/logo.png`).
- Styling is duplicated in each page. The same `:root` CSS variables (`--primary-color: #4A90E2`, `--secondary-color: #2b2d42`, etc.) and class names (`.container`, `.phrase-card`, `.tip-box`, `.jp-text`, `.romaji`, `.translation`, `.app-card`, `.btn`) are re-declared per file. A style change meant to be sitewide has to be made in every file.
- Images are hotlinked from Unsplash (`images.unsplash.com/...&w=1200` for heroes/OG, `&w=400` for thumbnails). Only favicons and `logo.png` are stored locally.
- Japanese phrases use the pattern `.jp-text` (kana/kanji) → `.romaji` → `.translation`.

## Adding or renaming a page

Several files have to be kept in sync by hand:
1. Create the page from an existing article, e.g. `last-resort-toilet.html`. Update these to match the new filename/URL: `<title>`, meta description, `<link rel="canonical">`, `og:*`/`twitter:*` tags and JSON-LD (`headline`, `mainEntityOfPage.@id`, dates).
2. Add a `.post-card` to `index.html`.
3. Add a `<url>` entry to `sitemap.xml`.
4. Add cross-links in the "Related Guides" lists of the other articles and in `404.html`.

## Other files

`404.html` is served by GitHub Pages for any missing path, including nested ones, so its links must be root-relative (`/toilet-japan.html`). It is `noindex` and stays out of the sitemap.

`_config.yml` excludes `CLAUDE.md` from the GitHub Pages (Jekyll) build so it isn't published. Add any other non-site files there too. A `.nojekyll` file would turn off Jekyll, and with it this exclude.

`google99ec3fbe524bd037.html` is a Google Search Console verification file. Don't modify or delete it.
