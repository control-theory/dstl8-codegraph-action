# Changelog

## v1.0.0

- First public release: `dstl8 graph push` from GitHub Actions.
- Linux and macOS runners (amd64, arm64); checksum-verified CLI download.
- Inputs: `api-url`, `token`, `version`, `path`, `extra-args`, `dry-run`,
  `fail-on-error`, `label`, `github-token`.
- Outputs: `status`, `repo`, `commit-sha`, `cli-version`, `artifact-path`.
- Detached-HEAD checkouts name the branch from the triggering event.
