# Release Docker Compose manifest

`docker-compose.release.yml` is the manifest Dokploy runs in production. It
pulls the image built by `.github/workflows/release.yml` from GHCR — it does
not mount source code or install dependencies.

Copy [deployment.env.example](deployment.env.example) to `deployment.env`
(never commit the real file) and set `IMAGE_REF` to the image tag or digest
you want to run, e.g. `ghcr.io/ai-artel/demo-nodejs:v1.0.0`.

Manual use for local verification:

```bash
docker compose \
  --project-name demo-nodejs \
  --env-file deployment.env \
  --file docker-compose.release.yml \
  up
```

In Dokploy, point the application at this compose file, set the same
environment variables (or an image digest) in the Dokploy dashboard, and
assign the domain from the Domains tab — Dokploy manages the reverse proxy
and TLS certificate itself, no manual Traefik labels are required.
