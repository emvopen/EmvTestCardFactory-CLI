<!-- release
source: 19ff4f7ca4e7a1812ae3eb4dfaa19165e582f0bc
channel: stable
-->

# Card Factory CLI 0.2.3

Card management release for macOS ARM64 (Apple Silicon), macOS x64 (Intel), and Windows x64.
It adds explicit Issuer Security Domain selection, so the CLI reaches cards whose security domain is not the default-selected application.

## Platform assets

- Available platforms: macOS ARM64 (Apple Silicon), macOS x64 (Intel), and Windows x64.
- Each platform ZIP has a matching SHA-256 file.
- Download the named ZIP and its SHA-256 file. The automatic Source code archives contain public documentation, not the application.

## Changes since 0.2.2

### Added

- `--isd-aid` on `applet status`, `applet install`, `applet uninstall` and `profile provision`. These commands now pass `--connect <AID>` to GlobalPlatformPro right after `--reader`, so it selects the Issuer Security Domain by AID instead of relying on it being the card's default-selected application. The default is `A000000151000000`, the same value `card memory` already used. This reaches cards that answer a SELECT without an AID with an error, such as an Android phone emulating a card through host card emulation, where Android answers `6F00` and GlobalPlatformPro's discovery previously stopped with "Could not SELECT default selected".
- The value must be 5–16 bytes of hexadecimal and is normalized to uppercase. An invalid value exits with usage status 2 before any reader is resolved, any key is prompted for or any process is started. `--help` of each command lists the option and its default.
- Dry runs of `applet install`, `applet uninstall` and `profile provision` show the `--connect` value. When GlobalPlatformPro fails during registry inspection or personalization, the CLI's error names the security domain it selected and points to `--isd-aid`.

### Changed

- **The default security domain is now always selected explicitly.** A card whose ISD has another AID, such as the older GlobalPlatform default `A000000003000000`, previously worked through GlobalPlatformPro's discovery and now needs `--isd-aid` on every command above. When the AID is wrong, GlobalPlatformPro's SELECT fails, typically with `6A82`.
- **The `GP_AID` environment variable no longer has any effect.** GlobalPlatformPro gives `--connect` precedence over it, and the CLI always sends `--connect`. Replace `GP_AID` in scripts with `--isd-aid`.
- `card memory` now shares the same `--isd-aid` option, validation and help text. Its behavior and default are unchanged.
- `applet install` checks replacement dependencies before authorizing a replacement. Installation stops when an existing target has the wrong registry type or its instance source package is missing or different. `--replace-payment` and `--replace-ppse` additionally require the expected applet class only, no additional dependent instances and source-package evidence for every application, and PPSE replacement is blocked while another known payment instance is present or its identity is uncertain. The replacement flags do not override these checks; inspect the registry before retrying.
- `doctor` probes the GlobalPlatformPro version with `--version`, which never opens a card.
- `update check` keeps selecting only `cli-*` releases. Desktop UI (`ui-*`) releases published in the same repository are never offered to the CLI.

### Build and release

- Releases and update discovery now use `emvopen/EmvTestCardFactory-CLI`. This release was rebuilt after the organization transfer; pre-transfer versions are retired.

- The bundled Java runtime is now built from the Azul Zulu 25 JDK instead of Temurin 25, the same distribution the desktop UI bundles. The build disables JDK auto-detection so the runtime image cannot come from the Temurin JDK preinstalled on the runners.
- The applet, inventory, memory, personalization and transaction logic the CLI used moved into a shared host module, which the desktop UI also uses. Apart from the changes listed above, CLI behavior is unchanged.

### Not changed

- No new CAP release is published with this CLI release; `caps-0.23.0` remains the signed CAP release. The bundled applet package version remains 0.23.
- GlobalPlatformPro remains pinned to 26.06.04.

## Features

- Install, personalize and test supported EMV test-card applets.
- Bundled Java 25 runtime, GlobalPlatformPro, common PPSE and six payment-scheme CAPs.
- Signed CAP update discovery, downloads, local cache and explicit artifact selection.
- CLI update checks with manual upgrade guidance.
- Applet compilation and maintainer signing tools are excluded from this distribution.

## Compatibility and upgrade

- Host version: 0.2.3; bundled CAP package version: 0.23.
- Requires install manifest schema 2; personalization protocol 3; applet contract 1.
- CAP target: Java Card Classic 3.0.5. Card memory and crypto support still require validation on the intended hardware.
- Cached CAP releases declaring minHostVersion 0.2.0 and maxHostVersionExclusive 0.3.0 stay compatible. Upgrading from 0.2.0, 0.2.1 or 0.2.2 needs no CAP re-download or re-selection.
- Pre-transfer versions are retired. Download this release directly, verify its checksum, extract the complete archive into a separate directory, and run `bin/card-factory doctor`. Old clients may reject the transferred repository URLs; do not rely on their update discovery.
- Downloading/selecting CAPs does not update a card; replacement and personalization remain explicit operations.
- Review the two security-domain changes above before upgrading automation: cards whose ISD is not `A000000151000000` need `--isd-aid`, and `GP_AID` is ignored.
- The ZIP has not been project-signed or notarized for macOS.

## Build provenance and validation

- Source commit: `19ff4f7ca4e7a1812ae3eb4dfaa19165e582f0bc`.
- Java Card SDK submodule: `700ec80afdda210a0e62fb6a151a9cddc1acd244`.
- Update repository: `emvopen/EmvTestCardFactory-CLI`.
- CI: [CLI rebuild 36268752803](https://github.com/emvopen/EmvTestCardFactory/actions/runs/36268752803) passed on all three platforms after the repository migration; each runner ran `:core:test :artifacts:test :card-io:test :cli:test :applet:test` and `:cli:buildCliRelease`, which includes the bundled-runtime smoke test.
- In the original ISD implementation session, `:host:test` (81 tests) and `:cli:test` (92 tests) passed; the new tests cover the default and custom `--connect` value, invalid values stopping before reader or key access, dry-run output and help text.
- The maintainer tested every CLI function by hand after the change, including the new security-domain selection, and reported all of them working.
- Publication validation: all three rebuilt ZIPs matched their SHA-256 sidecars and expected extraction roots. Each embeds host version 0.2.3 and update repository `emvopen/EmvTestCardFactory-CLI`; each runtime release file declares Java 25.0.4.
- CAP and install-manifest trees match across all three platforms and are byte-identical to their corresponding pre-migration 0.2.3 draft. The SDK submodule update changes only its README.
- The extracted macOS ARM64 application reports 0.2.3; `applet status --help` exposes `--isd-aid`; `doctor --scheme visa` verifies the bundled runtime, GlobalPlatformPro 26.06.04 and manifest checksums, and reports the organization update source. No reader was detected and no card operation was performed.
- macOS ARM64 runtime declares minimum macOS 11.0. Intel macOS and Windows binaries passed their native CI smoke tests; they were not executed locally on the publishing Mac. The maintainer confirmed native Windows package installation, upgrade and removal for 0.2.3. Browser quarantine was not revalidated in this ZIP publication.

## Usage

See the [installation guide](https://github.com/emvopen/EmvTestCardFactory-CLI/blob/main/docs/INSTALLATION.md), the [command guide](https://github.com/emvopen/EmvTestCardFactory-CLI/blob/main/docs/CLI.md#select-the-security-domain) and the [CLI/CAP update guide](https://github.com/emvopen/EmvTestCardFactory-CLI/blob/main/docs/UPDATES.md).

## Platform archive verification

| Platform | Bundled Java | ZIP SHA-256 |
| --- | --- | --- |
| macos-aarch64 | 25.0.4 | `03a0c4660ec3eb9798f3bcd977d121b0a567e825dbeb9a5d7fefd266b5ef22ec` |
| macos-x64 | 25.0.4 | `74035a7b7afd77096b325716cd4d4939f177103363087dcd86c1a1dd588ce19c` |
| windows-x64 | 25.0.4 | `2a5900aa45ddee557fa96f53c0ab3994e0467908cc4a26746d8e55f7b06a212e` |
