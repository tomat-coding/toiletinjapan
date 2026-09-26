# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A static, SEO-driven content site for **www.toiletinjapan.com** (see `CNAME`, so it's served by GitHub Pages from the repo root on `main`). The site is a set of travel guides about public toilets in Japan. It exists to funnel visitors to the **Sugu Toire** Android app (`com.tomat.sugutoire`) on Google Play.

There is no build system, package manager, framework, linter or test suite. Every page is a single hand-written `.html` file that links the shared `/styles.css`. Pushing to `main` deploys.

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
- Each article has two JSON-LD blocks:
  - `Article`, with author "Tomasz Matusik", publisher "Sugu Toire" (logo at `/logo.png`), `image` set to the share image, and `dateModified`
  - `BreadcrumbList` (Home → article)
- A visible `<p class="updated">Updated <time datetime="…">…</time></p>` sits at the top of each article. Keep it in sync with `dateModified` when content changes.
- **Styling:** `styles.css` holds the CSS variables, header/footer, `.btn`, and the article template (hero overlay, typography, `.tip-box`/`.warning-box`/`.phrase-card`, `.jp-text`/`.romaji`/`.translation`, `.app-card`). Make sitewide changes there.
  - Article typography uses `:where(article) h2` etc., so it stays at element specificity and a page's plain `h2 {}` rule can still override it.
  - Pages that differ keep a `<style>` block after the `<link>` with only their overrides: `index.html` (hub layout), `toilet-japan.html` (its own design: sticky header, hero without overlay, larger type) and `404.html`. The other articles have no inline CSS.
  - When a page overrides a shared rule, it must also reset any shared properties it doesn't want (e.g. `border-bottom: none` on `header`).
- Most images are hotlinked from Unsplash (one from Pexels). Photos that aren't on Unsplash are cropped to the standard sizes and stored in `images/` as `<subject>-<w>x<h>.jpg`, e.g. the Wikimedia Commons photo used by `tokyo-toilet.html`.
  - CC BY-SA photos need a visible `.photo-credit` line: author, a link to the source file, the license link, and "cropped" if the photo was cropped.
  - Always request an explicit crop (`fit=crop&w=…&h=…`) and put matching `width`/`height` attributes on the `<img>`.
  - Sizes: heroes 1200×600 with `fetchpriority="high"`; `.content-img` 800×533 with `loading="lazy"`; homepage `.post-thumb` 400×300 with `loading="lazy"`; `og:image`/`twitter:image` 1200×630.
  - Alt text should describe what the photo actually shows. Check the image rather than guessing from the surrounding text.
- Japanese phrases use the pattern `.jp-text` (kana/kanji) → `.romaji` → `.translation`.

## Adding or renaming a page

Several files have to be kept in sync by hand:
1. Create the page from an existing template article, e.g. `last-resort-toilet.html`, which uses only `styles.css`. Update these to match the new filename/URL: `<title>`, meta description, `<link rel="canonical">`, `og:*`/`twitter:*` tags and JSON-LD (`headline`, `mainEntityOfPage.@id`, dates).
2. Add a `.post-card` to `index.html`.
3. Add a `<url>` entry to `sitemap.xml`.
4. Add cross-links in the "Related Guides" lists of the other articles and in `404.html`.
5. Tag the page's Play Store link with its own campaign, so Play Console shows installs per page:
   `https://play.google.com/store/apps/details?id=com.tomat.sugutoire&amp;referrer=utm_source%3Dtoiletinjapan%26utm_medium%3Dwebsite%26utm_campaign%3D<filename-without-.html>`

## Other files

`404.html` is served by GitHub Pages for any missing path, including nested ones, so its links must be root-relative (`/toilet-japan.html`). It is `noindex` and stays out of the sitemap.

`_config.yml` excludes `CLAUDE.md` from the GitHub Pages (Jekyll) build so it isn't published. Add any other non-site files there too. A `.nojekyll` file would turn off Jekyll, and with it this exclude.

`google99ec3fbe524bd037.html` is a Google Search Console verification file. Don't modify or delete it.
