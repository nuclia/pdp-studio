# PDP Studio

Public distribution repository for PDP Studio CLI releases and, in the future,
GitHub Actions. This repository does not contain the PDP Studio application
source code.

The `pdp` CLI provides command-line access to PDP Studio, including project
configuration export and import for automation.

## Install the CLI

The initial release is planned to include:

| Platform | Architecture | Package target |
|---|---|---|
| macOS | Apple Silicon / ARM64 | `aarch64-apple-darwin` |
| macOS | Intel / x86-64 | `x86_64-apple-darwin` |
| Linux (glibc 2.39+) | x86-64 | `x86_64-unknown-linux-gnu` |
| Linux (glibc 2.39+) | ARM64 | `aarch64-unknown-linux-gnu` |

Windows packages are not included.
No permanent CLI release is available here yet; the instructions below apply
once the first release is published.

1. Open [Releases](https://github.com/nuclia/pdp-studio/releases) and choose a
   version. Read its release notes for requirements and compatibility.
2. Download both `pdp-v<VERSION>-<TARGET>.tar.gz` and its matching
   `.sha256` file into the same directory.
3. In that directory, set the selected version and target (Apple Silicon shown):

```sh
VERSION=X.Y.Z
TARGET=aarch64-apple-darwin
ARCHIVE="pdp-v${VERSION}-${TARGET}.tar.gz"
shasum -a 256 -c "$ARCHIVE.sha256"
```

On Linux, use `sha256sum -c "$ARCHIVE.sha256"` instead.
Continue only if checksum verification reports `OK`:

```sh
tar -xzf "$ARCHIVE"
mkdir -p "$HOME/.local/bin"
install -m 755 "pdp-$TARGET/pdp" "$HOME/.local/bin/pdp"
export PATH="$HOME/.local/bin:$PATH"
pdp --version
pdp --help
```

Add the same `PATH` setting to your shell startup file if needed.
Installing another version at this location replaces the existing executable.
If macOS blocks execution, contact your support channel for approved
installation guidance; checksum verification is not a substitute for code signing.

## Connect with a NUA key

Create a NUA key in the account owning your project. Ask an account owner to
grant the key **Platform Manager**, plus project **Reader** access for export
or **Writer** access for import.

In the terminal you will use, clear any old authentication or context overrides:

```sh
unset PDP_NUA_KEY PDP_TOKEN PDP_ACCOUNT PDP_API_URL PDP_ZONE
pdp ctx set --zone ''
pdp auth apikey --account '<owning-account-uuid>'
```

Enter the key's **secret value** at the hidden prompt and the region supplied
for your environment. `--account` is the account UUID, **not the NUA key ID**.
For a different API environment, also pass `--api-url '<regional-api-url>'`.

```sh
pdp ctx show
pdp auth status
pdp projects export-config '<source-project-id>' --output-dir ./project-config
```

Confirm the effective API URL and account in `ctx show` and `auth_type: nua_key`
in `auth status`. These commands describe local configuration; the export
checks server access. The export directory must not already exist.

To import, use a key with access to the destination and select its owning account:

```sh
pdp projects import-config '<destination-project-id>' --input-dir ./project-config
```

**Import changes destination configuration and may trigger reindexing.**
Configuration exports do not include resource contents or operational data.
Treat exported files as private project information.

For interactive browser authentication instead, use `pdp auth login`.
Run `pdp <command> --help` for command-specific options.

## GitHub Actions

Published GitHub Actions are planned separately; none are available here yet.

## Support

Use your existing PDP Studio support channel. Do not post API keys, credentials,
or private project configuration in this public repository.
