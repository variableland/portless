# Releasing @variablelab/portless

Releases are driven by the version in `packages/portless/package.json`, the same mechanism upstream uses. The workflow in `.github/workflows/release.yml` runs on every push to `main` and on manual dispatch:

1. It compares the `version` in `packages/portless/package.json` with the latest version on npm.
2. If they differ, it builds and runs `npm publish --provenance` from `packages/portless`.
3. It then creates the GitHub release `v<version>` with the notes found between `<!-- release:start -->` and `<!-- release:end -->` in `CHANGELOG.fork.md`.

Pushing a new version to `main` is what publishes. There is no separate approval step, so treat a merge to `main` that changes the version as a release.

## One-time setup

- Publishing uses [npm trusted publishing](https://docs.npmjs.com/trusted-publishers): the `@variablelab/portless` package on npm trusts the `release.yml` workflow of `variableland/portless` running in the `Release` environment, and the job exchanges its GitHub OIDC token for a short-lived npm token. No long-lived token is stored. To re-create the relationship (interactive 2FA):

  ```bash
  npm trust github @variablelab/portless --file release.yml --repo variableland/portless --env Release --allow-publish
  ```

- The `Release` GitHub environment exists. Add required reviewers there if you want a manual gate before every publish.
- The `NPM_TOKEN` secret used for the first release (0.0.1) is no longer read by the workflow. Revoke it on npmjs.com and delete the secret after the first successful OIDC publish.

## Cut a release

1. Branch from `main`: `git checkout -b release/v0.0.2`.
2. Bump `version` in `packages/portless/package.json`.
3. Add an entry at the top of `CHANGELOG.fork.md` wrapped in `<!-- release:start -->` and `<!-- release:end -->`, and remove the markers from the previous entry. Record which upstream version the release is based on.
4. Run `pnpm build && pnpm test`, then `cd packages/portless && npm publish --dry-run` to review the tarball.
5. Open a PR, get CI green, merge to `main`. The release workflow publishes and creates the tag and release.

`prepublishOnly` copies `README.md`, `LICENSE`, and `NOTICE` into `packages/portless/`. The copies are gitignored; delete them after a local dry run so they are not confused with the root files.

## Versioning

Fork versions are independent from upstream's. `0.0.1` is the first release, based on upstream `v0.15.6` plus path-based routing. Each changelog entry records the upstream version it tracks.
