# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

`시니어독 건강백서` (Senior Dog Health Report) — a static Korean-language blog about caring for small senior dogs. It's a plain HTML/CSS site with no build step, served as-is by **GitHub Pages** at `seniordog.kr` (see `CNAME`). There is no `package.json`, no bundler, no framework, and no test suite — every page is hand-authored HTML.

The same articles are also cross-posted, in a different format, to a Naver Blog (`blog.naver.com/seniordog_report`). Naver-side drafts live in `_naver/`, which is deliberately excluded from the published site (GitHub Pages / Jekyll skips underscore-prefixed folders) — never move files out of `_naver/` or rename it.

## Commands

There is no build, lint, or test tooling in this repo. To preview locally, just serve the directory statically, e.g.:

```
python3 -m http.server 8000
```

Changes are published simply by committing and pushing to the default branch (GitHub Pages serves directly from the repo).

## Site structure

- `index.html` — homepage: intro, category index, and a **POSTS** list of recent articles (newest first). Every new post must be added here.
- Five category listing pages, each with its own `<article>`/post-list: `health.html` (건강관리), `nutrition.html` (영양·사료), `gear.html` (용품리뷰), `cost.html` (병원·비용가이드), `log.html` (집사일지).
- `posts/NN-slug.html` — individual articles, numbered sequentially (`01-...` through `05-...` currently). Images for post `NN` live in `posts/images/NN/`.
- `style.css` — single shared stylesheet for the whole site (paper/ledger aesthetic). Color tokens are CSS custom properties (`--paper`, `--pine`, `--brass`, `--rule`, etc.) with a dark-mode override block (`prefers-color-scheme: dark`) — reuse existing tokens rather than hardcoding colors.
- `sitemap.xml`, `robots.txt` — must be updated whenever a post is added/removed.
- `_naver/` — not published. Contains:
  - `AGENT_INSTRUCTIONS.md` — the authoritative, detailed runbook for the automated weekly publishing pipeline (topic selection, affiliate link handling, dual-format authoring, infographic rules, commit conventions). **Read this file before doing any content-publishing work** — it is more detailed than this summary and takes precedence for that workflow.
  - `블로그포스팅/NN_주제명/` — Naver-format draft of each post, plus its own `images/`.
  - `쿠팡파트너스_링크.txt` — a notepad-style file where the human fills in Coupang Partners affiliate product names/links per post slot before the agent is allowed to write that post's content.

## Content model & conventions

Every published page (home, category, post) shares the same shell: `<head>` meta/OG/canonical tags → inline SVG brandmark header with nav (current page gets `class="current"`) → `<div class="sheet">` content → footer. Copy an existing file of the same kind as a template rather than building the shell from scratch — do not restyle or redesign the shared header/footer/logo, since brand identity is explicitly out of scope for routine content changes.

Article pages (`posts/NN-*.html`) additionally follow this fixed shape, in order:
1. Full meta block: title, description, canonical, OG tags (including `og:image`), `article:section`, and a `application/ld+json` `Article` schema block.
2. `.summary-box` (📌 3-line TL;DR) and `.toc-box` (🔍 table of contents) right after the intro paragraph.
3. Body sections using `<h2>`/`<h3>`, `<table>` for comparisons (with `<tr class="highlight">` for the row to emphasize), and `<figure class="post-figure">` for images.
4. `.product-card` blocks for each affiliate product (image + `.name` generic category label + `.desc` + `.buy-link` with the actual product name as link text), followed by a single `.affiliate-disclosure` notice.
5. FAQ section using `.faq-item` blocks.
6. A "다음 글" (next post) paragraph and a `.tags` line of hashtags.

Tone: direct and assertive — avoid hedging phrases like "~일 수도 있어요". The one exception is genuine medical diagnosis claims, where deferring to "정확한 진단은 병원에서" phrasing is correct, not hedging.

## The publishing pipeline (when asked to write/publish a new post)

Full details are in `_naver/AGENT_INSTRUCTIONS.md`; the essentials:

- A topic only gets written up once its slot in `_naver/쿠팡파트너스_링크.txt` has both product name(s) and Coupang link(s) filled in by the human. If a slot is empty, stop and prepare topic *candidates* only — never pick one unilaterally, and never write article content for an unfilled slot.
- Every table in a post needs a matching custom infographic (SVG rendered to PNG via `sharp`, ≥300 density), saved into **both** `posts/images/NN/` and `_naver/블로그포스팅/NN_.../images/`. Vary the visual format between posts (donut/step/card/icon-grid, etc.) — check the last few posts' images to avoid repeating a format. `sharp` isn't a repo dependency; install it in a scratch directory (e.g. `/tmp/render/`) and never commit `.svg` or rendering scripts, only the final PNGs.
- Lifestyle photos come from Pexels (no attribution required) using specific search phrases (breed/size + concrete situation, not just "dog").
- The homepage-format post and the Naver-format post are both written from existing numbered posts as templates, but Naver HTML has real constraints: no `<style>` tag (ignored by Naver's editor — use inline styles/explicit `<br>`), no `<ul>/<li>` (use `&middot;` + `<br><br>`), explicit blank lines between blocks, and Coupang product images represented as a `.photo-slot` text placeholder rather than an `<img>` tag.
- After publishing a new post NN: update the *previous* post's "다음 글" section to link to it (the new post's own "다음 글" must stay neutral/unresolved), and add it to its category page, `index.html`'s POSTS list, and `sitemap.xml`.
- Commit messages are Korean, terse/technical register (not polite form). Commit and push after each unit of work (`git add -A && git commit && git push`).
