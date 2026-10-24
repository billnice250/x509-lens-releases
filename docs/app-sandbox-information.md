# X509 Lens App Sandbox Information

**App:** X509 Lens  
**Bundle ID:** `io.github.billnice250.X509Lens`  
**Version:** 1.0.0  
**Platform:** macOS 12 or later

This document explains the App Sandbox capabilities used by X509 Lens for App Review.

## Purpose of the app

X509 Lens is a single-document viewer for X.509 certificates and related public-key infrastructure objects. Users can open a local file, drag a file into the application, open a supported file from Finder, inspect a TLS server certificate chain, and export selected public information.

The application does not install certificates, write to the Keychain, alter system trust settings, expose a local network server, or export private keys.

## Sandbox entitlements

### `com.apple.security.app-sandbox`

**Value:** `true`

The application runs inside the macOS App Sandbox. It does not request temporary exception entitlements.

### `com.apple.security.files.user-selected.read-write`

**Value:** `true`

This entitlement is required for files explicitly chosen by the user through native Open and Save dialogs, Finder’s Open With mechanism, or drag and drop.

Read access is used to inspect the selected certificate or cryptographic-object file. Write access is used only when the user explicitly chooses Export and selects a destination through the native Save dialog. Exports are limited to public material, including public certificates, public keys, certificate chains, fingerprints, and certificate-only PKCS#7 bundles. Private keys cannot be exported.

The app does not scan arbitrary folders or request unrestricted filesystem access. Input files are limited to regular files and are subject to an application size limit.

### `com.apple.security.network.client`

**Value:** `true`

Outbound network access supports two user-facing features:

1. Inspecting the certificate chain presented by a TLS server address entered by the user.
2. Explicit online verification, which may retrieve issuer certificates, OCSP responses, or certificate revocation lists from URLs contained in public certificates.

Opening a local file does not initiate online verification. The user must select **Verify Online** for issuer and revocation retrieval. Network activity and outcomes are displayed in the app, and network verification can be disabled in Settings.

The application does not listen for inbound network connections, run a localhost HTTP server, use environment HTTP proxies, or connect to a publisher analytics or advertising service.

## Sensitive-data handling

- Selected files are analyzed locally and are not uploaded to the publisher.
- Passwords for encrypted files are used only to perform the requested unlock operation and are not retained.
- Private-key bytes are not exposed to the user interface.
- Private-key export is intentionally unavailable.
- Public issuer responses may be cached in the app container for up to 24 hours.
- The app does not install trust anchors or treat downloaded issuers as trusted roots.

## Suggested App Review test

No account or demo credentials are required.

1. Launch X509 Lens.
2. Choose **Server**.
3. Enter `example.com:443`.
4. Inspect the server-presented certificates in General, Details, ASN.1, and Certification Path.
5. Alternatively, choose **Open** and select a PEM or DER certificate.
6. Choose **Verify Online** to exercise outbound issuer and revocation retrieval.
7. Choose **Export** to confirm that macOS presents a user-selected Save dialog and that only public material is offered.

## Entitlements source

The submitted application is signed with the entitlements declared in `packaging/macos/app-store.entitlements`:

```xml
<key>com.apple.security.app-sandbox</key>
<true/>
<key>com.apple.security.network.client</key>
<true/>
<key>com.apple.security.files.user-selected.read-write</key>
<true/>
```

No additional sandbox exceptions are requested.

## Publisher contact

**Publisher:** [INSERT LEGAL NAME OR COMPANY]  
**Review contact:** [INSERT CONTACT NAME]  
**Email:** [INSERT CONTACT EMAIL]  
**Telephone:** [INSERT INTERNATIONAL-FORMAT PHONE NUMBER]
