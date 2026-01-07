# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a personal GitHub Pages site using Jekyll with the Minima theme. The site is hosted at markpaulayers.com and serves as a public space for thinking, reflecting, and sharing ideas.

## Key Principles (from README)

- Say yes and build
- Name things once
- Assume good intentions
- Keep tools and structure as small as possible
- Follow defaults until they fail
- Keep the signal sharp

## Site Structure

- **README.md**: Main content - personal philosophy, background, and contact
- **\_config.yml**: Jekyll configuration (Minima theme with auto skin)
- **CNAME**: Custom domain configuration (markpaulayers.com)

## Working with This Site

This is a GitHub Pages repository that builds automatically on push. No local build commands are necessary unless you want to preview changes.

To preview locally (optional):

```bash
# Install Jekyll if needed
gem install bundler jekyll

# Create Gemfile if it doesn't exist
bundle init
bundle add jekyll
bundle add minima

# Serve locally
bundle exec jekyll serve
```

The site builds and deploys automatically via GitHub Pages when changes are pushed to the main branch.

## Content Philosophy

Content should reflect the principles stated: clarity, directness, consequence, and translating thought into words others can use. Avoid hedging or unnecessary complexity.
