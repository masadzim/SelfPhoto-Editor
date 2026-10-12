# Releasing SelfPhoto Editor

Feature work and bug fixes are merged through pull requests. The repository runs CI on pull
requests and pushes to `main`; the pull request template asks for a user-facing summary, change
type and verification. Use GitHub Issues to report a bug or propose a feature.

## Prepare a release

1. Merge the feature and bug-fix pull requests into `main` after CI passes.
2. Choose the next semantic version: patch for compatible bug fixes, minor for compatible
   features, and major for breaking changes.
3. Update the workspace version and lockfile:

   ```sh
   cargo xtask version set 0.7.1
   cargo test --workspace --locked --features heif
   cargo fmt --all -- --check
   ```

4. Commit and push the version bump to `main`, then create and push an annotated tag:

   ```sh
   git tag -a v0.7.1 -m "SelfPhoto Editor v0.7.1"
   git push origin v0.7.1
   ```

The tag must exactly match `[workspace.package] version` in `Cargo.toml`. Pushing a valid `v*`
tag starts `.github/workflows/release.yml`. It reruns the workspace tests, builds the desktop app
and CLI for Linux x86_64, and publishes a GitHub Release with generated notes, a tarball and
`SHA256SUMS.txt`. Release notes are grouped using `.github/release.yml`. Tags containing a
hyphen, such as `v0.8.0-rc.1`, are published as pre-releases.

The current automated binary release targets Linux x86_64. Other platform packaging remains
available in the repository but is not published by this fork's release workflow.

## Verify or recover

- Check **Actions → Release** and the GitHub Release page. A failed test, version mismatch,
  build, or upload fails the workflow before publication.
- Verify a downloaded archive with `sha256sum -c SHA256SUMS.txt`.
- Do not reuse or move a published version tag. Fix the issue, bump to the next patch version,
  and publish a new tag.
