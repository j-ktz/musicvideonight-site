# Pointing musicvideonight.app at this site

Only needed if/when the domain is registered. GitHub Pages serves a custom
domain free and provisions the HTTPS certificate itself, which `.app` requires
(the whole TLD is HSTS-preloaded — there is no http:// fallback).

## 1. Buy the domain

Any registrar (~$14/yr for `.app`). Cloudflare and Porkbun sell at cost;
Google/Squarespace and GoDaddy are fine too.

## 2. DNS records at the registrar

Apex (`musicvideonight.app`) — four A records and four AAAA records:

```
A     @   185.199.108.153
A     @   185.199.109.153
A     @   185.199.110.153
A     @   185.199.111.153
AAAA  @   2606:50c0:8000::153
AAAA  @   2606:50c0:8001::153
AAAA  @   2606:50c0:8002::153
AAAA  @   2606:50c0:8003::153
```

Plus the www subdomain:

```
CNAME www j-ktz.github.io
```

If you use Cloudflare, set those records to **DNS only** (gray cloud), not
proxied — proxying breaks Pages' certificate provisioning.

## 3. Tell GitHub

Repo → Settings → Pages → Custom domain → `musicvideonight.app` → Save, then
tick **Enforce HTTPS** once the certificate is issued (usually minutes, can be
up to 24h). That writes a `CNAME` file into this repo; leave it there.

Or from the CLI:

```bash
gh api repos/j-ktz/musicvideonight-site/pages -X PUT -f cname=musicvideonight.app -F https_enforced=true
```

## 4. Then, in the app

`LobbyView.inviteText` shares `https://musicvideonight.app/join/<CODE>`. That
link is dead until the domain exists.

The site side of it is already built:

- **`join.html`** shows the code and walks someone through joining. It reads the
  code from `?code=XXXXX`, `?c=XXXXX`, a `/join/XXXXX` path, or a `#XXXXX` hash,
  and falls back to a generic "type the code in yourself" page when there is no
  code (which is also what renders with JavaScript off).
- **`404.html`** catches `/join/<CODE>` — a path with no file behind it, so
  GitHub Pages serves the 404 — and bounces it to `join.html?code=<CODE>`. It
  works under both the project path and an apex domain.

Still to do once the domain resolves: an `apple-app-site-association` file so
the link opens the app directly (universal links). Until then the invite should
not promise a URL that goes nowhere.

`404.html` has its in-page links hard-coded to `/musicvideonight-site/…` for the
project-page path and rewrites them to `/…` on any non-`github.io` host, so it
needs no edit when the domain moves.

## 5. Launch day (unrelated to the domain)

The App Store badge is a placeholder in three places — twice in `index.html`,
once in `join.html`. Each is marked with a comment. For each one:

1. delete the `<p class="badge-note">…</p>` line under it, and
2. change `Coming soon to the` to `Download on the`.

The `href` is already the real store URL (`apps.apple.com/app/id6811704618`), so
nothing else changes and nothing needs redesigning.
