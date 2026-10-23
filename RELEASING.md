# Releasing

Releases are trunk-based: there are no release branches or release PRs. Pushing a
`vX.Y.Z` tag on `main` publishes that version.

1. Commits on `main` follow [Conventional Commits](https://www.conventionalcommits.org)
   (`feat:`, `fix:`, `perf:`, `refactor:`, …). While the project is experimental at 0.0.x,
   every release may break the CLI and only the patch version moves: do not mark
   commits as breaking (no `!`, no `BREAKING CHANGE:` footer), because release-plz would
   bump the minor version to 0.1. Describe breaking changes in the commit body instead, and
   check the version `release-plz update` proposes before committing it.
2. Prepare the release on `main`: run `release-plz update` (or edit by hand) to bump the
   crate version in `Cargo.toml` and update `CHANGELOG.md`, then commit as `chore: release vX.Y.Z`
   and push.
3. Once CI is green, tag that commit and push the tag:
   `git tag vX.Y.Z && git push origin vX.Y.Z`.
4. The `Release` workflow runs the tests of the tagged commit, and
   checks that the tag is on `main`, matches the version in `Cargo.toml` and has a
   `CHANGELOG.md` entry. It then publishes the crate to crates.io and
   creates the GitHub Release from the changelog entry.

Protect `v*` tags with a tag ruleset so only maintainers can trigger a release.

## Trusted publishing

The repository uses Trusted Publishing (OIDC) on crates.io, so no long-lived token is stored.
crates.io can only trust a workflow for a package that already exists, so the first release
uses a short-lived secret token instead:

1. Create the `crates-io` environment in the repository settings
   (`gh api repos/ffalcinelli/astroceleste-cli/environments/crates-io -X PUT`).
2. **crates.io**: create an API token scoped to `publish-new` and `publish-update` for the
   crate `astroceleste`, with a short expiry, and store it as the
   `CARGO_REGISTRY_TOKEN` secret of the `crates-io` environment:
   `gh secret set CARGO_REGISTRY_TOKEN --env crates-io`.
3. Push the `v0.0.0` tag for the version in `Cargo.toml`.
4. Once the package exists on crates.io, add the trusted publisher on crates.io (crate settings →
   *Trusted Publishing*: this repository `ffalcinelli/astroceleste-cli`, workflow `release.yml`,
   environment `crates-io`).
5. Delete the `CARGO_REGISTRY_TOKEN` secret and revoke the token on crates.io. The workflow prefers
   OIDC whenever it is available.
