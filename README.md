# Publish npm with Provenance

This action automates publishing a Node.js package to the npm registry using pnpm. It supports stable and beta releases based on git tags, strict version enforcement, and npm provenance generation.

## Usage

Add the following to your workflow file:

```yaml
- uses: lovc21/publish-npm-provenance@main
  env:
    NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

The workflow must run on a tag push and have `id-token: write` permission for provenance:

```yaml
on:
  push:
    tags:
      - 'v*'

jobs:
  publish:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
    steps:
      - uses: actions/checkout@v4
      - uses: lovc21/publish-npm-provenance@main
        with:
          # Optional: set if package.json is not in the root (e.g. app/)
          # path: app
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

## Tag format

| Tag example      | Publishes as                       |
| ---------------- | ---------------------------------- |
| `v1.0.0`         | `latest` (stable)                  |
| `service/v1.0.0` | `latest` (stable, monorepo prefix) |
| `v1.0.0-rc.1`    | `beta`                             |
| `v1.0.0-alpha.1` | `beta`                             |

## Inputs

| Input                | Type    | Default               | Description                                                     |
| -------------------- | ------- | --------------------- | --------------------------------------------------------------- |
| `path`               | string  | `.`                   | Path to the package root directory containing `package.json`    |
| `strict_version`     | boolean | `true`                | Enforce that the git tag version matches `package.json` version |
| `npm_access`         | string  | `restricted`          | Package access level: `public` or `restricted`                  |
| `npm_provenance`     | boolean | `true`                | Enable npm provenance (requires `id-token: write` permission)   |
| `skip_publish`       | boolean | `false`               | Skip the publish step                                           |
| `debug_mode`         | boolean | `false`               | Print debug information on failure                              |
| `regex_stable_tag`   | string  | `v1.0.0` pattern      | Regex to match stable release tags                              |
| `regex_unstable_tag` | string  | `v1.0.0-rc.1` pattern | Regex to match beta release tags (rc/alpha)                     |

## Secrets

| Secret      | Description                                                                                               |
| ----------- | --------------------------------------------------------------------------------------------------------- |
| `NPM_TOKEN` | npm access token with publish permissions. Pass as `NODE_AUTH_TOKEN` or `NPM_TOKEN` environment variable. |
