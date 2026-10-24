# X509 Lens — Microsoft Store release document

This is the Partner Center submission copy and release checklist for X509 Lens 1.0.0.0. Text under **Copy into Partner Center** is customer-facing. Text marked **Publisher action** must be completed or confirmed by the account holder.

## Product identity

| Field | Value |
| --- | --- |
| Product name | X509 Lens |
| Package type | MSIX desktop application |
| Package version | 1.0.0.0 |
| Supported architectures | x64 and ARM64 |
| Minimum Windows version | Windows 10, version 1809 (build 17763) |
| Recommended category | Developer tools |
| Secondary category | Utilities & tools |
| Pricing | Free |
| Account required | No |
| In-app purchases | None |

**Publisher action:** Replace the manifest placeholders with the exact identity values assigned by Partner Center:

- `WINDOWS_IDENTITY_NAME`
- `WINDOWS_PUBLISHER`
- `WINDOWS_PUBLISHER_DISPLAY_NAME`

The package identity and publisher must match Partner Center exactly.

## Store listing

### Short description

**Copy into Partner Center:**

Inspect X.509 certificates, trust paths, CSRs, CRLs, keys, PKCS bundles, and TLS server chains in a focused Windows desktop app.

### Description

**Copy into Partner Center:**

X509 Lens makes certificate inspection clear, focused, and accessible on Windows.

Open a local certificate or cryptographic object, drag it into the app, open it from File Explorer, or inspect the certificate chain presented by a TLS server. X509 Lens organizes complex PKI data into approachable General, Details, ASN.1, Missing, and Certification Path views.

Inspect common PKI formats

• X.509 certificates in PEM and DER formats
• Certificate signing requests (CSRs)
• Certificate revocation lists (CRLs)
• PKCS#7 certificate and CRL bundles
• PKCS#12 identities
• RSA, EC, and Ed25519 public or private-key containers

Understand certificates and paths

• Review subjects, issuers, validity periods, extensions, fingerprints, and public-key details
• Explore parsed ASN.1 text and structure
• Compare server-presented chains with paths resolved through the Windows trust store
• Inspect alternative and incomplete certification paths
• Review certificate and revocation-check diagnostics

Privacy-conscious by design

Local files open without a network request. Online issuer and revocation checks occur only when you explicitly choose Verify Online. Network activity is visible in the app and can be disabled in Settings.

X509 Lens does not install certificates or change your system trust settings. Private keys can be inspected for public metadata but cannot be exported. Passwords used to unlock encrypted files are not retained.

Built for desktop workflows

Use native Open and Save dialogs, drag and drop, File Explorer’s Open with command, keyboard shortcuts, and system, light, or dark appearance. Export public certificates, public keys, chains, fingerprints, and certificate-only bundles when you need to share or compare results.

X509 Lens is an inspection and diagnostic tool. It should not be used as the sole authority for security, authorization, or revocation decisions.

### Feature list

**Copy into Partner Center as separate feature entries without the leading Markdown hyphens:**

- Inspect X.509, PEM, DER, CSR, CRL, PKCS#7, PKCS#12, and supported key files
- Explore certificate fields, extensions, fingerprints, and ASN.1 structure
- View certification paths and alternative or incomplete chains
- Inspect TLS server-presented certificate chains
- Perform explicit issuer and revocation checks with visible network activity
- Export public certificates, keys, chains, fingerprints, and certificate-only bundles
- Open files from the app, drag and drop, or File Explorer
- Use system, light, or dark appearance
- Work without an account, subscription, advertising, or tracking

### Search terms

**Copy into Partner Center:**

```text
certificate viewer
x509 inspector
PKI tools
TLS certificate
PEM DER
CSR CRL
ASN.1
```

### What’s new in this version

Leave this field blank for the first Store submission. For a later 1.0 update, this draft is available:

Initial release of X509 Lens for Windows.

- Inspect certificates, CSRs, CRLs, PKCS bundles, and cryptographic keys.
- Compare certification paths using the Windows trust store.
- Inspect TLS server certificate chains.
- Review ASN.1, extensions, fingerprints, and public-key details.
- Run explicit online issuer and revocation checks with visible network activity.
- Open files from File Explorer and export selected public material.

## Store images

Microsoft requires at least one screenshot and accepts up to ten desktop screenshots. Desktop screenshots must be PNG files, at least 1366 × 768 pixels and no larger than 50 MB. Capture the submitted app running on Windows rather than reusing macOS screenshots.

Recommended order and captions:

1. **General certificate view** — “Review certificate identity, validity, fingerprints, and verification results at a glance.”
2. **Certification Path** — “Explore trusted, alternative, incomplete, and server-presented certificate paths.”
3. **Details** — “Search certificate fields and inspect extensions, constraints, and encoded values.”
4. **TLS server inspection** — “Inspect the chain presented by a TLS server and compare resolved paths.”
5. **ASN.1 view** — “Explore parsed ASN.1 text and structure without leaving the app.”
6. **CSR inspection** — “Review requested names, signature details, and public-key information.”
7. **CRL inspection** — “Review issuer, freshness, signature status, and revoked entries.”
8. **Bundle inspection** — “Move between certificates and public objects in PKCS bundles.”

Keep critical content in the top two-thirds because Store overlays can cover the lower portion. Do not include private keys, passwords, personal certificates, private hostnames, internal IP addresses, email addresses, or customer data.

Recommended Store artwork:

- 300 × 300 PNG app tile icon.
- Package logos already generated for `Square44x44Logo`, `Square150x150Logo`, and `StoreLogo`.
- Optional promotional art only if it accurately represents the Windows application.

## Properties and declarations

### Category

Use **Developer tools** as the primary category. If Partner Center offers a secondary category, use **Utilities & tools**.

### Privacy

The manifest declares internet access and the app can read files selected by the user. Provide a privacy policy URL even if Partner Center does not mark it as mandatory.

Suggested answer, subject to final-binary verification:

> X509 Lens does not collect or transmit personal information to the publisher. Files are analyzed locally. User-requested TLS inspection and online certificate verification connect directly to the specified server or public certificate infrastructure endpoints.

Publish `docs/windows-privacy-policy.md` at a stable public HTTPS URL and enter that URL in Partner Center.

### Age rating

Complete the IARC questionnaire accurately. For the current build, confirm:

- No violence, sexual content, controlled substances, gambling, or user-generated content.
- No unrestricted web browsing or social communication.
- No advertising or in-app purchases.
- No account creation or sharing of precise location.

Use the rating generated by IARC rather than claiming a rating manually.

### Accessibility

**Publisher action:** Test keyboard navigation, visible focus, text scaling, contrast, screen-reader labels, and high-contrast behavior on Windows before making accessibility declarations. Do not claim unsupported accessibility features.

### System requirements

- Windows 10 version 1809 or later, or Windows 11.
- x64 or ARM64 processor matching the installed package.
- Microsoft Edge WebView2 Runtime.
- Internet access only for TLS server inspection and explicit online verification.

## Restricted capability justification

The MSIX manifest declares:

```xml
<rescap:Capability Name="runFullTrust"/>
```

**Copy into certification notes if requested:**

X509 Lens is a Win32 desktop application built with Go and Wails. The `runFullTrust` restricted capability is required to launch its packaged desktop executable using `Windows.FullTrustApplication`. The application uses native desktop file dialogs, drag and drop, File Explorer file associations, local cryptographic parsing, and user-requested public-file export.

The application does not request elevation, install a service or driver, add startup tasks, modify security settings, install trust anchors, access the Windows credential store, or export private keys. It does not run a local server. Outbound networking is limited to user-entered TLS endpoints and certificate issuer or revocation URLs used during explicit online verification.

## Certification notes

**Copy into Partner Center:**

X509 Lens is a single-document certificate and PKI-object viewer. No account, purchase, subscription, product key, or special hardware is required.

Suggested certification test:

1. Launch X509 Lens.
2. Choose Server.
3. Enter `example.com:443`.
4. Inspect the displayed certificates using General, Details, ASN.1, and Certification Path.
5. Alternatively, choose Open and select a PEM or DER certificate.
6. Choose Verify Online to exercise outbound issuer and revocation retrieval.
7. Choose Export to confirm that only public material is available.

Password-protected files display an unlock prompt. Passwords are not retained. Local files open without network access. The app never installs certificates, modifies the Windows trust store, or exports private keys. Verification results are diagnostic rather than authorization decisions.

The application depends on the Microsoft Edge WebView2 Runtime. On supported current Windows installations this is normally present. Test the package on a clean supported Windows installation before submission.

## Package preparation

Set these environment values from the product identity page in Partner Center:

```powershell
$env:WINDOWS_IDENTITY_NAME='value from Partner Center'
$env:WINDOWS_PUBLISHER='CN=value from Partner Center'
$env:WINDOWS_PUBLISHER_DISPLAY_NAME='publisher display name'
```

Build both packages on a Windows host with the Windows SDK:

```powershell
python scripts/build_desktop.py --store --arch amd64
python scripts/build_desktop.py --store --arch arm64
```

The expected outputs are architecture-specific `.msix` packages under `dist/desktop`. Upload both packages in the same submission so Partner Center can deliver the correct architecture. Microsoft re-signs accepted Store MSIX packages; a public CA certificate is not required for the Store MSIX submission path.

Increment the four-part package version for every submission. Never reuse a package identity/version combination.

## Windows validation checklist

- [ ] Build on Windows using the final Partner Center identity values.
- [ ] Inspect the packaged `AppxManifest.xml` and verify identity, publisher, version, architecture, and display name.
- [ ] Confirm the `Windows.Desktop` target and minimum version are correct.
- [ ] Run the Windows App Certification Kit against each architecture package.
- [ ] Install and launch the MSIX on clean x64 and ARM64 Windows systems as applicable.
- [ ] Confirm install, upgrade, uninstall, and reinstall behavior.
- [ ] Confirm the app runs as a standard user without elevation.
- [ ] Test behavior when WebView2 is present and absent.
- [ ] Test Open, drag and drop, native dialogs, and File Explorer Open with.
- [ ] Confirm registered file types open correctly without changing existing defaults automatically.
- [ ] Test supported certificate, CSR, CRL, PKCS#7, PKCS#12, and key formats.
- [ ] Test correct, incorrect, empty, and cancelled password flows.
- [ ] Test TLS server inspection, Verify Online, and network-disabled behavior.
- [ ] Test Certification Path, Details, ASN.1, Missing, copy, and public export.
- [ ] Confirm private keys cannot be exported.
- [ ] Confirm no certificate is installed and the Windows trust store remains unchanged.
- [ ] Test system, light, dark, high-contrast, keyboard, and screen-reader behavior.

## Partner Center submission checklist

- [ ] Reserve and confirm the product name and package identity.
- [ ] Complete pricing and availability.
- [ ] Complete properties, category, system requirements, and capability declarations.
- [ ] Complete the IARC age-rating questionnaire.
- [ ] Upload both x64 and ARM64 MSIX packages.
- [ ] Resolve every package validation and WACK failure.
- [ ] Add the description, features, search terms, support contact, and privacy-policy URL.
- [ ] Upload at least four Windows desktop screenshots and the 300 × 300 Store icon.
- [ ] Add certification notes and the `runFullTrust` justification.
- [ ] Choose publication timing in Submission options.
- [ ] Submit for certification and monitor Partner Center messages.

## Traditional EXE submission (alternative to MSIX)

MSIX is the preferred Store package for X509 Lens. If the Partner Center product
was instead created as an EXE/MSI app, submit the architecture-specific NSIS
installer ending in `-setup.exe`. Do not submit the portable application
executable or the ZIP archive as an installer.

Build the installer on Windows with the Partner Center publisher display name:

```powershell
$env:WINDOWS_PUBLISHER_DISPLAY_NAME='exact publisher display name from Partner Center'
python scripts/build_desktop.py --arch amd64 --installer
python scripts/build_desktop.py --arch arm64 --installer
```

For each EXE package in Partner Center, set **Silent install parameters** to
`/S` (capital `S`). NSIS treats switch names as case-sensitive. The installer
then creates one **Installed apps / Add or remove programs** entry containing
the name `X509 Lens`, the configured publisher, and the app version. Its quiet
uninstall command also uses `/S`.

Before uploading, verify each signed installer on a clean matching-architecture
Windows system:

```powershell
.\X509Lens-1.0.0-windows-amd64-setup.exe /S
Get-ItemProperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Uninstall\X509Lens'
& "$env:LOCALAPPDATA\Programs\X509Lens\Uninstall.exe" /S
```

The install and uninstall commands must finish without visible installer UI,
the registry result must include `DisplayName`, `Publisher`, and
`DisplayVersion`, and uninstall must remove the application directory and the
single Installed apps entry.

Recheck Microsoft’s current Partner Center and Store policy requirements before each release because submission fields and certification rules can change.

## Partner Center limits used by this document

- Description: 10,000 characters maximum.
- What’s new: 1,500 characters maximum; blank for the first submission.
- Product features: up to 20 entries, 200 characters per entry.
- Short description: 1,000 characters maximum; keep it under 270 characters for best display.
- Keywords: up to 7 entries, 40 characters each, and no more than 21 unique words total.
- Desktop screenshots: PNG, at least 1366 × 768 pixels, no more than 50 MB; up to 10 screenshots.
- Screenshot captions: 200 characters maximum.

