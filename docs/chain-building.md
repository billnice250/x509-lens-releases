# Certificate paths

Sources include files, PEM bundles, PKCS#7, PKCS#12, verified host paths, and AIA. Human-readable source strings and duplicate provenance are preserved. AIA origins are prefixed `AIA:`; host results are prefixed `HOST:`. These origins are excluded from custom-anchor selection.

Discovery follows raw issuer/subject distinguished names, AKI/SKI when both exist, and `CheckSignatureFrom`. Candidate traversal maintains a path-local visited set, with depth 16 and at most 128 partial results per starting object. Cross-signed candidates can produce alternative paths. All imported certificates, including standalone CAs, are starting objects. Missing issuers remain diagnostic incomplete paths.

Trusted path validation uses `x509.Verify` with host roots when enabled and explicitly configured local anchors. Every Go-returned trusted path is retained. When validation fails, independent discovery preserves available candidate paths and the validation error. Structural signature integrity is distinct from trust, validity dates, and revocation; a partial path's valid edges do not establish complete PKIX validity.

The browser uses selected certificate fingerprints for anchors. The old API `customRoots` flag remains compatible and explicitly trusts all loaded local self-signed CAs when requested. It is hidden in the new UI. Never enable this legacy mode on untrusted files.

Host-root enumeration is limited by Go's platform APIs; cached system intermediates are not enumerated. Name-constraint, basic-constraint and usage restrictions are enforced for successful Go validation paths. Partial-path diagnostics are not a substitute for full validation. No selected application purpose/hostname is currently configured.
