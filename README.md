# sandbox

A staging copy of [philoserf/site](https://github.com/philoserf/site) — same layouts, styles, config, and workflows, with throwaway posts. Changes to the rendering half of the site are tried here first, then ported to `site`. Deployed to [philoserf.github.io/sandbox](https://philoserf.github.io/sandbox/). **Site motto:** COGITA·DISCE·NECTE·ENUNTIA (Think, Learn, Connect, Articulate).

## What this repo is

`site` is the rendering half of a two-repo writing pipeline: the author writes in an Obsidian vault and a publisher plugin pushes notes into `content/posts/` there, so those posts are read-only output. This repo has no publisher attached. Posts under `content/posts/` are hand-written scaffolding — enough content for the templates to have something to render — and are safe to edit, add, and delete.

Everything else should stay a faithful mirror of `site`. The intended differences are:

- `baseURL` and the `github.repository` guard in `deploy.yml` (this repo, not `philoserf.com`)
- `static/CNAME.pending` instead of `static/CNAME` — the domain stays pointed at `site`
- `params.home.featured` / `params.home.startHere`, which reference the posts that exist here
- `content/posts/`, and `.prettierignore` not excluding it
- `theory.md`, `walkthrough.md`, and `.claude/`, which document `site`'s publisher relationship

## Tasks

```bash
task setup   # install dependencies via Homebrew
task dev     # hugo server -D -F (drafts + future content visible)
task build   # hugo --gc --minify --panicOnWarning (production)
task check   # hugo --renderToMemory --panicOnWarning (the integration test)
task format  # prettier --write .
```

Run `task --list` for the rest.

## Conventions

- **No taxonomies.** `taxonomies: {}` in `hugo.yaml` is intentional; posts may carry `tags` but the site discards them.
- **Templated identity.** Author name, motto, contact details, and vCard fields all live in `hugo.yaml` `params`. Five home-page output formats (`html`, `rss`, `llms`, `manifest`, `vcard`) and `robots.txt` are templated from it — no `vcard.vcf`, `site.webmanifest`, or `llms.txt` under `static/`.
- **Two archetypes, asymmetric on purpose.** `archetypes/default.md` scaffolds `lastmod` only (structural pages); `archetypes/posts.md` scaffolds `date` only (essays), letting `enableGitInfo` backfill `lastmod` from the commit date.
- **`hugo --panicOnWarning` is the integration test.** There is no test suite. The strict build catches missing shortcodes and broken refs, including the home-page `pageRef` entries.
- **Conditional asset loading.** KaTeX loads when a page sets `math: true`; Mermaid loads when a page uses the `mermaid` shortcode; `callout.css` loads when a page uses the `callout` shortcode. Don't add unconditional scripts to `baseof.html`.
- **Theme colors are mirrored.** `params.themeColor` in `hugo.yaml` must match `--background-color` in `static/style.css`.
- **vCard PII is intentional.** `layouts/index.vcard.vcf` includes home address, mobile, and date of birth deliberately.

## Deployment

GitHub Pages, artifact-based, via `.github/workflows/deploy.yml`. Triggered on push to `main`, manual dispatch, and daily at 08:25 UTC.

## License

- **Code / config**: [MIT](./LICENSE).
- **Content**: [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/) © 2023–2026 Mark Ayers. See [CONTENT-LICENSE.md](./CONTENT-LICENSE.md).
