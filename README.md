# codecrafters-shell-builds

Builds standalone executables of [r0uxx/codecrafters-shell-python](https://github.com/r0uxx/codecrafters-shell-python)
for Linux, macOS (Apple Silicon and Intel) and Windows.

The build lives here, not in the shell repo, because CodeCrafters syncs that repo to GitHub and its sync
can't push workflow files (`.github/workflows/`), which blocks syncing altogether.

## Building

Actions → **Build executables** → **Run workflow** (optionally pick a branch/commit of the shell repo).
Each run uploads one artifact per OS; download them from the run's summary page.

Or from a terminal:

```sh
gh workflow run build.yml -R r0uxx/codecrafters-shell-builds
```

## Running a downloaded build

- **Linux / macOS:** unzip the artifact, then `tar -xzf codecrafters-shell-*.tar.gz && ./codecrafters-shell`
- **Windows:** unzip the artifact and run `codecrafters-shell.exe`. SmartScreen may warn about an
  unknown publisher: "More info" → "Run anyway".

## macOS signing and notarization

When these repository secrets are set, the macOS builds are signed with a Developer ID certificate
and notarized by Apple, so they run on other Macs without a Gatekeeper warning. Without them they're unsigned.

| Secret | Value |
|---|---|
| `MACOS_CERTIFICATE` | Base64 of the exported "Developer ID Application" `.p12` (certificate + private key) |
| `MACOS_CERTIFICATE_PASSWORD` | Password of that `.p12` |
| `APPLE_ID` | Apple account email |
| `APPLE_TEAM_ID` | 10-character Team ID (developer.apple.com → Account → Membership details) |
| `APPLE_APP_PASSWORD` | App-specific password from account.apple.com |
