# Changelog

## v1.0.1

- `action.yml` description shortened to 110 characters; GitHub Marketplace
  requires fewer than 125.
- README: the API URL note now names only the public `https://<org>.app.dstl8.ai`
  host. No change to the action's behavior.
- Repository: SOC 2 change-management protection on `main` (pull request with one
  approving review, no force-push or deletion) and a pull-request template.

## v1.0.0

- First public release: `dstl8 graph push` from GitHub Actions.
- Linux and macOS runners (amd64, arm64); checksum-verified CLI download.
- Inputs: `api-url`, `token`, `version`, `path`, `extra-args`, `dry-run`,
  `fail-on-error`, `label`, `github-token`.
- Outputs: `status`, `repo`, `commit-sha`, `cli-version`, `artifact-path`.
- Detached-HEAD checkouts name the branch from the triggering event.
