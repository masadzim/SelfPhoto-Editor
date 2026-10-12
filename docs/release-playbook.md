# Release playbook

Use the feature and bug-report templates in GitHub Issues, make changes on a branch, and submit a
pull request. CI must pass before the pull request is merged into `main`.

For a release, update `[workspace.package] version` in `Cargo.toml` (and `Cargo.lock`) using
`cargo xtask version set <version>`, merge the change, then push an annotated `v<version>` tag to
`main`. The tag must match the workspace version. The release workflow runs the full workspace
test suite, builds the Linux x86_64 binaries, packages them with their licenses, calculates a
SHA-256 checksum, and publishes a GitHub Release with generated notes.

See [releasing.md](releasing.md) for the exact commands and supported release artifacts.
