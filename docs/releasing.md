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
tag starts `.github/workflows/release.yml`. It verifies the version, then builds and packages on
each platform's runner and publishes a GitHub Release with the installers attached:

- **Windows** (`windows-latest`): `selfphoto-editor-<version>-windows-x64.msi` (installs
  `photocraft.exe`) and `selfphoto-editor-<version>-windows-x64-portable.zip`.
- **macOS** (`macos-latest`): `selfphoto-editor-<version>-macos-universal.dmg` (Intel + Apple silicon)
  and the `selfphoto-editor-cli` zip.
- **Linux** (`ubuntu-22.04`): `selfphoto-editor-<version>-linux-x86_64.deb` (Debian, Ubuntu,
  Mint, Pop!_OS, ...) and `selfphoto-editor-<version>-linux-x86_64.AppImage` (any distro).

Release notes are grouped using `.github/release.yml`. Tags containing a hyphen, such as
`v0.9.0-rc.1`, are published as pre-releases.

Without signing secrets the Windows build is unsigned and the macOS build is ad-hoc signed
(Gatekeeper rejects an ad-hoc build on other Macs); the packaging scripts warn and still produce
installers. Add `WINDOWS_CERTIFICATE`/`WINDOWS_CERTIFICATE_PASSWORD` (or the `AZURE_*` Trusted
Signing set), and/or `APPLE_CERTIFICATE`/`APPLE_CERTIFICATE_PASSWORD`/`KEYCHAIN_PASSWORD`/
`APPLE_ID`/`APPLE_PASSWORD`/`APPLE_TEAM_ID`, as repository secrets to sign and notarize.

## In-app update checks

The native desktop app checks the public GitHub latest-release endpoint in a background
thread once at launch, with no periodic checks or automatic retries. Preferences › General › Automatically
Check for Updates disables the startup check; Help › Check for Updates runs one manually.
Checks use a 15-second timeout and a 1 MiB response limit, need no GitHub token, and send
no document or personal data. Network failures are quiet during automatic checks and visible
in the manual check window. Concurrent requests share one check.
HTTPS validates certificates and host names against the operating system's trust store.

Tags must be semantic versions (`v0.8.0` or `0.8.0`); only published stable versions newer
than the installed workspace version trigger a notice. The notice opens the official release
page to choose an installer; installation remains a user action. Drafts and prereleases do
not trigger it. Web/headless frontends have no update transport. No release-workflow changes
are required: publishing a newer stable GitHub Release makes it discoverable.

## Verify or recover

- Check **Actions → Release** and the GitHub Release page. A failed version check, build or
  upload fails the workflow before publication.
- Compare a downloaded installer's checksum with the SHA-256 GitHub shows on the asset.
- On Windows, the MSI installs `photocraft.exe`; the portable zip also ships `photocraft-cli.exe`.
- Do not reuse or move a published version tag. Fix the issue, bump to the next patch version,
  and publish a new tag.
