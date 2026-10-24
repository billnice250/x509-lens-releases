# Architecture

X509 Lens is a Wails desktop application. The tagged root `main.go` launches the native window and embeds the static frontend from `internal/desktop/frontend`; there is no browser edition or local application server.

`internal/core` separates import (`core.go`, `native.go`, `openssl.go`), ASN.1 inspection (`asn1.go`), OCSP (`ocsp.go`), discovery, validation, revocation, and HTTP transport. Discovery and validation remain methods in the same package but have separate responsibilities. Future larger changes should split these files along those boundaries.

Objects retain a SHA-256 identifier, source, provenance, public metadata, and exportable public material. Duplicate certificates merge provenance rather than creating redundant nodes. Private-key identifiers are fingerprints of the public SPKI, never of secret material. PKCS#12 bag headers are attached when the native decoder exposes them.

Significant changes from the inherited implementation include transport-level address pinning, bounded alternative path traversal, per-certificate OCSP/CRL result reuse, native-password error classification, in-memory decrypted OpenSSL output, and bounded ASN.1 inspection.

## Single-document desktop app

The tagged root `main.go` launches Wails through `internal/desktop`. Its embedded static frontend needs no npm build or framework. Native menus, title bars, open/save dialogs, drag/drop, macOS file-open events and second-instance command-line routing wrap the existing core engine. `wails.json` and platform packaging register file types without replacing default handlers automatically.

`internal/viewer.Session` owns one document. Open replaces the previous document, uses bounded regular-file reads, cancels superseded operations, and does local checks only. A bundle may contain several objects but does not create a multi-file workspace. Verification creates a fresh engine from public PEM and preserves private-key metadata. Passwords are consumed per unlock call and not retained in the session. Export selects known public objects only and never accepts an arbitrary input path from the frontend.

The desktop frontend calls bound Go methods directly, without a TCP listener or HTTP upload API. Native file dialogs and OS open events supply file paths. Network operations stay in the existing hardened core transport. Platform-specific AppKit code is isolated behind the desktop build tag.
