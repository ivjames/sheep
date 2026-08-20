# Deploying Come By!

Target: **https://comeby.lab980.com** — served from the lab980 droplet
(conventions in the `ivjames/lab980.com` repo's CLAUDE.md).

The game is one static `index.html` with zero dependencies: no build step, no
app process, no port, no pm2, no database. nginx serving the git checkout *is*
the deployment. Everything is driven by the operate CLI at `bin/comeby` —
the exact same shape as sparkle-butt's `bin/sparkle`.

> Why not `provision-site`? That script scaffolds proxy-shaped sites (app on a
> local port). This site has no app, so `comeby setup` writes its own static
> vhost instead — same DNS/doctl, security-headers, and certbot shape.

## One-time bring-up (on the droplet, as root)

```bash
git clone https://github.com/ivjames/sheep /var/www/comeby
ln -sf /var/www/comeby/bin/comeby /usr/local/bin/comeby
comeby setup
```

`comeby setup` is idempotent and does, in order:

1. **DNS** — `doctl` A record `comeby.lab980.com -> droplet IP` (skipped if
   it already exists; `--no-dns` to skip, `--ip` to override autodetect).
   Manual equivalent:
   `doctl compute domain records create lab980.com --record-type A --record-name comeby --record-data 165.22.128.19 --record-ttl 300`
2. **nginx** — static vhost from `deploy/nginx.conf.template` installed as
   `/etc/nginx/sites-available/comeby.lab980.com`, symlinked into
   `sites-enabled/`, `nginx -t` + reload. Root is the checkout; `index.html`
   is served `Cache-Control: no-cache` (the whole game is that one file, so
   deploys are live on the next visit); dotfiles and `*.md` are denied.
   An existing vhost is left untouched (certbot owns it after TLS).
3. **TLS** — waits for DNS to resolve, then
   `certbot --nginx -d comeby.lab980.com --redirect -n`. If DNS is still
   propagating it tells you the exact certbot command to re-run.

## Deploying updates

Land changes on `main`, then on the droplet:

```bash
comeby deploy
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

## Check it

```bash
comeby status         # HEAD commit, live probe, cert days
health-check --site comeby    # the droplet-wide auditor also covers it
```

## Overrides

- `COMEBY_FQDN` — serve under a different name (default `comeby.lab980.com`)
- `COMEBY_BRANCH` — deploy a different branch (default `main`)
