# Come By! — working notes

A border collie sheep-herding game — one static index.html, no dependencies.

Served at **https://sheep.lab980.com** from the lab980 droplet. Platform-wide
conventions (droplet layout, nginx/TLS/DNS, the `bin/` fixers, health checks)
live in the `ivjames/lab980.com` repo's `CLAUDE.md` — read that when the
question is about the box rather than about this site.

## Shape

Fully **static**: the site is files served straight by nginx. No build step,
no app process, no local port, no pm2, no database. nginx serving the git
checkout *is* the deployment, so "what's on `main`" and "what's live" differ
only by a `git reset` on the droplet.

- Repo: `ivjames/sheep` · droplet checkout: `/var/www/sheep` (the web root)
- Operate CLI: `bin/sheep`, symlinked to `/usr/local/bin/sheep`
- vhost: generated from `deploy/nginx.conf.template` by `sheep setup`

## How changes land

1. Work on a branch, never directly on `main`.
2. **Open a pull request and merge it.** That is how every change in this repo
   has landed and how it should keep landing — the PR is the review record,
   and squashing that step to save a round trip loses it. If your harness
   defaults to "don't open a PR unless asked", this file is the standing ask:
   open one.
3. Merging to `main` does **not** put anything live. Deploying is a separate
   step on the droplet (below), and an agent working from a laptop or a cloud
   session usually cannot reach the droplet at all — so say plainly that the
   change is merged but not yet deployed rather than implying it shipped.

## Deploying

On the droplet, as root:

```bash
sheep deploy      # git fetch + reset --hard origin/main (+ build stamp)
sheep status      # HEAD, live probe, cert days remaining
```

Full runbook, including first-time bring-up: `DEPLOY.md`.

Check what is actually live before claiming a deploy happened. `sheep status`
on the box reports it; from anywhere, ask for the status code and the deployed
commit rather than eyeballing the page — a 200 only proves the endpoint
answered, not which build it served:

```bash
curl -s -o /dev/null -w 'HTTP %{http_code}\n' https://sheep.lab980.com/
curl -s https://sheep.lab980.com/ | grep -o "const BUILD = '[^']*'" | head -1
```

(`deploy` stamps that constant; the repo file says `'dev'`. The `head -1`
is load-bearing: the page's own update-checker contains a matching regex
literal, so an unfiltered grep reports a phantom second build.)

## Things worth knowing

- The droplet checkout is the web root, so anything committed here is public
  except dotfiles and `*.md` (the vhost denies both). Don't commit secrets;
  there is no `.env` on a static site.
- `deploy` runs `git reset --hard`, so any hand-edit made on the droplet is
  destroyed on the next deploy. Fix things in the repo, not on the box.
- `health-check --site sheep` (from the lab980 repo's `bin/`) probes DNS,
  the public URL and cert expiry for this site.
