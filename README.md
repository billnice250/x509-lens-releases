# X509 Lens

A single-file certificate viewer with a native desktop window, inspired by Windows Certificate Viewer. Open a file from Finder/Explorer, use a native Open dialog, or drop it into the window. General, Details, Missing, and Certification Path tabs keep the review focused; bundles use a compact object picker.

## Desktop installation

- **macOS:** open the disk image in `dist/desktop` and drag X509 Lens into Applications. The local build is ad-hoc signed, not notarized.
- **Windows:** the per-user setup installer registers Open With handlers and uninstall support. Requires Microsoft WebView2.
- **Linux:** extract the desktop archive and run `python3 install.py`; installs a launcher and MIME associations for your user. Requires GTK 3 / WebKitGTK 4.1.

X509 Lens registers as a viewer while preserving existing default apps. Choose it under Open With, or explicitly set your preferred default in the OS. It does not install certificates into your trust store.

```sh
# Build the native desktop app and package it for this host:
python3 scripts/build_desktop.py
# Windows installer (NSIS required):
python scripts/build_desktop.py --installer
```

See [desktop installation, associations and development](docs/desktop.md) for platform requirements and commands. Desktop builds use Wails v2.17 with native webviews and dialogs; macOS/Linux require CGO. The app does not start a localhost HTTP server.

Files initially open offline. Verify Online explicitly checks issuer/revocation endpoints; settings let you keep verification offline and choose system/light/dark appearance. Encrypted files use a password prompt, and public exports use a native Save dialog.

## Supported formats

| Format | Support |
|---|---|
| X.509 PEM, DER, concatenated DER, PEM bundles | Native |
| PKCS#10 CSRs, PEM or DER | Native subject, requested names, public key and signature inspection |
| PKCS#7 SignedData certificates and CRLs, PEM or DER | Native extraction; CMS message signatures are not verified |
| X.509 direct CRLs | Native inspection and signature/freshness checks |
| PKCS#1, SEC1, PKCS#8, PKIX RSA/EC/Ed25519 keys | Native public metadata only |
| Legacy encrypted PEM | Native Go decryption |
| PKCS#12 | Native `x/crypto/pkcs12`, optional OpenSSL fallback for explicitly unsupported native features |
| Encrypted PKCS#8, PEM or DER | Optional OpenSSL |
| Export | Individual PEM/DER, public-key PEM, chain PEM and certificate-only P7B |

Formats are detected from contents. PFX passwords, including empty passwords, are attempted natively. Incorrect native passwords and malformed containers do not trigger OpenSSL. Enter a password before loading encrypted files or reanalyzing. The password field is cleared after successful analysis, so enter it again before reanalyzing encrypted objects.

## Trust and networking

Host trust uses Go's platform trust support. Imported bundle roots are untrusted by default. Downloaded AIA objects cannot be selected as anchors. Validation never automatically trusts fetched issuers.

Path discovery matches issuer/subject names, AKI/SKI, and signatures, bounds depth, avoids cycles, and preserves alternative partial paths. Go validates trusted paths. Each certificate, including standalone CAs, has path results. Application-specific key usage is explicitly NOT_CHECKED; this is an inspection tool without a selected TLS/email/code-signing purpose.

AIA and revocation are online by default. Disable **Enable network** for offline use; local CRLs can still be checked. OCSP signatures, certificate serials, delegated responder authorization/validity, and freshness are checked before reporting GOOD. Nondefinitive OCSP falls back to authenticated direct CRLs. Unsupported CRL scope, delta/indirect CRLs, and unknown critical extensions cannot establish GOOD. Network failures never establish validity.

Network requests have timeouts and size/redirect/request-count limits. DNS addresses are validated and dialed directly; private, loopback, link-local, and shared-address destinations are blocked unless explicitly enabled. HTTP proxies from the environment are not used. Downloaded GET responses and revocation results are cached within an analysis. Public AIA responses are also cached in the user cache directory for up to 24 hours and remain untrusted issuer candidates. Network events show requests and verification outcomes.

## Build and verification

Requires Go 1.26 or later. The cryptographic engine uses `golang.org/x/crypto`; desktop builds additionally use Wails and its dependencies. Optional OpenSSL must be on PATH for encrypted PKCS#8 and unsupported PFX containers.

```sh
make build       # native desktop app and host package
make test
make test-race
make desktop     # native desktop app and host package
make check       # fmt, vet and tests
```

Equivalent Go checks are `go mod tidy`, `go vet ./...`, `go test ./...`, and `go test -race ./...`. Use the Wails build described in `docs/desktop.md` for the application.

A restricted environment may need a writable cache, e.g. `GOCACHE=/private/tmp/certview-go-cache`. Tests create local OCSP responders; permit loopback listeners.

CI runs tests and race checks on macOS, Linux and Windows, then builds native desktop artifacts for supported AMD64 and ARM64 runners. Release tags publish those artifacts. Only macOS ARM64 execution was exercised in this development environment; Windows/Linux runtime validation still requires their target hosts.

## Security and remaining limitations

Private-key bytes are never exposed to the Wails frontend. OpenSSL runs without a shell, with a deadline and password on stdin. Only encrypted input is placed in a mode-0600 temporary file; decrypted output stays in memory.

This is an improved development build, **not a completed production PKIX validator**. See [implementation status](docs/implementation-status.md) for the remaining PLAN.md work. In particular:

- No persistent OCSP cache or native Windows/macOS intermediate-store enumeration.
- No indirect/delta/scoped CRL support, full revocation policy engine, or application-specific key-usage validation.
- PKCS#7 is a certificate/CRL extractor, not a general CMS validator; BER indefinite lengths and attribute certificates are unsupported.
- OpenSSL fallback does not implement every legacy provider/encryption combination.
- Native desktop launch and initial certificate display are verified on macOS; Windows/Linux desktop runtime and installer checks remain for CI/target hosts.
- Automated tests cover important cases, not the complete adversarial PKIX matrix in PLAN.md. Windows/Linux runtime checks remain for CI or target machines.

Technical docs: [desktop app](docs/desktop.md), [publishing](docs/publishing.md), [architecture](docs/architecture.md), [chain discovery](docs/chain-building.md), [revocation](docs/revocation.md), [formats](docs/file-formats.md), [security](docs/security.md), [development](docs/development.md).
