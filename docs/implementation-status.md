# PLAN.md implementation status

The plan is broader than this development build. Do not describe this as its completed definition of done.

Verified improvements include working native dependencies/builds, safer optional OpenSSL handling, content-based PFX detection, alternative chain diagnostics, explicit local anchors, transport DNS pinning, conservative CRL applicability, delegated OCSP responder validity, cancellation, per-analysis cache reuse, UI inspection/export additions, parser/security tests, and CI/release configuration.

Remaining work:

- Persistent OCSP caching, explicit platform intermediate-store adapters, user CA-directory/bundle settings.
- Complete PKCS#12 bag support and legacy provider combinations; fallback integration tests with OpenSSL absent and additional encryption variants.
- Full revocation distribution-point scope/reason coverage, indirect/delta CRLs (currently rejected), richer reason reporting in validation results.
- Application-purpose key usage, hostname verification, independently validated alternative candidate paths alongside trusted paths, richer cycle/missing-issuer diagnostics.
- Uniform per-request telemetry for redirects, OCSP transport status/size/timing, cache expiry metadata and verification association.
- Per-ASN.1-value copy actions, fuller decoded ASN.1 types, automated visual regression tests and screenshots. The single-file desktop view supersedes the planned multi-file explorer.
- Full cross-sign/name-constraint/cycle/unauthorized-responder/redirect/rebinding/malformed-response fixture matrix requested in PLAN.md. Existing tests are a foundation, not comprehensive coverage of that matrix.
- Windows/Linux runtime verification and CI execution, release signing/notarization and full installer acceptance checks. Platform packaging configuration is now implemented.

A source archive is supplied as a development snapshot; release readiness is not claimed. No browser automation surface was available for visual QA, and target Windows/Linux machines were not present. These checks must be completed before release.

## Desktop workflow update (0.2)

The user subsequently requested a native, installable single-file viewer. This replaces the earlier multi-file explorer direction for the desktop UI. Wails integration, native menus/dialogs, OS file-open handling, single-document state, compact bundle selection, password modal, native public exports, installation/association manifests and per-platform CI packaging have now been added. Desktop builds have platform GUI dependencies; older CGO-free browser builds are separate. See desktop.md and desktop-verification.md for current platform testing and packaging status.
