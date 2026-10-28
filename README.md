# codecrafters-shell-builds

Standalone executables of [r0uxx/codecrafters-shell-python](https://github.com/r0uxx/codecrafters-shell-python),
a POSIX-style shell written in Python for the CodeCrafters "Build your own shell" challenge.
Nothing needs to be installed, Python included: it's a single file.

## Download

Every build is published under [Releases](https://github.com/r0uxx/codecrafters-shell-builds/releases/latest).
Pick the file for your system, or use the one-liner below.

| System | File |
|---|---|
| Linux (x86_64, glibc 2.35+: Ubuntu 22.04+, Debian 12+, Fedora 36+, Arch, Mint 21+) | `codecrafters-shell-linux-x86_64.tar.gz` |
| macOS, Apple Silicon (M1 and newer) | `codecrafters-shell-macos-arm64.tar.gz` |
| macOS, Intel | `codecrafters-shell-macos-x86_64.tar.gz` |
| Windows 10/11 (x64) | `codecrafters-shell-windows-x86_64.exe` |

### Linux and macOS

This downloads the right file for your machine, unpacks it and starts the shell:

```sh
curl -fsSL "https://github.com/r0uxx/codecrafters-shell-builds/releases/latest/download/codecrafters-shell-$(uname -s | tr A-Z a-z | sed s/darwin/macos/)-$(uname -m | sed s/aarch64/arm64/).tar.gz" | tar -xz && ./codecrafters-shell
```

Next time, just run `./codecrafters-shell` from the same folder. To be able to run it from anywhere:

```sh
mkdir -p ~/.local/bin && mv codecrafters-shell ~/.local/bin/
```

(`~/.local/bin` has to be on your `PATH`, it is by default on most Linux distros.)

Downloaded with a browser instead? Then unpack it first: `tar -xzf codecrafters-shell-*.tar.gz && ./codecrafters-shell`.
The macOS builds are signed and notarized by Apple, so they open without a Gatekeeper warning.

### Windows

In PowerShell:

```powershell
Invoke-WebRequest https://github.com/r0uxx/codecrafters-shell-builds/releases/latest/download/codecrafters-shell-windows-x86_64.exe -OutFile codecrafters-shell.exe; .\codecrafters-shell.exe
```

Downloaded with a browser instead, SmartScreen may warn about an unknown publisher since the Windows build isn't
signed: "More info" → "Run anyway".

## Things to try

```sh
echo hello | tac                  # pipes, tac prints lines in reverse
declare name=friend
echo "hi ${name}"                 # variables
ls > files.txt; tac files.txt     # redirects
sleep 3 &                         # background jobs
jobs
history                           # history, also with the up arrow
type tac                          # which commands are builtins
rickroll
```

Tab completes commands and file names. `exit` or Ctrl-D quits.

### Troubleshooting

- **The first start takes a second.** The executable unpacks itself into a temporary folder every time it starts.
- **It doesn't start at all on a locked-down Linux machine.** Some systems don't allow running programs from
  `/tmp`, point it somewhere else: `TMPDIR=~/.cache ./codecrafters-shell`.
- **`cannot execute binary file` / `Exec format error`.** There's no build for your processor
  (e.g. ARM Linux or a Raspberry Pi), see "From source" below.
- **`GLIBC_2.35 not found`.** Your Linux is older than the build supports, see "From source" below.

### From source

With [uv](https://docs.astral.sh/uv/) installed (it fetches the right Python by itself), on any system:

```sh
git clone https://github.com/r0uxx/codecrafters-shell-python && cd codecrafters-shell-python && uv run -m app.main
```

## Building

Actions → **Build executables** → **Run workflow** (optionally pick a branch/commit of the shell repo),
or from a terminal:

```sh
gh workflow run build.yml -R r0uxx/codecrafters-shell-builds
```

It builds on every OS, smoke tests each executable, and once all builds pass publishes them as a new release
(`build-<run number>`), which the download links above pick up automatically.

The build lives here, not in the shell repo, because CodeCrafters syncs that repo to GitHub and its sync
can't push workflow files (`.github/workflows/`), which blocks syncing altogether.

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

The build waits at most 20 minutes for Apple. A bare executable can't have its notarization stapled to it,
Gatekeeper looks it up online on first launch, so a build Apple is still working on is uploaded anyway and passes
Gatekeeper as soon as Apple accepts it. Check on submissions with `xcrun notarytool history`.
