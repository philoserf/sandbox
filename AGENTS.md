# AGENTS.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository

Personal GitHub Pages site (markpaulayers.com) using Jekyll with the Minima theme (auto skin). Builds and deploys automatically via GitHub Pages on push to main. CLAUDE.md symlinks here.

**Key files:**

- README.md — main site content
- \_config.yml — Jekyll config (theme, skin, date format)
- CNAME — custom domain (markpaulayers.com)

## Markdown

- Format with `bunx prettier --write <file>`
- Every fenced code block must have a language tag; use `text` when no other applies
- Line length is not enforced

## Local Preview

```bash
bundle exec jekyll serve
```

Bootstrap if no Gemfile: `bundle init && bundle add jekyll minima`

## CI

- **Claude Code Action** (.github/workflows/claude.yml) — triggered by `@claude` mentions in issues/PRs
- **Dependabot** — weekly updates for GitHub Actions

## Principles

- Say yes and build
- Name things once
- Assume good intentions
- Keep tools and structure small
- Follow defaults until they fail
- Keep the signal sharp
- Be clear, direct, avoid hedging
