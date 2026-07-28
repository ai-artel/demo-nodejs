# Release Docker Compose manifest

`docker-compose.release.yml` is the manifest Dokploy runs in production. It
pulls the image built by `.github/workflows/release.yml` from GHCR — it does
not mount source code or install dependencies.

Copy [deployment.env.example](deployment.env.example) to `deployment.env`
(never commit the real file) and set `IMAGE_REF` to the image tag or digest
you want to run, e.g. `ghcr.io/ai-artel/demo-nodejs:0.0.2`.

All values are consumed via compose variable substitution (`${VAR}`), not an
`env_file:` entry — so the same variables work whether they come from a local
`--env-file` or from Dokploy's own environment variables panel, without
relying on a physical `deployment.env` file existing in the deployed checkout.

Manual use for local verification:

```bash
docker compose \
  --project-name demo-nodejs \
  --env-file deployment.env \
  --file docker-compose.release.yml \
  up
```

In Dokploy, point the application at this compose file and set the same
environment variables (or an image digest) in the Dokploy dashboard's
Environment Variables tab, then assign the domain from the Domains tab —
Dokploy manages the reverse proxy and TLS certificate itself, no manual
Traefik labels are required.
