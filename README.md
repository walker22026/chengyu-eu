# chengyu.eu

Personal site built with [Hugo](https://gohugo.io/) + [Blowfish](https://blowfish.page/) — a daily-notes / portfolio site for Chengyu, a final-year Computer Science student at the University of Sydney.

## Local preview

```bash
hugo server -D
```

Requires Hugo installed locally (extended, >= 0.165.0 recommended): `brew install hugo`

## Structure

- `content/_index.md` — homepage.
- `content/about/_index.md` — bio / "what I'm looking for" (fill in résumé, GitHub, LinkedIn, email links here).
- `content/posts/` — blog posts. New post: create `content/posts/<slug>/index.md`, following the front matter of an existing post.
- `config/_default/` — site config (title, menu, theme params).
- `themes/blowfish/` — the Blowfish theme, vendored directly (not a git submodule), so it needs no network access to build. To upgrade, copy a newer version's `layouts/`, `assets/`, `static/`, `archetypes/`, `i18n/`, `data/` over this directory.

## Migrated posts

Several early posts were translated from the Chinese-language blog at mcetf.cn and migrated here, keeping their original publish dates. Each post's original cover image was intentionally left out during migration; any other images are hot-linked from the original image host.

## Deploying

This site deploys to Cloudflare via Git integration:

- Build command: `hugo --gc --minify`
- `wrangler.jsonc` points `assets.directory` at Hugo's `public/` output.
- Custom domain: `chengyu.eu`, configured in the Cloudflare project's Custom Domains settings.
