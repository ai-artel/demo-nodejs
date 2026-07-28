# ai-artel Demo — Node.js Web Application

This is a sample Node.js web app using the Express framework and EJS templates.

The app has been designed with containers in mind, in order to provide a real
working application for deployment, something more than "hello-world" but
with the minimum of pre-reqs. It is not intended as a complete example of a
fully functioning architecture or complex software design.

The delivery path is deliberately simple: **GitHub Actions** builds and
publishes a container image, and **Dokploy** runs it via Docker Compose.
There is no Kubernetes, Jenkins or Semaphore path.

The app has several basic pages accessed from the top navigation menu, some of which are only lit up when certain configuration variables are set (see 'Optional Features' below):

- **'Info'** - Will show system & runtime information, and will also display if the app is running from within a Docker container.
- **'Tools'** - Some tools useful in demos, such a forcing CPU load (for autoscale demos), and an error/exception page.
- **'Monitor'** - Display realtime monitoring data, showing memory usage/total and process CPU load.
- **'Weather'** - (Optional) Gets the location of the client page (with HTML5 Geolocation). The resulting location is used to fetch weather data from the [OpenWeather](https://openweathermap.org/) API
- **'Todo'** - (Optional) This is a small todo/task-list app which uses MongoDB as a database.

# 🧑‍💻 Running and Testing Locally

### Pre-reqs

- Be using Linux, WSL or MacOS, with bash, make etc
- [Node.js](https://nodejs.org/en/) - for running locally, linting, running tests etc
- [Docker](https://docs.docker.com/get-docker/) - for running as a container, or building images

Clone the project to any directory where you do development work, then copy
`.env.example` to `.env` and adjust as needed.

### Makefile

A standard GNU Make file is provided to help with running and building locally.

```txt
$ make

help                 💬 This help message
install              Install exact Node dependencies from package-lock.json
lint                 Check JavaScript formatting
test                 Run baseline HTTP integration tests against a running app
test-health          Verify liveness and readiness endpoints against a running app
image                🔨 Build a local OCI image (override IMAGE_TAG or IMAGE)
image-smoke          Start the local image and wait for its Docker healthcheck
push                 📤 Push a previously built image
run                  🏃 Start the app locally with the development environment
clean                🧹 Remove local dependency and CI output directories
```

Make file variables and default values, pass these in when calling `make`, e.g. `make image IMAGE_REPO=blah/foo`

| Makefile Variable | Default                            |
| ------------------ | ----------------------------------- |
| IMAGE_REG          | ghcr<span>.</span>io                |
| IMAGE_REPO         | ai-artel/demo-nodejs                |
| IMAGE_TAG          | dev                                  |
| TEST_BASE_URL      | http://127.0.0.1:3000                |

The web app will be listening on the standard Express port of 3000, but this can be changed by setting the `PORT` environmental variable.

Alternatively, run the local development stack with Docker Compose:

```bash
docker compose up
```

# 📦 Containers

Container images are published to GitHub Container Registry as
`ghcr.io/ai-artel/demo-nodejs`.

Run in a container with:

```bash
docker run --rm -it -p 3000:3000 ghcr.io/ai-artel/demo-nodejs:latest
```

Should you want to build your own container, use `make image` and the above variables to customise the name & tag.

# 🏗️ CI/CD

GitHub Actions is the only CI/CD path for this project:

- [`.github/workflows/ci.yml`](.github/workflows/ci.yml) - lints and validates the app on every pull request and push to `main`.
- [`.github/workflows/release.yml`](.github/workflows/release.yml) - builds and publishes a container image to GHCR whenever a `v*` tag is pushed.

# 🚀 Deployment

Deployment is handled by [Dokploy](https://dokploy.com/) using
[deploy/compose/docker-compose.release.yml](deploy/compose/docker-compose.release.yml).
See [deploy/readme.md](deploy/readme.md) and
[deploy/compose/README.md](deploy/compose/README.md) for details.

# 🧪 Testing

This project uses HTTP files located in `src/tests/` that can be used a few different ways, you can install the [VSCode REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client) or [httpYac](https://marketplace.visualstudio.com/items?itemName=anweber.vscode-httpyac) allowing you to interactively run requests from VSCode

You can interactively run & send the requests in the `src/tests` file using these extensions, but the main reason to use _httpYac_, is it has a much richer language & the support of assertions which can turn the request files into integration tests too 👌

_httpYac_ has a command line tool for running tests and .http files which forms the basis of the `make test` and `make test-report` makefile targets.

## Running Tests

To run the tests, in one shell session run `make run` and open a second separate shell run `make test`. You can set `TEST_BASE_URL` to point the tests at a different URL, host or port, and set `TEST_FILES` to a glob that matches the files you want to run from the src/tests/ directory.

There's also an advisory k6 load-test script at `tests/load/smoke.js` that
exercises `/healthz`, `/readyz` and `/info` under light concurrent load.

# 🎂 Optional Features

The app will start up and run with zero configuration, however the following optional features can be enabled with various settings

### Weather Details

Enable this by setting `WEATHER_API_KEY`

This will require a API key from OpenWeather, you can [sign up for free and get one here](https://openweathermap.org/price). The page uses a browser API for geolocation to fetch the user's location.  
However, the `geolocation.getCurrentPosition()` browser API will only work when the site is served via HTTPS or from localhost. As a fallback, weather for London, UK will be show if the current position can not be obtained

### Todo App

Enable this by setting `TODO_MONGO_CONNSTR`

A mini todo & task tracking app can be enabled if a MongoDB backend is provided and a connection string to access it.

The default database name is `todoDb` but you can change this by setting `TODO_MONGO_DB`

When working locally and if you have Docker, you can easily run MongoDB with `docker run --network host mongo`

# 🛠️ Configuration

The following configuration environmental variables are supported, however none are mandatory. These can be set directly or when running locally will be picked up from an `.env` file if it is present. A sample `.env` file called `.env.example` is provided for you to copy.

| Environmental Variable | Default | Description                                                                      |
| ----------------------- | ------- | ---------------------------------------------------------------------------------- |
| PORT                    | 3000    | Port the server will listen on                                                     |
| TODO_MONGO_CONNSTR      | _none_  | Connect to specified MongoDB instance, when set the todo feature will be enabled   |
| TODO_MONGO_DB           | todoDb  | Name of the database in MongoDB to use (optional)                                 |
| WEATHER_API_KEY         | _none_  | OpenWeather API key. [Info here](https://openweathermap.org/api)                  |
| DISABLE_METRICS         | _none_  | Set to truthy value if you want to switch off Prometheus metrics                  |
| REDIS_SESSION_HOST      | _none_  | Point to a Redis host to hold/persist session cache                               |
| SESSION_SECRET          | _none_  | Secret used to sign the session cookie — required in production                   |
| SESSION_COOKIE_SECURE   | false   | Set to `true` in production when served over HTTPS                                |
| TRUST_PROXY             | false   | Set to `true` when running behind a reverse proxy (e.g. Dokploy's Traefik)         |

## Deployment

See the [deploy](./deploy) folder for deploying with Dokploy and Docker.
