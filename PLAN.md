X509 Lens — Complete the Cross-Platform X.509 Certificate Explorer

You are taking over an existing Go project called X509 Lens.

Your mission is to inspect, refactor, complete, test, and polish the application into a reliable, modern, cross-platform certificate and cryptographic object explorer.

X509 Lens should provide functionality comparable to the Windows Certificate Viewer, but with broader support for X.509 certificates, certificate chains, CRLs, PKCS#7, PKCS#12, and cryptographic keys.

Do not rewrite the project unnecessarily. Build upon the existing implementation, preserve working functionality, and improve the architecture where needed.

Work autonomously. Make reasonable engineering decisions without repeatedly asking for clarification.

1. First: Audit the Existing Project

Before implementing new functionality:

1. Inspect the complete repository.
2. Read the README and existing documentation.
3. Understand the current architecture and parsing pipeline.
4. Identify incomplete features, bugs, security weaknesses, and architectural limitations.
5. Inspect the existing UI and API contracts.
6. Review all dependencies.
7. Run:

go version
go mod tidy
go vet ./...
go test ./...
make desktop

Fix all compilation errors and failing tests.

The previous implementation was not fully build-verified due to dependency download restrictions. Assume that integration issues may exist.

Do not remove features simply to make the project compile.

Document significant architectural changes.

2. Technology Requirements

Primary language: Go.

Target platforms:

* macOS (ARM64 and AMD64)
* Windows (AMD64 and ARM64 where supported)
* Linux (AMD64 and ARM64)

Use the latest stable Go version supported by the development environment.

Prefer Go’s standard library and official Go-maintained modules.

Avoid unnecessary dependencies, especially cryptographic libraries.

Cryptographic backends

Feature	Primary implementation	Fallback
X.509 certificates	crypto/x509	None
Certificate verification	crypto/x509	Platform-specific APIs when necessary
PEM / DER	Standard library	None
PKCS#7 / P7B	Existing ASN.1 parser	Extend as necessary
CRLs	crypto/x509	None
OCSP	golang.org/x/crypto/ocsp	None
PKCS#12	golang.org/x/crypto/pkcs12	OpenSSL
Unencrypted PKCS#8	crypto/x509	None
Encrypted PKCS#8	OpenSSL	None
Legacy encrypted PEM	crypto/x509	OpenSSL if unsupported

OpenSSL must remain optional.

The application must start and operate normally without OpenSSL installed.

Never use OpenSSL for operations already adequately supported by the Go implementation.

For unsupported PFX encryption algorithms, attempt the OpenSSL fallback.

Distinguish unsupported algorithms from incorrect passwords and malformed files before attempting fallback.

3. Complete File Format Support

X509 Lens must recognize file formats using their contents rather than relying exclusively on extensions.

Certificates

Support:

* PEM X.509 certificates
* DER X.509 certificates
* PEM certificate bundles
* DER certificate collections where applicable
* Cross-signed certificates
* Self-signed certificates
* Intermediate CA certificates
* Root CA certificates

PKCS#7 / P7B

Support:

* DER PKCS#7 certificate bundles
* PEM PKCS#7 bundles
* SignedData containers containing certificates
* SignedData containers containing CRLs

Correctly handle certificates in arbitrary order.

Handle duplicate certificates without displaying redundant chain nodes.

CRLs

Support:

* PEM CRLs
* DER CRLs
* CRLs embedded in supported PKCS#7 containers

Expose:

* Issuer
* This Update
* Next Update
* CRL Number
* Authority Key Identifier
* Signature algorithm
* Revoked certificates
* Serial numbers
* Revocation dates
* Revocation reasons
* CRL extensions

Provide filtering and searching for revoked serial numbers.

Private and public keys

Support:

* PKCS#1 RSA private keys
* PKCS#8 private keys
* Encrypted PKCS#8 private keys
* SEC1 EC private keys
* PKIX public keys
* RSA public keys
* Ed25519 keys
* ECDSA keys
* Legacy encrypted PEM keys

Display only public metadata unless the user explicitly requests otherwise.

Never expose decrypted private-key material through the UI API.

PKCS#12 / PFX

Support password-protected PKCS#12 containers.

Extract:

* End-entity certificates
* Intermediate certificates
* Root certificates
* Private-key metadata
* Friendly names
* Local key identifiers, where supported

Try the native Go parser first.

Use OpenSSL only if the native parser cannot handle the PFX format or encryption algorithm.

Handle incorrect passwords clearly.

Avoid writing private keys to disk.

4. Certificate Chain Discovery and Construction

This is one of the application’s most important features.

The application must reconstruct certificate chains using multiple sources.

Certificate sources

Support:

1. Certificates loaded directly from files.
2. Certificates embedded in PEM bundles.
3. Certificates embedded in PKCS#7 containers.
4. Certificates extracted from PKCS#12.
5. Host operating-system trust stores.
6. Additional user-provided CA bundles.
7. Certificates downloaded through AIA.
8. Previously cached issuer certificates.

Each certificate must retain provenance information.

Possible sources:

FILE
PEM_BUNDLE
PKCS7
PKCS12
SYSTEM_STORE
CUSTOM_CA
AIA_ONLINE
AIA_CACHE

Chain-building behavior

Implement a chain builder independent of the chain validator.

The builder must:

* Identify potential issuer certificates.
* Match issuer and subject distinguished names.
* Use AKI/SKI where available.
* Verify issuer signatures.
* Handle multiple issuer candidates.
* Handle cross-signed certificates.
* Detect cycles.
* Handle missing intermediates.
* Construct alternative certification paths.
* Preserve incomplete paths for diagnostics.

Do not rely exclusively on x509.Verify() for chain discovery.

x509.Verify() should be used for standards-based validation of constructed paths.

Trust handling

Support:

* Host trust store
* Custom trust anchors
* Host + custom trust anchors
* Bundle-only path construction
* Bundle + host trust validation

Certificates supplied in PEM, P7B, or PFX containers must not automatically become trusted roots.

Only explicitly configured trust anchors should be trusted.

Host-specific behavior

Investigate platform-specific chain discovery capabilities.

On Windows, consider CryptoAPI chain-building APIs.

On macOS, consider Security.framework.

On Linux, support system CA bundles and configured certificate directories.

Prefer a portable Go implementation, but use platform-specific integration when it materially improves host trust behavior.

Keep platform-specific code isolated behind interfaces.

5. Automatic AIA Issuer Fetching

Automatically retrieve missing issuer certificates using Authority Information Access when network access is available.

Requirements:

* Support HTTP and HTTPS AIA endpoints.
* Parse DER and PEM issuer responses.
* Support PKCS#7 issuer responses where applicable.
* Cache downloaded certificates.
* Avoid repeated requests.
* Prevent circular fetching.
* Limit chain depth.
* Apply request timeouts.
* Apply response-size limits.
* Limit redirects.
* Restrict access to private, loopback, link-local, and metadata IP addresses by default.
* Validate DNS resolution and redirect destinations to reduce SSRF risk.

AIA certificates are untrusted issuer candidates.

They must never automatically become trust anchors.

Network access should be enabled by default but configurable.

The UI must clearly distinguish certificates loaded locally from certificates downloaded online.

6. OCSP Verification

Complete the native Go OCSP implementation.

Use:

golang.org/x/crypto/ocsp

Implement:

* OCSP request generation
* OCSP responder discovery
* HTTP requests
* OCSP response parsing
* Signature verification
* Delegated responder authorization
* Issuer identification
* Certificate serial-number matching
* ThisUpdate validation
* NextUpdate validation
* Clock-skew tolerance
* Response caching
* Revoked status reporting
* Unknown status reporting
* Network failure handling

Do not accept an OCSP response merely because it parses successfully.

Validate responder authorization and cryptographic signatures.

Never report a certificate as GOOD if the OCSP response is invalid, expired, or unauthenticated.

7. CRL Revocation Checking

Implement complete CRL-based revocation checking for supported CRL types.

Support:

* CRL Distribution Points
* Downloading CRLs
* Parsing CRLs
* CRL signature verification
* Issuer verification
* ThisUpdate / NextUpdate validation
* Revoked serial-number lookup
* Revocation reasons
* CRL caching

Investigate indirect CRLs, delta CRLs, and distribution-point scope.

If a feature is unsupported, report the limitation explicitly rather than silently returning GOOD.

Revocation policy

Use the following default strategy:

1. Attempt OCSP when an applicable responder is available.
2. If OCSP cannot provide a definitive valid result, attempt CRL checking.
3. If either method establishes revocation, report REVOKED.
4. If an applicable, authenticated, current response establishes non-revocation, report GOOD for that certificate.
5. Otherwise report UNKNOWN or UNAVAILABLE.

Preserve the result for every certificate in the chain.

Do not treat a network error as evidence that a certificate is valid.

Separate revocation status from certificate path validation.

8. Modern User Interface

The application should have a modern, professional interface inspired by:

* Windows Certificate Viewer
* macOS Keychain Access
* Modern developer tools
* Certificate inspection utilities

Avoid an outdated or overly complicated interface.

The application uses a Wails desktop UI without a localhost application server.

Prefer Wails if it substantially improves the desktop experience, but do not introduce unnecessary complexity.

Main layout

Use a three-panel layout:

┌───────────────────────────────────────────────────────────────┐
│ X509 Lens                  Open   Export   Verify   Settings   │
├──────────────────┬────────────────────────────────────────────┤
│                  │                                            │
│ FILE EXPLORER    │ CERTIFICATE DETAILS                        │
│                  │                                            │
│ ▼ bundle.p7b     │ Subject: example.com                       │
│   Root CA        │ Issuer: Intermediate CA                    │
│   Intermediate   │                                            │
│   example.com    │ Valid From: ...                            │
│                  │ Valid Until: ...                           │
│ ▼ identity.p12   │                                            │
│   Certificate    │ General | Details | Chain | Revocation     │
│   Private Key    │                                            │
│                  │                                            │
│ ▼ revoked.crl    │ Certificate Extensions                     │
│                  │                                            │
├──────────────────┴────────────────────────────────────────────┤
│ Status: Chain Trusted | OCSP Good | Online                    │
└───────────────────────────────────────────────────────────────┘

Required UI features

Implement:

* Dark mode
* Light mode
* Automatic system theme detection
* Drag-and-drop file opening
* Multiple-file loading
* File explorer tree
* Certificate chain visualization
* Certificate details
* Extension inspector
* ASN.1 / DER viewer
* CRL viewer
* Public/private key metadata viewer
* Search and filtering
* Copy-to-clipboard actions
* PEM/DER export
* Certificate fingerprints
* Password dialogs
* Network activity panel
* Validation result badges
* Responsive layout

Chain visualization

Show:

* Certificate subject
* Issuer
* Certificate source
* Trust status
* Expiration status
* Revocation status
* Signature validation
* Missing issuers
* Alternative paths

Users must be able to select any certificate in the chain and inspect it.

ASN.1 inspector

Implement a hierarchical ASN.1 viewer.

Display:

* Tag
* Class
* Length
* Offset
* Decoded value
* Raw hexadecimal bytes

Support expanding and collapsing nested structures.

Allow copying individual values and hexadecimal representations.

Handle malformed ASN.1 safely.

9. Validation Dashboard

Show independent validation results:

Certificate Validation
Chain Integrity       VALID
Host Trust            TRUSTED
Certificate Validity  VALID
Key Usage             VALID
Revocation            GOOD — OCSP ONLINE
Network Activity
AIA       Intermediate downloaded
OCSP      Response verified
CRL       Not required
Last Checked: 2026-10-20

Possible validation states:

VALID
INVALID
TRUSTED
UNTRUSTED
REVOKED
GOOD
UNKNOWN
UNAVAILABLE
NOT_CHECKED
EXPIRED
NOT_YET_VALID
UNSUPPORTED

Avoid reducing all verification outcomes to a single boolean.

Distinguish offline verification from online verification.

10. Network Activity Panel

Every network request initiated by X509 Lens must be visible.

Display:

* Request type: AIA, OCSP, CRL
* Destination URL
* HTTP status
* Duration
* Response size
* Cache hit/miss
* Verification outcome
* Error details

Allow users to disable network operations globally.

Never send private keys or passwords over the network.

11. Security Requirements

Treat all imported files and remote responses as untrusted input.

Implement:

* Input-size limits
* ASN.1 parsing limits
* Safe error handling
* Network timeouts
* Redirect restrictions
* SSRF protections
* Safe subprocess execution
* No shell command construction
* Password handling through pipes
* No passwords in command-line arguments
* No decrypted private-key files
* No private-key material in logs
* No private-key material in API responses
* No automatic execution of imported content

For OpenSSL subprocesses, use exec.CommandContext.

Never invoke a shell to construct OpenSSL commands.

Use stdin/stdout pipes.

Apply execution timeouts.

Avoid leaking secrets in error messages.

Local web server security

If retaining the browser-based interface:

* Bind exclusively to loopback by default.
* Protect API endpoints against unauthorized local callers.
* Validate Origin and Host headers.
* Avoid permissive CORS.
* Use unpredictable session credentials.
* Do not expose private keys through HTTP responses.
* Prevent arbitrary filesystem access.

12. Export Features

Support:

* Individual certificates as PEM
* Individual certificates as DER
* Certificate chains as PEM bundles
* Certificate chains as PKCS#7
* CRLs as PEM or DER
* Public keys as PEM
* Certificate fingerprints as text

Allow exporting:

* Leaf only
* Leaf + intermediates
* Complete chain
* Selected chain path

Do not export private keys automatically.

Any private-key export functionality must require explicit user interaction and appropriate safeguards.

13. Testing

Create comprehensive automated tests.

Parsing tests

Test:

* Valid PEM certificates
* Valid DER certificates
* Multiple certificates in one PEM
* Malformed PEM
* Malformed DER
* PKCS#7 containers
* PKCS#12 containers
* Incorrect PFX passwords
* Unsupported PFX encryption
* Encrypted PKCS#8
* RSA keys
* ECDSA keys
* Ed25519 keys
* CRLs

Chain tests

Generate test PKI hierarchies:

Root CA
   |
Intermediate CA
   |
Leaf Certificate

Test:

* Valid chains
* Missing intermediates
* Expired certificates
* Untrusted roots
* Cross-signed intermediates
* Multiple candidate paths
* Invalid signatures
* Invalid Basic Constraints
* Invalid Key Usage
* Name Constraints
* Circular issuer relationships

Revocation tests

Create local test OCSP responders and CRL endpoints.

Test:

* GOOD
* REVOKED
* UNKNOWN
* Expired OCSP response
* Invalid OCSP signature
* Unauthorized responder
* Expired CRL
* Invalid CRL signature
* Missing revocation endpoints
* Network timeout
* OCSP-to-CRL fallback

Do not depend on public OCSP responders for automated tests.

Security tests

Test:

* Malformed ASN.1
* Oversized inputs
* Recursive issuer chains
* SSRF attempts
* Redirect attacks
* Malformed network responses
* OpenSSL subprocess failures

Add fuzz tests for appropriate parsers.

Run:

go test ./...
go test -race ./...
go vet ./...

Run the race detector on supported platforms.

14. Build and Distribution

Provide reproducible builds for:

* macOS ARM64
* macOS AMD64
* Windows AMD64
* Windows ARM64, if supported
* Linux AMD64
* Linux ARM64

Use GitHub Actions for CI/CD.

CI should:

1. Run tests.
2. Run static analysis.
3. Build supported targets.
4. Package application artifacts.
5. Generate checksums.
6. Publish release artifacts when a release tag is created.

OpenSSL must remain optional.

If the GUI framework requires platform-specific build tools or CGO, document those requirements accurately.

Do not claim CGO_ENABLED=0 compatibility unless it has been verified.

15. Documentation

Update the README with:

* Project overview
* Screenshots or UI previews
* Supported formats
* Feature matrix
* Build instructions
* Platform requirements
* OpenSSL fallback behavior
* Chain-building behavior
* Trust-store behavior
* OCSP and CRL behavior
* Security considerations
* Known limitations

Add:

docs/
├── architecture.md
├── chain-building.md
├── revocation.md
├── file-formats.md
├── security.md
└── development.md

Document unsupported PKIX features explicitly.

16. Engineering Guidelines

Follow these principles:

1. Prefer correctness over cleverness.
2. Prefer standard Go libraries.
3. Keep parsing independent from the UI.
4. Keep network retrieval independent from validation.
5. Separate chain discovery from chain verification.
6. Separate certificate trust from revocation status.
7. Preserve certificate provenance.
8. Never silently downgrade security checks.
9. Avoid unnecessary dependencies.
10. Keep the application usable offline.
11. Make online operations transparent.
12. Avoid overengineering.
13. Keep the UI responsive during expensive operations.
14. Use contexts and cancellation for background operations.
15. Write tests for critical cryptographic behavior.

17. Execution Plan

Work in the following order.

Phase 1 — Stabilization

* Audit the repository.
* Fix build failures.
* Fix broken tests.
* Verify dependency compatibility.
* Identify security issues.

Phase 2 — Cryptographic Engine

* Complete file-format parsing.
* Complete PKCS#12 support.
* Complete encrypted-key support.
* Complete PKCS#7 support.
* Implement robust chain construction.

Phase 3 — Validation and Networking

* Complete OCSP verification.
* Complete CRL verification.
* Implement AIA retrieval.
* Implement caching.
* Add network security restrictions.

Phase 4 — User Interface

* Modernize the visual design.
* Complete all inspection panels.
* Add chain visualization.
* Add ASN.1 inspection.
* Add network activity.
* Add password dialogs.
* Add export functionality.

Phase 5 — Quality and Distribution

* Add comprehensive tests.
* Add fuzz testing.
* Add CI/CD.
* Build cross-platform artifacts.
* Update documentation.

18. Definition of Done

The project is complete when:

* The application builds successfully.
* All automated tests pass.
* The application works on Windows, macOS, and Linux.
* All supported file formats can be inspected.
* PKCS#12 works natively where supported.
* OpenSSL fallback works when necessary.
* Encrypted private keys are handled securely.
* Certificate chains can be reconstructed from multiple sources.
* Host trust verification works.
* AIA fetching works.
* OCSP verification works.
* CRL checking works.
* The UI clearly identifies online verification.
* Alternative certificate paths can be inspected.
* ASN.1 structures can be explored.
* Certificates and chains can be exported.
* The application remains usable without internet connectivity.
* OpenSSL is not required for ordinary operation.
* Documentation accurately reflects implemented functionality.

Do not mark incomplete features as implemented.

Do not claim cross-platform compatibility without testing or documenting the verification limitations.

19. Final Deliverables

Provide:

1. Complete updated source code.
2. Working application.
3. Passing automated tests.
4. Cross-platform build configuration.
5. Updated README.
6. Technical documentation.
7. A summary of changes.
8. A list of remaining limitations.
9. Exact build and test commands.
10. A release-ready ZIP containing the project.

Final Instruction

Start by inspecting the existing repository.

Do not merely propose an implementation plan.

Proceed with the actual development, making incremental changes and running tests throughout.

Prioritize a working, secure, polished application over theoretical completeness.

When a feature cannot be fully implemented, document the limitation and ensure the application reports it clearly.

Continue until the project meets the definition of done, or until you encounter a concrete blocker that cannot be resolved within the development environment.
