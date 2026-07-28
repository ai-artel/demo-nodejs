# Deployment

This app is designed to be deployed as a single Docker container behind
[Dokploy](https://dokploy.com/). There is no Kubernetes, Jenkins or Semaphore
path — GitHub Actions builds and publishes the image, and Dokploy runs it.

## How it fits together

1. `.github/workflows/ci.yml` runs lint on every PR and push to `main`.
2. `.github/workflows/release.yml` builds and pushes a container image to
   GHCR whenever a `v*` tag is pushed.
3. Dokploy is pointed at a specific image tag/digest and runs
   [deploy/compose/docker-compose.release.yml](compose/docker-compose.release.yml),
   assigning the domain and TLS certificate from its own dashboard.

See [deploy/compose/README.md](compose/README.md) for the compose manifest
itself, and the root [README.md](../README.md#configuration) for the
supported environment variables.
