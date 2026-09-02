# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

This repo is a staging copy of [philoserf/site](https://github.com/philoserf/site). Outside `content/posts/` and the intended differences listed in `README.md`, the two repos should stay identical — when changing layouts, styles, config, or workflows here, keep the port back to `site` in mind, and when `site` changes, mirror it here.

## Commands

All commands use Task (taskfile.yml). Run `task` or `task help` to see available tasks.

## Architecture

- **Config**: hugo.yaml. `enableGitInfo: true` — posts can omit `lastmod` and Hugo falls back to the git commit date; do not disable without auditing every post.
- **Layouts**: hand-rolled, no theme/submodule. `baseof.html` owns the `<main id="main">` element; templates override the `main-class` block (default `prose`).
- **Archetypes**: archetypes/default.md (pages — `lastmod` only) and archetypes/posts.md (posts — `date` only, no `lastmod`); the asymmetry is load-bearing — posts deliberately omit `lastmod` so `enableGitInfo` backfills it from the git commit date
- **Front matter schema**: implicit. Archetypes scaffold `title` + dates + `draft`, but posts in `site` also carry `description`, `tags`, sometimes `aliases`/`series`/`created`. `layouts/partials/description.html` falls back `.Description → .Summary → Site.Params.description`, so a missing `description` degrades gracefully rather than failing.
- **Home page**: `params.home.featured` and `params.home.startHere` are lists of `pageRef` entries resolved by `layouts/partials/resolve-pagerefs.html`, which calls `errorf` on a broken ref — a strict build fails rather than rendering an empty card. These lists reference this repo's own posts, so do not copy `site`'s values verbatim.
- **Shortcodes**: `callout` and `mermaid` mirror `site`, where they are reference templates from the publisher plugin. Mermaid loads from jsDelivr with an SRI hash via `layouts/partials/mermaid.html`, conditionally on `.HasShortcode "mermaid"` — bumping the pinned version requires recomputing the `integrity` hash.
- **Generated text files**: `robots.txt`, `site.webmanifest`, `vcard.vcf`, `llms.txt` render from `layouts/` templates via custom output formats on the home page — do not re-create them under `static/`
- **Theme colors**: `params.themeColor.light` / `.dark` in hugo.yaml feed the `theme-color` metas and the webmanifest; they must match `--background-color` in `static/style.css`.

## Content

Posts under `content/posts/` are hand-written here and safe to edit — unlike `site`, where they are publisher output. Keep the set small; it exists so the templates have something to render.

## Testing

There is no end-to-end test. The strict Hugo build (`--panicOnWarning` in `task check`, `build.yml`, and `deploy.yml`) catches missing shortcodes, broken refs, and template errors; anything subtler is caught by visual review.

The deploy workflow also runs `lychee` over `public/` (external links and anchors), non-blocking (`continue-on-error`).

## Deployment

Automated to GitHub Pages via GitHub Actions (`.github/workflows/deploy.yml`). Builds with `hugo --gc --minify --panicOnWarning`; local `task build` and PR builds use the same strict flags. The build job is guarded by `github.repository == 'philoserf/sandbox'` so forks don't deploy.

## Notes

- New content starts as `draft: true` (see archetypes); the dev server includes drafts via `-D`, production does not.
- `static/CNAME.pending` is parked, not active — `philoserf.com` points at `site`.
