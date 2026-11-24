---
title: "Portfolio site built with Astro and GitHub Pages"
description: "How this portfolio and technical blog is structured and deployed as a static site."
pubDatetime: 2026-11-12T09:00:00Z
category: projects
tags:
  - astro
  - github-pages
  - portfolio
draft: false
---

## Context

This site brings a portfolio and a technical blog together in one static
website. AstroPaper provides the shared layouts, navigation, article cards,
search, theme switch, and table of contents; Astro generates the pages for
GitHub Pages.

## Symptoms

A portfolio and a technical blog can become difficult to maintain when they
use separate layouts, duplicate project descriptions, or depend on a server
that a static portfolio does not need.

## Diagnosis

The content divides into three sections: Linux procedures, Ansible articles,
and projects. Each post has a required category, and the home page selects the
latest entries for its featured lists. The site uses the repository owner's
GitHub Pages URL, so it does not need a project-site base path.

## Fix

Set Astro's canonical deployment URL and keep the package manager consistent
with the repository lockfile. A minimal package script for the production
build looks like this:

```json
{
  "scripts": {
    "build": "astro check && astro build && pagefind --site dist && node copy-pagefind.mjs"
  }
}
```

GitHub Actions builds the site artifact and publishes it to GitHub Pages:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: withastro/action@v3
        with:
          package-manager: pnpm@11.3.0
```

The workflow's deploy job publishes the artifact with
`actions/deploy-pages@v4`. No deployment token is stored in the repository.

## Verification

Run the production build locally, then confirm that the GitHub Actions
workflow completes and the site is available at its canonical URL. Check
category links, article routes, search, dark mode, and mobile layouts after
publishing.
