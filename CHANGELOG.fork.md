# Changelog (fork)

Release notes for `@variablelab/portless`. Upstream's own history is in [CHANGELOG.md](./CHANGELOG.md).

## 0.0.1

<!-- release:start -->

First release of the fork, based on upstream [portless v0.15.6](https://github.com/vercel-labs/portless/releases/tag/v0.15.6) (upstream `main` at `1ad573b`).

### Added

- **Path-based routing**: `--path <prefix>` on `portless run` and `portless <name>`, the `PORTLESS_PATH` environment variable, and the `path` field in `portless.json` and in the `"portless"` key of `package.json`. Several apps can share one hostname; the proxy dispatches by the longest prefix that matches at a `/` boundary and forwards the full path to the backend. `alias`, `get`, and `list` understand prefixes, and the 404 page lists them. Proposed upstream in [vercel-labs/portless#165](https://github.com/vercel-labs/portless/pull/165).

### Changed

- Published as `@variablelab/portless`. The installed command is still `portless`.
- The npm tarball now includes `LICENSE` and `NOTICE`.

<!-- release:end -->
