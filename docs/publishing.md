# Publishing X509 Lens

The stable cross-platform application identity is `io.github.billnice250.X509Lens`, the product name is **X509 Lens**, and the initial store version is `1.0.0` (`1.0.0.0` on Windows). Do not change the identity after reserving store records.

## Store listing copy

**Short description:** Inspect X.509 certificates, chains, CSRs, CRLs, bundles, keys, and TLS server endpoints.

**Description:** X509 Lens is a privacy-conscious desktop certificate viewer. Open local certificate and cryptographic-object files or inspect the chain presented by an HTTPS endpoint. Compare the server-presented chain with paths resolved through the host trust store, inspect extensions and critical constraints, review SHA-1/SHA-256 and SPKI fingerprints, and explore ASN.1 text and structure. Private keys are never exportable. Local files open without network access; online issuer and revocation checks are explicit.

**Category:** Developer Tools (preferred) or Utilities. **Price:** Free. **Age rating:** lowest/general audience; no user-generated content, purchases, advertising, or account system.

Screenshots still need to be captured on each store's target platform. Recommended shots: certificate General view, critical extensions in Details, ASN.1 text, Certification Path comparison for a TLS endpoint, and dark appearance.

## macOS

Reserve bundle ID `io.github.billnice250.X509Lens` in Apple Developer and create the matching macOS App Store record. App Store builds require App Sandbox; the prepared entitlements permit outbound TLS and user-selected file read/write.

Create a Mac App Store provisioning profile for `io.github.billnice250.X509Lens` and copy `.env.example` to `.env` with `MAC_APP_STORE_IDENTITY`, `MAC_INSTALLER_IDENTITY`, `MAC_PROVISIONING_PROFILE` and the App Store Connect API key values. Then:

```sh
make appstore            # arm64
make appstore ARCH=all   # arm64 and amd64
make appstore-upload     # validate and upload the latest package
```

The build embeds the provisioning profile and signs with its application identifier, team identifier and keychain access groups in addition to the sandbox entitlements. Direct-download releases should set `MAC_APP_IDENTITY` to a Developer ID Application identity and run `make production`; App Store packages are reviewed by Apple instead of notarized separately.

Before submission, verify file-open, drag/drop, export, TLS endpoint inspection, and cache behavior from a sandboxed signed build. Complete App Privacy and encryption/export-compliance answers in App Store Connect.

### Signed GitHub releases

Tags trigger dedicated Intel and Apple Silicon release jobs. Each job imports a Developer ID Application certificate, signs the app, notarizes and staples its DMG, and attaches the DMG and ZIP to the GitHub release. Configure these repository Actions secrets:

- `MACOS_BUILD_CERTIFICATE_BASE64`: the Developer ID Application certificate and private key exported as a password-protected `.p12`, then base64 encoded.
- `MACOS_BUILD_CERTIFICATE_PASSWORD`: the `.p12` export password.
- `APPLE_API_KEY_ID`: the App Store Connect API key ID.
- `APPLE_API_ISSUER_ID`: the App Store Connect issuer UUID.
- `APPLE_API_PRIVATE_KEY`: the full contents of the matching `AuthKey_*.p8` file.

For example, encode and upload the certificate without writing the base64 value to the terminal:

```sh
base64 -i DeveloperIDApplication.p12 -o DeveloperIDApplication.p12.base64
gh secret set MACOS_BUILD_CERTIFICATE_BASE64 < DeveloperIDApplication.p12.base64
gh secret set MACOS_BUILD_CERTIFICATE_PASSWORD
gh secret set APPLE_API_KEY_ID
gh secret set APPLE_API_ISSUER_ID
gh secret set APPLE_API_PRIVATE_KEY < AuthKey_XXXXXXXXXX.p8
```

The ordinary macOS matrix builds remain ad-hoc signed CI checks and are deliberately excluded from tagged GitHub releases.

## Windows

Reserve **X509 Lens** in Partner Center. Copy the reserved package identity and publisher values into the environment before running on a Windows SDK host:

```powershell
$env:WINDOWS_IDENTITY_NAME='value from Partner Center'
$env:WINDOWS_PUBLISHER='CN=value from Partner Center'
$env:WINDOWS_PUBLISHER_DISPLAY_NAME='publisher display name'
python scripts/build_desktop.py --store --arch amd64
python scripts/build_desktop.py --store --arch arm64
```

This produces architecture-specific MSIX packages with full-trust desktop and file-association declarations. The Microsoft Store re-signs accepted MSIX submissions. Run the Windows App Certification Kit, then upload both architectures (or combine them into an MSIX bundle) and provide the listing description, age rating, logos, and screenshots.

The NSIS installer remains available for direct distribution with `--installer`; it must be Authenticode-signed before a Store EXE submission or public download.

## Linux

The repository includes AppStream metadata, a desktop file, MIME declarations, and `snap/snapcraft.yaml`. Reserve `x509-lens` in the Snap Store, then build and test on Linux:

```sh
snapcraft
snap install --dangerous ./x509-lens_1.0.0_*.snap
snapcraft upload --release=stable ./x509-lens_1.0.0_*.snap
```

Test opening files from the home directory and removable media, TLS endpoint inspection, host trust resolution, and Wayland/X11 behavior under strict confinement.

Flathub preparation is intentionally incomplete: the project currently has no repository license file, and Flathub requires a declared redistributable license, source-only reproducible build inputs, AppStream metadata, and a human-authored submission manifest. Choose and add the project license before preparing a Flathub submission. Flathub's current policy also prohibits AI-generated submission manifests and automated submission activity, so that final manifest and pull request must be authored and submitted manually.

## Required publisher-owned work

- Confirm the name and identity are available in Apple Developer, Partner Center, and Snapcraft.
- Supply signing certificates, store accounts, tax/contact information, support and privacy-policy URLs.
- Choose and add a source license before Flathub submission.
- Capture platform-native screenshots and perform runtime testing on physical Windows and Linux systems.
- Complete each store's privacy, export-compliance, age-rating, and certification forms.
