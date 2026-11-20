<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/icon-dark.svg">
    <img src="assets/icon.svg" alt="Traefik logo" width="128">
  </picture>
</p>

<h1 align="center">aac-traefik</h1>

<p align="center">Self-hosted deployment definition for Traefik, a modern HTTP reverse proxy and load balancer.</p>

<p align="center">
  <a href="https://github.com/traefik/traefik"><img alt="Upstream" src="https://img.shields.io/badge/upstream-traefik%2Ftraefik-blue?logo=github"></a>
  <img alt="Image" src="https://img.shields.io/badge/image-traefik%3Av3.7-2496ED?logo=docker&logoColor=white">
  <a href="https://github.com/traefik/traefik/blob/master/LICENSE.md"><img alt="License" src="https://img.shields.io/badge/license-MIT-green"></a>
</p>

## About

Traefik is a cloud-native reverse proxy and load balancer. It discovers services
automatically from the Docker provider, routes HTTP and HTTPS traffic to them and manages
TLS certificates through Let's Encrypt. This repository defines how Traefik is deployed as
an App-as-Code service; it does not contain the Traefik software itself.

Key features (as provided by upstream):

- Automatic service discovery from the Docker API via container labels
- Automatic TLS certificates from ACME providers, including DNS-01 challenges
- HTTP to HTTPS redirection on the `web` entrypoint
- Built-in dashboard and Prometheus metrics
- Hot reload of dynamic configuration files

## Upstream

- Project: <https://github.com/traefik/traefik>
- Documentation: <https://doc.traefik.io/traefik/>
- Container image: `traefik:v3.7` (Docker Hub)
- License: MIT (upstream project)

## Quick start (Docker Compose)

The Compose file is rendered from `service.yml` by the App-as-Code template engine. The
static Traefik configuration (`traefik.yml`) and the dynamic configuration are rendered from
the templates in `custom_templates/`. The container is started with
`--configFile=/etc/traefik/traefik.yml`.

For a manual run, provide the secrets listed under "Configuration" in the environment
(for example `IONOS_API_KEY`) and start the rendered Compose file:

```bash
docker compose up -d
```

Secrets are not stored in this repository. Provide them through the deployment pipeline
or your own environment. Do not commit them.

## Configuration

Environment variables passed to the container (defaults from `service.yml`; `CHANGE_ME` must be set per deployment):

| Variable | Default | Description |
|---|---|---|
| `LOG_LEVEL` | `WARN` | Traefik log level |
| `IONOS_API_KEY` | `CHANGE_ME` (secret) | API key for the IONOS DNS provider used for the ACME DNS-01 challenge |
| `LEGO_CA_CERTIFICATES` | empty | Extra CA root files for the ACME client; empty uses the system pool only |
| `LEGO_CA_SYSTEM_CERT_POOL` | `true` | Keep the system certificate pool when extra CA roots are added |
| `PUID` / `PGID` | `1000` | User and group ID the container runs as |
| `TZ` | `Europe/Berlin` | Time zone |
| `HOMEPAGE_WIDGET_SLUG` | `traefik-dashboard` | Slug used by the Homepage dashboard integration |
| `HOMEPAGE_WIDGET_TYPE` | `traefik` | Widget type used by the Homepage dashboard integration |
| `SOME_TEST_VAR` | `test-value` | Test variable from `service.yml`; has no effect on Traefik |

Ports:

| Port | Protocol | Purpose |
|---|---|---|
| `80` | TCP | HTTP entrypoint (`web`); redirects to HTTPS |
| `443` | TCP | HTTPS entrypoint (`websecure`) |
| `8080` | TCP | Traefik dashboard and API (published as `28080` on the host) |

Volumes:

| Volume | Mount | Description |
|---|---|---|
| `docker_sock` | `/var/run/docker.sock` (read-only) | Docker API used for service discovery |
| `data` | `/data` | Data directory |
| `log` | `/log` | Traefik access and application logs |
| `config` | `/etc/traefik` | Static and dynamic configuration files |
| `letsencrypt` | `/letsencrypt` | Let's Encrypt certificates and ACME account data |

Integrations (from `service.yml`): the Homepage dashboard widget is enabled, AutoDNS creates
DNS records including a wildcard record, and the Traefik dashboard is registered on
service port `8080` with the `ionos` certificate resolver on the `websecure` entrypoint.
An optional second ACME resolver for a private CA is defined but disabled by default.

## Deploy with App-as-Code

1. Edit `service.yml`. It is the single source of truth for image, ports, environment,
   volumes, integrations and per-stage overrides. Do not edit the rendered output.
2. Pushing to `main` (or a configured stage branch) runs the service pipeline, which renders
   the manifests with `aac-template-engine` and triggers the deployment.
3. Releases of this definition are published under <https://github.com/app-as-code/aac-traefik>.

## Credits and license

Traefik is developed by Traefik Labs and licensed under the MIT License. This repository
only contains the deployment definition for it and is not a fork of the application. The
logo is provided by the Traefik project; see [`assets/ICON_SOURCE.md`](assets/ICON_SOURCE.md)
for icon sources and licenses.
