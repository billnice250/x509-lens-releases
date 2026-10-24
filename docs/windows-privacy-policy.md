# X509 Lens Privacy Policy for Windows

**Effective date:** October 23, 2026  
**Last updated:** October 23, 2026

X509 Lens is a Windows desktop application for inspecting X.509 certificates and related cryptographic objects. This policy explains how information is handled when you use the app.

## Summary

X509 Lens does not require an account and does not include advertising, analytics, tracking, or publisher-operated telemetry. The publisher does not collect personal data through the app.

Files selected in X509 Lens are processed locally on your computer. The app does not upload those files to the publisher.

## Files and cryptographic material

X509 Lens can open certificates, certificate signing requests, certificate revocation lists, PKCS bundles, and supported key containers that you explicitly select, open from File Explorer, or drop into the app.

The app processes selected files locally. It does not install certificates, modify the Windows trust store, or change system trust settings. Private-key bytes are not exposed to the app’s interface and private keys cannot be exported through the app.

For supported encrypted files, a password may be entered to unlock and inspect the file. Passwords are used for that operation and are not retained as an account credential or sent to the publisher. Like other sensitive values processed by software, a password may exist briefly in application memory while the operation is performed.

## Network activity

Opening a local file does not require a network request. When you explicitly inspect a TLS server or select **Verify Online**, X509 Lens may connect directly to:

- the server address you entered;
- certificate issuer URLs contained in a certificate;
- Online Certificate Status Protocol responders; and
- certificate revocation list distribution URLs.

Those servers receive ordinary connection information, such as your IP address, as part of providing their services. Their handling of information is governed by their own policies. These requests are not routed through or collected by the X509 Lens publisher.

Network activity is displayed in the app. Network verification can be disabled in Settings.

## Local caching

To reduce repeated downloads, publicly available issuer responses may be stored in the app’s local cache for up to 24 hours. Cached issuers remain untrusted candidates and are not installed into the Windows trust store. Revocation results are reused only during the current analysis and are not maintained as a persistent OCSP history.

Local app data can be removed by uninstalling the application and, where applicable, removing its local application-data or cache directory.

## Data collection and sharing

X509 Lens does not use publisher-operated analytics, advertising networks, tracking technologies, user accounts, or cloud synchronization. The publisher does not sell or share data obtained through the app because the app does not transmit user data to the publisher.

## Children’s privacy

X509 Lens is a technical utility and is not directed to children. The app does not knowingly collect personal information from children.

## Security

The Microsoft Store package declares internet access and the full-trust desktop capability required to run the Win32 application. X509 Lens does not request administrator elevation, install services or drivers, or modify system security settings. No security measure can guarantee absolute protection, but the app is designed to minimize access and keep file analysis local.

## Changes to this policy

This policy may be updated when the app’s functionality or legal requirements change. The updated version will show a revised date at the top of this page.

## Contact

For privacy questions or requests, contact:

**Publisher:** [INSERT LEGAL NAME OR COMPANY]  
**Email:** [INSERT SUPPORT OR PRIVACY EMAIL]  
**Website:** [INSERT SUPPORT WEBSITE URL]  
**Postal address:** [INSERT ADDRESS IF REQUIRED]
