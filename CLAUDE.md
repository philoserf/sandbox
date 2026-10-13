# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

This repo is a staging copy of [philoserf/site](https://github.com/philoserf/site). Outside `content/posts/` and the intended differences listed in `README.md`, the two repos should stay identical — when changing layouts, styles, config, or workflows here, keep the port back to `site` in mind, and when `site` changes, mirror it here.

## Commands

All commands use Task (Taskfile.yml). Run `task` or `task help` to see available tasks.

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

All prose in this repo is placeholder: Old English word-salad, composed from a real vocabulary rather than quoted from any surviving text, standing in for `site`'s writing. That covers `content/` and the identity params in `hugo.yaml` (`title`, `author`, `description`, `email`, `motto`, `home`, `contact`, `social`). Do not replace it with real content, and do not copy `site`'s prose back over it — the point is to see layout and typography, not to read.

`params.contact` is fabricated on purpose. This repo publishes a vCard at `/vcard.vcf`; `site` deliberately puts real PII there, and a public sandbox must not. Keep the invented address, the 555-01xx phone, and the `example.com` addresses.

`layouts/` stays in English. Template chrome is UI, not content, and byte-identical templates are what make this repo useful for trying layout changes before porting them to `site`.

The prose is placeholder but the shapes are not: `content/` holds one example of every kind of page and post `site` has, so a change tried here meets everything it will meet there. Today that is the home page, About, Now and Uses (Now and Uses with `showUpdated`), both books pages (`showUpdated`, an `/posts/` alias, linked from Now by `relref`), posts with `math`, `aliases`, a footnote, tables and block quotes, and one future-dated post (`wyrd-on-ecge`), which a default build leaves out until a scheduled rebuild passes its date. When `site` gains a new kind (a frontmatter key, a shortcode, a page), add a placeholder example here. When the future-dated post's date passes, move it forward again.

Posts under `content/posts/` are generated, not publisher output, and are safe to regenerate, edit, add, or delete — but `params.home.featured` and `startHere` reference them by slug, so renaming or removing one means updating `hugo.yaml` or the strict build fails.

## Testing

There is no end-to-end test. The strict Hugo build (`--panicOnWarning` in `task check` and in `wrangler.jsonc`'s `build.command`, which every Workers Build runs) catches missing shortcodes, broken refs, and template errors; anything subtler is caught by visual review.

`task links` runs `lychee` over `public/` (external links and anchors). It is not part of any deploy.

## Deployment

A Cloudflare Worker at `sandbox.philoserf.com`, deployed by Workers Builds on every push to `main` (other branches get preview uploads). `wrangler.jsonc`'s `build.command` is the whole build, the same on a Mac (`task deploy`) and in the build image, so change the build there and nowhere else. Workers Builds clones shallow, and `enableGitInfo` then dates every post to the head commit, so `build.command` fetches full history before Hugo runs. A run of identical post `lastmod` dates in `sitemap.xml` means that step stopped working. A Monday 05:00 deploy-hook call from `almanac`'s `~/.config/refresh.zsh` rebuilds for future-dated posts. This hosting is the rehearsal for site's move ([#16](https://github.com/philoserf/sandbox/issues/16)).

## Notes

- New content starts as `draft: true` (see archetypes); the dev server includes drafts via `-D`, production does not.
