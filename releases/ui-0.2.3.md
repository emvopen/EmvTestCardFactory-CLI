<!-- release
source: be31befb5ec260b3a76f991f9033f105550a0628
channel: staged
-->

# Card Factory UI 0.2.3

First desktop UI release, for macOS ARM64 (Apple Silicon) and Windows x64.
It is published as a staged prerelease: update discovery ignores it until it is promoted to stable.

## Platform assets

- `card-factory-ui-0.2.3-macos-aarch64.dmg` for Apple Silicon Macs.
- `card-factory-ui-0.2.3-windows-x64.msi` for Windows x64, installed per user.
- Each installer has a matching SHA-256 file.
- Intel Mac and Windows ARM64 installers are not published. The automatic Source code archives contain public documentation, not the application.

## Features

- Card: one Issuer Security Domain AID field shared by every card operation, an authenticated applet inventory for PPSE and all six payment schemes, install and uninstall directly from the inventory, and read-only card memory.
- Security domain: the AID defaults to `A000000151000000`, is validated inline and is passed to GlobalPlatformPro as `--connect`, so cards whose ISD is not the default-selected application, such as Android host card emulation, are reached. An invalid value disables the card actions before any key is requested. Editing it clears results read through the previous AID, and key dialogs show `Security domain <AID>`.
- GlobalPlatform keys: named key profiles stored in the system credential vault (macOS Keychain, Windows Credential Manager), or keys entered for a single operation. Every card operation asks for its keys explicitly.
- Profile: per-tag editing of profile overrides, explicit TLV import and export, and personalization of the installed payment and PPSE applets with digest read-back.
- Transactions: per-tag terminal data and deterministic transactions with guarded results.
- Artifacts: the same signed CAP cache and selection as the CLI, with explicit checks and downloads.
- Diagnostics and Settings: host version, release source and UI update checks; System, Light and Dark appearance.

## Compatibility and upgrade

- Host version: 0.2.3; the macOS installer records it as native version 1.2.3 because jpackage rejects a zero major version. Update and CAP compatibility use 0.2.3.
- Bundled CAP package version: 0.23; `caps-0.23.0` remains the current signed CAP bundle. The UI and the CLI share the user's CAP cache and selection.
- Requires install manifest schema 2; personalization protocol 3; applet contract 1.
- Bundled Azul Zulu Java 25 runtime and GlobalPlatformPro 26.06.04; no separate Java or `gp` installation is required. PC/SC reader drivers are still needed.
- A card whose ISD is not `A000000151000000` needs its AID in the Card page's field. The `GP_AID` environment variable has no effect.
- The installers are not signed or notarized by the Card Factory project. macOS Gatekeeper and Windows SmartScreen may warn on first launch.

## Build provenance and validation

- Source commit: `be31befb5ec260b3a76f991f9033f105550a0628`.
- Desktop UI CI passed on the source commit.
- CI: TODO — record the `Desktop UI build and release` run that created this draft; its native jobs run tests and offline distribution and window checks, and the collector verifies the four-file installer and checksum set.
- The maintainer tested every desktop UI function by hand after the security-domain change and reported all of them working.
- Open before stable publication: installer install, upgrade and uninstall lifecycle, signing and notarization, and the physical-card gates of the M9 acceptance checklist.

## Installer verification

| Platform | Installer | SHA-256 |
| --- | --- | --- |
| macos-aarch64 | `card-factory-ui-0.2.3-macos-aarch64.dmg` | `TODO` |
| windows-x64 | `card-factory-ui-0.2.3-windows-x64.msi` | `TODO` |
