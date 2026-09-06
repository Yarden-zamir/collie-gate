# KitSHn Recipe

This repository is a KitSHn recipe. A push to `main` deploys the `prod` environment
on the VPS: one `oauth2-proxy` container on the KitSHn socket, and a Caddy route
for `collie.yarden-zamir.com` that gates the Collie bridge on the host.

## Contract

- `.kitshn.yaml` maps `main` to `prod`. There are no PR previews.
- `.github/workflows/kitshn.yml` calls the reusable KitSHn deploy workflow.
- `compose.yml` runs oauth2-proxy. It reads `emails.txt` from the recipe checkout.
- `Caddyfile.j2` renders the route. `Caddyfile` is generated and ignored.
- `KITSHN_OAUTH2_PROXY_*` GitHub vars and secrets become the container params.
- `KITSHN_SSH_KEY` and `KITSHN_VPS_HOST` are set by `kitshn recipe auth`.

## Origin

- Generated from: https://github.com/Yarden-zamir/kitshn/blob/83ce01c6552ee5a0767fe3b2a3d6c6cff1762941/src/kitshn/repo_init.py
- KitSHn commit: `83ce01c6552ee5a0767fe3b2a3d6c6cff1762941`
