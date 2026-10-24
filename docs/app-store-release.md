# X509 Lens — Mac App Store release document

This is the submission copy and release checklist for X509 Lens 1.0.0. Text under **Copy into App Store Connect** is customer-facing. Text marked **Publisher action** must be completed or confirmed by the account holder before submission.

## App identity

| Field                 | Value                              |
| --------------------- | ---------------------------------- |
| App name              | X509 Lens                          |
| Subtitle              | Certificate & PKI Inspector        |
| Bundle ID             | `io.github.billnice250.X509Lens` |
| Version               | 1.0.0                              |
| Minimum macOS version | macOS 12.0                         |
| Primary category      | Developer Tools                    |
| Secondary category    | Utilities                          |
| Price                 | Free                               |

## Product page metadata

### Promotional text

**Copy into App Store Connect:**

Inspect certificates, trust paths, CSRs, CRLs, keys, PKCS bundles, and TLS server chains in a focused, privacy-conscious native Mac app.

### Description

**Copy into App Store Connect:**

X509 Lens makes certificate inspection clear, focused, and accessible on the Mac.

Open a local certificate or cryptographic object, drop it into the app, or inspect the certificate chain presented by a TLS server. X509 Lens organizes complex PKI data into approachable General, Details, ASN.1, Missing, and Certification Path views.

INSPECT COMMON PKI FORMATS

• X.509 certificates in PEM and DER formats
• Certificate signing requests (CSRs)
• Certificate revocation lists (CRLs)
• PKCS#7 certificate and CRL bundles
• PKCS#12 identities
• RSA, EC, and Ed25519 public or private-key containers

UNDERSTAND CERTIFICATES AND PATHS

• Review subjects, issuers, validity periods, extensions, fingerprints, and public-key details
• Explore parsed ASN.1 text and structure
• Compare server-presented chains with paths resolved through the Mac trust store
• Inspect alternative and incomplete certification paths
• Review certificate and revocation-check diagnostics

PRIVACY-CONSCIOUS BY DESIGN

Local files open without a network request. Online issuer and revocation checks occur only when you explicitly choose Verify Online. Network activity is visible in the app and can be disabled in Settings.

X509 Lens does not install certificates or change your system trust settings. Private keys can be inspected for public metadata but cannot be exported from the app. Passwords used to unlock encrypted files are not retained.

X509 Lens is an inspection and diagnostic tool. It should not be used as the sole authority for security, authorization, or revocation decisions.

### Keywords

**Copy into App Store Connect:**

```text
certificate,x509,pki,tls,ssl,pem,der,csr,crl,ocsp,asn1,pkcs12,security,crypto,developer
```

### Version notes

App Store Connect does not show a “What’s New” field for the first release. Keep this copy for release announcements or a later metadata update:

**X509 Lens 1.0 introduces:**

• Inspection of certificates, CSRs, CRLs, PKCS bundles, and cryptographic keys
• Certification-path comparison using the Mac trust store
• Explicit online issuer and revocation checks with visible network activity
• TLS server certificate-chain inspection
• ASN.1, extension, fingerprint, and public-key detail views
• Native file opening, drag and drop, public export, and dark appearance

## Screenshot plan

Upload one to ten Mac screenshots. Use only the files ending in `-AppStore-2880x1800.jpg` under `brand/screenshots`; they are 2880 × 1800, 16:10 JPEGs without transparency.

Recommended order:

1. General certificate view — establish the main inspection experience.
2. Certification Path — show the trust path and selectable certificates.
3. Details — show searchable extensions and fingerprints.
4. TLS server inspection — demonstrate server-presented and resolved paths.
5. ASN.1 view — show low-level inspection for advanced users.
6. CSR view — show requested names and public-key information.
7. CRL view — show issuer, freshness, and revoked entries.
8. Bundle or PKCS#12 view — show object selection without exposing private material.
9. Missing-path diagnostics — demonstrate actionable diagnostics.
10. Dark appearance — show the native Mac presentation.

Do not include real private keys, passwords, personal certificates, private hostnames, email addresses, internal IP addresses, or customer data in screenshots.

## App Review information

### Contact

**Publisher action:** Enter a monitored name, email address, and telephone number in international format.

### Sign-in

No sign-in or demo account is required.

### Review notes

**Copy into App Store Connect:**

X509 Lens is a sandboxed, single-document certificate and PKI-object viewer. It does not require an account, subscription, in-app purchase, or special hardware.

To test without a local certificate file:

1. Launch the app.
2. Choose Server.
3. Enter `example.com:443` and inspect the presented TLS certificate chain.
4. Select certificates in Certification Path and inspect General, Details, and ASN.1 views.

To test a local file, choose Open or drag a PEM/DER certificate, CSR, CRL, PKCS#7 bundle, or PKCS#12 identity into the window. Password-protected files display an unlock prompt. Passwords are not retained. Export is limited to public material; private-key export is intentionally unavailable.

Local files are analyzed offline when opened. Verify Online explicitly enables issuer and revocation retrieval for that operation. The app displays resulting requests under Network activity. Network access can be disabled in Settings.

The app never installs certificates, modifies the Keychain, or changes system trust settings. Validation output is diagnostic and is not presented as an authorization decision.

## Privacy declaration

### Suggested App Privacy response

If the release build contains no analytics, advertising, telemetry, crash-reporting SDK, account system, or publisher-operated collection endpoint, select:

> No, we do not collect data from this app.

The app reads files selected by the user. When the user explicitly requests online verification or enters a TLS endpoint, it connects directly to the specified server and to certificate issuer or revocation URLs contained in public certificates. The publisher does not receive these requests.

**Publisher action:** Confirm the release binary and every third-party dependency match this statement. Add a public Privacy Policy URL in App Store Connect. The policy should disclose local-file handling, password handling, direct network requests, caching, and the absence of publisher analytics or tracking.

## Encryption export compliance

The bundle currently declares:

```xml
<key>ITSAppUsesNonExemptEncryption</key>
<false/>
```

The app uses standard cryptographic formats and algorithms for certificate inspection and relies on standard TLS for network connections. It does not implement proprietary or unpublished cryptography.

**Publisher action:** Answer Apple’s export-compliance questionnaire based on the final binary and obtain legal advice if needed. Do not rely on this release document as an export-control determination. If Apple determines documentation is required, upload it and add the compliance code Apple provides.

## Age rating and commercial declarations

Confirm the following for the submitted build:

- No user-generated content or social features.
- No gambling, contests, advertising, or in-app purchases.
- No unrestricted web browsing.
- No health, medical, or treatment functionality.
- No violent, sexual, or mature content.
- No account creation or login.
- No location, contacts, photos, microphone, camera, or Bluetooth access.

Select the lowest rating produced by Apple’s current questionnaire; do not manually claim a rating that differs from App Store Connect’s result.

## URLs and ownership

**Publisher action:** Supply and verify these public HTTPS pages before submission:

| Requirement                          | URL            |
| ------------------------------------ | -------------- |
| Support URL with contact information | `https://…` |
| Privacy Policy URL                   | `https://…` |
| Marketing URL (optional)             | `https://…` |

Publish `docs/privacy-policy.md` at the Privacy Policy URL. Use `docs/app-sandbox-information.md` as the App Review sandbox explanation or as source copy for the corresponding submission field.

Copyright field:

```text
2026 X509 Lens contributors
```

Confirm that the publisher owns or has permission to distribute the app name, icon, screenshots, source, dependencies, and all included assets.

## Final submission checklist

### Build

- [ ] Increment `CFBundleVersion` for every uploaded build.
- [ ] Confirm version 1.0.0 and bundle ID `io.github.billnice250.X509Lens`.
- [ ] Build the intended architecture package with `make appstore` or `make appstore ARCH=all`.
- [ ] Verify the provisioning profile and App Sandbox entitlements.
- [ ] Confirm the app bundle contains no `com.apple.quarantine` extended attributes.
- [ ] Validate and upload the package with `make appstore-upload` or Transporter.
- [ ] Select the processed build in App Store Connect.

### Functional review

- [ ] Launch the App Store-signed build on a clean Mac running the minimum supported macOS version.
- [ ] Test Open, Finder Open With, drag and drop, and repeated file-open events.
- [ ] Test certificate, CSR, CRL, PKCS#7, PKCS#12, and supported key formats.
- [ ] Test correct, incorrect, empty, and cancelled password flows.
- [ ] Test TLS server inspection and Verify Online.
- [ ] Test with networking disabled.
- [ ] Test Certification Path, Details, ASN.1, Missing, copy, and public export.
- [ ] Confirm private keys cannot be exported.
- [ ] Confirm no certificate is installed and system trust is unchanged.
- [ ] Test system, light, and dark appearance.

### App Store Connect

- [ ] Add the description, promotional text, keywords, categories, copyright, and URLs.
- [ ] Upload the 2880 × 1800 screenshots in the intended order.
- [ ] Complete App Privacy and publish the answers.
- [ ] Complete export compliance for the selected build.
- [ ] Complete the age-rating questionnaire.
- [ ] Enter App Review contact information and review notes.
- [ ] Confirm availability, pricing, tax, banking, and required agreements.
- [ ] Choose manual or automatic release.
- [ ] Submit the app version and monitor App Review messages.

## Apple field limits used by this document

- App name: 30 characters maximum.
- Subtitle: 30 characters maximum.
- Promotional text: 170 characters maximum.
- Description: 4,000 characters maximum.
- Keywords: 100 bytes maximum, comma-separated.
- App Review notes: 4,000 bytes maximum.
- Mac screenshots: 1280 × 800, 1440 × 900, 2560 × 1600, or 2880 × 1800; no alpha channel.

Recheck Apple’s current App Store Connect requirements before each release because submission fields and policies can change.
