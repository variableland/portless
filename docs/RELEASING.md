# Releasing @variablelab/portless

Releases are driven by the version in `packages/portless/package.json`, the same mechanism upstream uses. The workflow in `.github/workflows/release.yml` runs on every push to `main` and on manual dispatch:

1. It compares the `version` in `packages/portless/package.json` with the latest version on npm.
2. If they differ, it builds and runs `npm publish --provenance` from `packages/portless`.
3. It then creates the GitHub release `v<version>` with the notes found between `<!-- release:start -->` and `<!-- release:end -->` in `CHANGELOG.fork.md`.

Pushing a new version to `main` is what publishes. There is no separate approval step, so treat a merge to `main` that changes the version as a release.

## One-time setup

- Create an npm granular access token with publish rights on the `@variablelab` scope and "bypass two-factor authentication" enabled (npm requires this for publishes from CI). Add it to the repository as the `NPM_TOKEN` secret under Settings, Secrets and variables, Actions.
- The publish job runs in the `Release` GitHub environment. GitHub creates it on the first run. Add required reviewers to that environment if you want a manual gate before every publish.
- Alternative: configure [npm trusted publishing](https://docs.npmjs.com/trusted-publishers) for `variableland/portless` with the `release.yml` workflow, then delete the `NODE_AUTH_TOKEN` line from the publish step. Provenance works with either method.

## Cut a release

1. Branch from `main`: `git checkout -b release/v0.0.2`.
2. Bump `version` in `packages/portless/package.json`.
3. Add an entry at the top of `CHANGELOG.fork.md` wrapped in `<!-- release:start -->` and `<!-- release:end -->`, and remove the markers from the previous entry. Record which upstream version the release is based on.
4. Run `pnpm build && pnpm test`, then `cd packages/portless && npm publish --dry-run` to review the tarball.
5. Open a PR, get CI green, merge to `main`. The release workflow publishes and creates the tag and release.

`prepublishOnly` copies `README.md`, `LICENSE`, and `NOTICE` into `packages/portless/`. The copies are gitignored; delete them after a local dry run so they are not confused with the root files.

## Versioning

Fork versions are independent from upstream's. `0.0.1` is the first release, based on upstream `v0.15.6` plus path-based routing. Each changelog entry records the upstream version it tracks.
