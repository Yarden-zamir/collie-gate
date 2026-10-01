# collie-gate

KitSHn recipe for the login gate in front of [Collie](https://github.com/AltanS/collie)
at `https://collie.yarden-zamir.com`.

## What it deploys

- One container: `oauth2-proxy` with the GitHub provider. It listens on the KitSHn
  Unix socket. Only the GitHub user `Yarden-zamir` with an email in the
  `KITSHN_ALLOWED_EMAILS` secret can log in. The cookie lasts 30 days.
- One Caddy route: `/auth/*` goes to oauth2-proxy. Everything else passes
  `forward_auth`, then reaches the Collie bridge on `127.0.0.1:8787`.

Collie is not part of this recipe. It runs on the VPS as a herdr plugin next to
the herdr socket, as a `systemd --user` service. See the Collie section below.

## Params

Set in the GitHub repo settings. KitSHn strips the `KITSHN_` prefix.

| Name | Kind | Value |
| --- | --- | --- |
| `KITSHN_OAUTH2_PROXY_CLIENT_ID` | variable | GitHub OAuth app client id |
| `KITSHN_OAUTH2_PROXY_CLIENT_SECRET` | secret | GitHub OAuth app client secret |
| `KITSHN_OAUTH2_PROXY_COOKIE_SECRET` | secret | `openssl rand -base64 32` |
| `KITSHN_ALLOWED_EMAILS` | secret | Login allowlist, one email per line |

GitHub OAuth app (https://github.com/settings/developers, "New OAuth App"):

- Application name: `collie-gate`
- Homepage URL: `https://collie.yarden-zamir.com`
- Authorization callback URL: `https://collie.yarden-zamir.com/auth/callback`

## Collie on the VPS

Collie config lives in the herdr plugin config dir. The values that pair with this
recipe (deployment variant C in the Collie docs):

```bash
COLLIE_SKIP_SERVE=1
COLLIE_PUBLIC_HOSTS=collie.yarden-zamir.com
COLLIE_ALLOWED_ORIGINS=https://collie.yarden-zamir.com
COLLIE_PUBLIC_URL=https://collie.yarden-zamir.com
COLLIE_DEVICE_HEADER=X-Auth-Request-User
COLLIE_DEVICE_ALLOWLIST=Yarden-zamir
```

## Security note

Collie hands out a shell as the user that runs herdr. The oauth2-proxy allowlist
is the only defense on the public side. Keep `KITSHN_ALLOWED_EMAILS` to your own addresses only.
