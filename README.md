# `depot/use-action`

This action prepares the environment to build Docker images with the [Depot CLI](https://github.com/depot/cli). It does this by making the following changes:

1. Installs the `depot` CLI in the GitHub Actions environment.
2. Installs Depot as a Docker CLI plugin (`docker depot ...`)
3. Sets the Depot plugin as the default Docker builder (`docker build ...`)
4. Installs a Buildx driver so that `docker buildx build` is forwarded to the Depot plugin.

## Usage

Download and use the latest version of the CLI:

```yaml
jobs:
  job-name:
    steps:
      - uses: depot/use-action@v1
      - run: depot ...
```

The `depot` CLI needs to resolve a project ID to build images. This can be from a `DEPOT_PROJECT_ID` environment variable, a `depot.json` file, or the `--project` CLI flag. To set the global `DEPOT_PROJECT_ID`, use the `project` input:

```yaml
jobs:
  job-name:
    steps:
      - uses: depot/use-action@v1
        with:
          project: abc123xzy
```

The `depot` CLI also needs to authenticate with Depot to connect to remote builders. We recommend using [OIDC trust relationships](https://depot.dev/docs/integrations/github-actions#build-and-push-to-docker-hub-with-oidc-token-exchange) to authenticate, as these do not require configuring a static access token:

```yaml
jobs:
  job-name:
    permissions:
      contents: read
      id-token: write
      # optional, if you need to push to ghcr.io
      packages: write

    steps:
      - uses: depot/use-action@v1
```

If you do need to provide an API token, you can set the `DEPOT_TOKEN` environment variable with the `token` input:

```yaml
jobs:
  job-name:
    steps:
      - uses: depot/use-action@v1
        with:
          token: ${{ secrets.DEPOT_TOKEN }}
```

## Inputs

- `project` (optional) - A string representing the project ID to use for builds. This can be overridden by the `DEPOT_PROJECT_ID` environment variable or the `--project` CLI flag.
- `token` (optional) - A string representing the API token to use for authentication. This can be overridden by the `DEPOT_TOKEN` environment variable.
- `version` (optional) - A string representing the version of the Depot CLI to install (e.g. `1.2.3`). The default value is `latest` which will install the latest available version. Can also specify a semver version range selector (e.g. `0.x.x`).

## License

MIT License, see `LICENSE`.
