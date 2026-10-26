# sandbox

![Status: Staging](https://img.shields.io/badge/Status-Staging-lightgrey.svg)

A staging copy of [philoserf/site](https://github.com/philoserf/site) — same layouts, styles, config, and workflows, with throwaway posts. Changes to the rendering half of the site are tried here first, then ported to `site`. Deployed to [sandbox.philoserf.com](https://sandbox.philoserf.com/). **Site motto:** COGITA·DISCE·NECTE·FARE (Think, Learn, Connect, Speak).

## What this repo is

`site` is the rendering half of a two-repo writing pipeline: the author writes in an Obsidian vault and a publisher plugin pushes notes into `content/posts/` there, so those posts are read-only output. This repo has no publisher attached, and **all of its prose is placeholder** — Old English word-salad in place of `site`'s real writing, so what you see on the page is layout and typography rather than content worth reading.

The placeholder is composed, not quoted. Like Latin lorem ipsum, which scrambles Cicero, it draws words from a genuine Old English vocabulary and arranges them into nonsense; no passage here reproduces a surviving text. The thorn, eth, and ash (þ ð æ) are the point as much as the words are — they exercise font fallback and line-breaking the way plain ASCII filler cannot.

`layouts/` is deliberately left in English. Template chrome — "Featured post", "Recent", "Start here", the archive intro — is UI, not content, and keeping the templates byte-identical to `site` is what makes this repo useful for trying layout changes before they land there.

Everything else should stay a faithful mirror of `site`. The intended differences are:

- `baseURL` (`sandbox.philoserf.com`, not `philoserf.com`)
- The Worker's `name` and route in `wrangler.jsonc`, the Monday (not daily) cron, and no `security.txt` sentence under Cloudflare settings
- All prose: `content/` and the identity params in `hugo.yaml` — `title`, `author`, `description`, `email`, `motto`, `home`, `contact`, `social` — are placeholder, not `site`'s real content
- `params.contact` is invented. `site` deliberately publishes real vCard PII; a public sandbox should not, so the address, phone (555-01xx, reserved for fiction), and birthday here are fabricated, and `email`/`social` point at `example.com`
- `params.home.featured` / `params.home.startHere`, which reference the posts that exist here
- `content/posts/`, and `.prettierignore` not excluding it
- `THEORY.md`, `WALKTHROUGH.md`, and `.claude/`, which document `site`'s publisher relationship

## Tasks

```bash
task setup   # install dependencies via Homebrew
task dev     # hugo server -D -F (drafts + future content visible)
task build   # hugo --gc --minify --panicOnWarning (production)
task check   # build.command's validate and strict passes, in memory (the integration test)
task format  # bun fmt
task links   # lychee over public/ (external links and anchors)
task deploy  # build and deploy by hand; pushes to main deploy on their own
```

Run `task --list` for the rest.

## Conventions

- **No taxonomies.** `taxonomies: {}` in `hugo.yaml` is intentional; posts may carry `tags` but the site discards them.
- **Templated identity.** Author name, motto, contact details, and vCard fields all live in `hugo.yaml` `params`. Five home-page output formats (`html`, `rss`, `llms`, `manifest`, `vcard`) and `robots.txt` are templated from it — no `vcard.vcf`, `site.webmanifest`, or `llms.txt` under `static/`.
- **The `latin-motto` shortcode is no longer Latin.** The name matches `site`, and renaming it would diverge the templates for nothing; it renders whatever `params.motto` holds.
- **Two archetypes, asymmetric on purpose.** `archetypes/default.md` scaffolds `lastmod` only (structural pages); `archetypes/posts.md` scaffolds `date` only (essays), letting `enableGitInfo` backfill `lastmod` from the commit date.
- **`hugo --panicOnWarning` is the integration test.** There is no test suite. The strict build catches missing shortcodes and broken refs, including the home-page `pageRef` entries.
- **Conditional asset loading.** KaTeX loads when a page sets `math: true`; Mermaid loads when a page uses the `mermaid` shortcode; `callout.css` loads when a page uses the `callout` shortcode. Don't add unconditional scripts to `baseof.html`.
- **Theme colors are mirrored.** `params.themeColor` in `hugo.yaml` must match `--background-color` in `assets/css/style.css`.
- **vCard PII is intentional.** `layouts/index.vcard.vcf` includes home address, mobile, and date of birth deliberately.

## Deployment

A Cloudflare Worker serving `public/` as static assets, with Hugo's `404.html` as the not-found page. Workers Builds, Cloudflare's git integration, runs `bunx wrangler deploy` on every push to `main`, and `bunx wrangler versions upload` on other branches. Either one first runs `build.command` from `wrangler.jsonc`: a validate pass over drafts and future-dated posts, then the strict build. A cron trigger in `wrangler.jsonc` runs `worker.js` every Monday at 09:00 UTC (05:00 Eastern in summer, 04:00 in winter), which POSTs the Worker's own deploy hook, so a future-dated post goes live within a week of its date. That script is the Worker's only code: requests are served from `public/`, and one matching no file is handed straight back to asset serving for the 404 page. The build image's Hugo is set by the `HUGO_VERSION` build variable in the dashboard, not by this repo.

### Cloudflare settings

`wrangler.jsonc` holds the Worker, its build and its custom domain. These settings live only in Cloudflare, so restore them by hand if the Worker is ever recreated:

- **Builds connection** (Workers & Pages → `sandbox` → Settings → Builds): GitHub `philoserf/sandbox`, root directory `/`, build command empty, since `build.command` is the build. The Cloudflare GitHub app needs access to this repo.
- **Production branch** `main`, deploy command `bunx wrangler deploy`.
- **Non-production branches** on, deploy command `bunx wrangler versions upload`. They build and validate, and report as the "Workers Builds: sandbox" check on GitHub, but get no preview URL: `preview_urls` is off.
- **Build variable** `HUGO_VERSION=latest` on both, so the build image's Hugo floats.
- **Deploy hook** "Monday rebuild" on `main`. Its URL is the Worker secret `DEPLOY_HOOK`, which `worker.js` POSTs on the cron. To replace it, create a hook under Settings → Builds → Deploy Hooks, pipe its URL to `bunx wrangler secret put DEPLOY_HOOK`, and delete the old hook.

On a fresh account, run `task deploy` once to create the Worker and attach the domain, then connect Builds and set `DEPLOY_HOOK`. Until the secret exists, the cron fails and nothing else does. The domain attaches only when no other DNS record holds the hostname.

## License

- **Code / config**: [MIT](./LICENSE).
- **Content**: [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/) © 2023–2026 Mark Ayers. See [CONTENT-LICENSE.md](./CONTENT-LICENSE.md).
