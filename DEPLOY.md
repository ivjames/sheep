# Deploying Sparkle Butt

Live at **https://sparkle.lab980.com** — served from the lab980 droplet
(conventions in the `ivjames/lab980.com` repo's CLAUDE.md).

The game is one static `index.html` with zero dependencies: no build step, no
app process, no port, no pm2, no database. nginx serving the git checkout *is*
the deployment. Everything is driven by the operate CLI at `bin/sparkle`.

> Why not `provision-site`? That script scaffolds proxy-shaped sites (app on a
> local port). This site has no app, so `sparkle setup` writes its own static
> vhost instead — same DNS/doctl, security-headers, and certbot shape.

## One-time bring-up (on the droplet, as root)

```bash
git clone https://github.com/ivjames/sparkle-butt /var/www/sparkle
ln -sf /var/www/sparkle/bin/sparkle /usr/local/bin/sparkle
sparkle setup
```

`sparkle setup` is idempotent and does, in order:

1. **DNS** — `doctl` A record `sparkle.lab980.com -> droplet IP` (skipped if
   it already exists; `--no-dns` to skip, `--ip` to override autodetect).
   Manual equivalent:
   `doctl compute domain records create lab980.com --record-type A --record-name sparkle --record-data 165.22.128.19 --record-ttl 300`
2. **nginx** — static vhost from `deploy/nginx.conf.template` installed as
   `/etc/nginx/sites-available/sparkle.lab980.com`, symlinked into
   `sites-enabled/`, `nginx -t` + reload. Root is the checkout; `index.html`
   is served `Cache-Control: no-cache` (the whole game is that one file, so
   deploys are live on the next visit); `assets/` caches a week; dotfiles and
   `*.md` are denied. An existing vhost is left untouched (certbot owns it
   after TLS).
3. **TLS** — waits for DNS to resolve, then
   `certbot --nginx -d sparkle.lab980.com --redirect -n`. If DNS is still
   propagating it tells you the exact certbot command to re-run.

## Deploying updates

Land changes on `main`, then on the droplet:

```bash
sparkle deploy
```

That's a `git fetch` + `git reset --hard origin/main` of the checkout, plus
one `sed` that stamps the deployed commit into `index.html`'s `BUILD`
constant (the repo file says `'dev'`; the stamp deliberately dirties the
checkout — the next deploy's reset wipes and re-stamps it). No build, no
restart, no reload.

The game shows the stamp on its title screen ("build abc1234") and every
open tab re-fetches the page on load/foreground and compares stamps — on the
title screen it auto-reloads onto the new build, mid-game it shows a
"tap to update" toast. So an iPad that never closes Safari still picks up
deploys without any manual cache clearing.

## Shared art store (optional)

The sprite manager (`https://sparkle.lab980.com/tools/sprite-manager.html`)
can run against a shared store so anyone with the link sees — and can upload —
the same art. Still no app process: nginx's WebDAV module handles writes
(`PUT`/`DELETE`) and `autoindex_format json` handles listings; files live in
`/var/lib/sparkle-art/`, outside the checkout, so `sparkle deploy` never
touches them. No accounts, no sign-in — link access is the access model
(fine for an unlisted art scratchpad; the store is one confined directory
with an 8 MB upload cap).

```bash
sparkle art-setup           # store dir, nginx snippet, vhost include, reload
```

`art-setup` is idempotent and safe on the live vhost: the `/art/` location
lives in `snippets/sparkle-art.conf` and gets `include`d into the existing
server blocks (certbot owns that file after TLS, so it is edited in place,
not regenerated). Without `art-setup`, the tool quietly falls back to
local-only mode (uploads stay in that person's browser).

## Check it

```bash
sparkle status        # HEAD commit, live probe, art-store state, cert days
health-check --site sparkle   # the droplet-wide auditor also covers it
```

## Overrides

- `SPARKLE_FQDN` — serve under a different name (default `sparkle.lab980.com`)
- `SPARKLE_BRANCH` — deploy a different branch (default `main`)
