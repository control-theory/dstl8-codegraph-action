# dstl8 code graph action

Push your repository's code graph to [Dstl8](https://dstl8.ai) from GitHub
Actions, so incidents and log patterns link straight to the code that emits
them.

The action installs a checksum-verified release of the
[dstl8 CLI](https://github.com/control-theory/dstl8) and runs
`dstl8 graph push`. Only metadata leaves the runner (file paths, service
names, log format strings, symbols and call edges), never source code. Use
`dry-run` to see every byte that would be sent.

By default the action **never fails your build**: any problem becomes a
workflow warning. Set `fail-on-error: "true"` to change that.

## Quick start

1. In Dstl8, create an API token: **Org Settings → API Tokens**.
2. In your GitHub repo (or org), add:
   - secret `DSTL8_API_TOKEN`: the token
   - variable `DSTL8_API_URL`: your org's API URL, e.g. `https://acme.app.dstl8.ai`
3. Add `.github/workflows/dstl8-codegraph.yml`:

```yaml
name: dstl8 code graph
on:
  push:
    branches: [main]        # default branch only; other branches are skipped
  schedule:
    - cron: "17 6 * * 1"    # weekly refresh keeps quiet repos current
  workflow_dispatch:

permissions:
  contents: read

jobs:
  codegraph:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
        with:
          fetch-depth: 50    # history to diff against the previous push
      - uses: control-theory/dstl8-codegraph-action@v1
        with:
          api-url: ${{ vars.DSTL8_API_URL }}
          token: ${{ secrets.DSTL8_API_TOKEN }}
```

The API URL is the address you use for your org's Dstl8 API. Most orgs are
`https://<org>.app.dstl8.ai`. If your org lives elsewhere (for example
`https://<org>.wd.dstl8.ai`), use that host. A wrong host returns an HTML 404,
not an auth error.

## Inputs

| Input | Default | Description |
|-------|---------|-------------|
| `api-url` | | Org API base URL. Required unless `dry-run`. |
| `token` | | `dstl8_` API token. Store it as a secret. Required unless `dry-run`. |
| `version` | `latest` | dstl8 CLI release, e.g. `v0.2.10`. Pin it for reproducible builds. |
| `path` | `.` | Directory to extract, for monorepos. |
| `extra-args` | | Extra `dstl8 graph` flags, shell-quoted, e.g. `--exclude 'gen/**' --service api='cmd/api/**'`. |
| `dry-run` | `false` | `"true"` builds the graph to a file (`artifact-path`) and uploads nothing. Needs no token. |
| `fail-on-error` | `false` | `"true"` fails the job when the push cannot complete. |
| `label` | | Name shown in log lines when one job pushes to several orgs. |
| `github-token` | `${{ github.token }}` | Used only to look up the latest CLI release without hitting rate limits. |

## Outputs

| Output | Description |
|--------|-------------|
| `status` | `pushed`, `refreshed`, `unchanged`, `skipped`, `dry-run` or `failed` |
| `repo` | Repository key, e.g. `github.com/acme/checkout` |
| `commit-sha` | Commit the snapshot was taken at |
| `cli-version` | dstl8 CLI release that ran |
| `artifact-path` | Built graph JSON (dry-run only) |

`skipped` means there was nothing to do: either the server already has this
exact graph, or the branch is not the repository's default branch.

## Notes

- **Runners:** Linux and macOS, x86-64 and arm64. Windows runners are not supported.
- **Default branch only.** Dstl8 tracks the default branch; pushes from other
  branches are skipped, so a pull-request trigger is harmless but useless.
- **Audit first:** run once with `dry-run: "true"` and upload `artifact-path`
  with `actions/upload-artifact` to review exactly what would be sent.
- **Tuning what is indexed:** `.dstl8.yaml` (service mapping) and
  `.dstl8ignore` (gitignore syntax) in the repo root. See the
  [code graph docs](https://docs.controltheory.com).
- **Pinning:** `@v1` follows compatible releases. For maximum supply-chain
  safety, pin a full commit SHA and set `version`.

## Without this action

Any CI can run the CLI directly:

```sh
curl -fsSL https://install.dstl8.ai/script/dstl8-cli | sh
DSTL8_API_URL=https://acme.app.dstl8.ai DSTL8_API_TOKEN=$TOKEN dstl8 graph push
```

## License

MIT
