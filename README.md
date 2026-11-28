# Garbage Collector — Personal Blog

This repository contains the source for [Pietro F. Maggi's personal blog](https://pietromaggi.com), built with [Hugo](https://gohugo.io/) and the [Monochrome](https://github.com/kaiiiz/hugo-theme-monochrome) theme, and deployed to **Cloudflare Workers (Static Assets)** via GitHub Actions and Wrangler.

## Local Development

Clone the repository with submodules (required for the theme):

```sh
git clone --recurse-submodules https://github.com/pfmaggi/pfmaggi.github.io.git
cd pfmaggi.github.io
```

Run the local Hugo server (requires **Hugo Extended `>= 0.146.0`** and **Dart Sass** on `PATH`; CI uses Hugo `0.170.1` + Dart Sass `1.108.2`):

```sh
hugo server -D
```

## Publishing a New Post

1. Create a new Markdown file under `./content/posts/`.
2. Set `draft: false` when ready to publish.
3. Push to `main` — the [Cloudflare Workers workflow](.github/workflows/deploy-cloudflare.yml) builds the site with Hugo and deploys `./public` via Wrangler.

## Updating the Theme

The Monochrome theme is pinned as a git submodule at `themes/hugo-theme-monochrome`:

```sh
git -C themes/hugo-theme-monochrome fetch --tags
git -C themes/hugo-theme-monochrome checkout <tag>
```

## Deployment Secrets (GitHub Actions)

Configure the following in **Settings → Secrets and variables → Actions**:

- Secret `CLOUDFLARE_API_TOKEN` — Cloudflare API token with *Workers Scripts: Edit* permission.
- Secret `CLOUDFLARE_ACCOUNT_ID` — Cloudflare account ID.
- Optional Variable `SITE_BASE_URL` — Defaults to `https://pietromaggi-blog.pfm.workers.dev/` during staging; set to `https://pietromaggi.com/` when cutting over the custom domain.
