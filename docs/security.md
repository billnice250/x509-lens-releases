# Desktop security boundaries

The native desktop app uses Wails bindings and an embedded frontend, with no localhost TCP listener or HTTP password API. Only native dialogs, launch arguments and OS file-open/drop events supply input paths. It reads one regular file, at most 8 MiB. The frontend can request exports of known public objects through a native save dialog; private keys cannot be exported. Imported strings are escaped and no remote HTML or URLs are rendered as executable content. Passwords are used only during unlocking and are not retained for later verification.

Desktop packaging registers Open With/Viewer capabilities without forcing defaults or installing trust anchors. macOS local builds use ad-hoc signatures. Developer ID signing/notarization and Windows Authenticode signing require release credentials not present in this workspace.

The following engine/network rules apply to issuer and revocation retrieval.

Imported files are limited to 8 MiB. ASN.1 traversal is bounded. AIA has five rounds and a seen-URL set. Candidate paths have depth/result caps. An analysis caps direct fetch/OCSP attempts at 64. These are defensive limits, not a comprehensive resource-exhaustion proof for all crypto parsers.

HTTP retrieval permits HTTP/HTTPS only and rejects URL credentials. The transport disables environment proxies, resolves destinations, rejects nonpublic/private/shared-address ranges, and dials the validated IPs directly. TLS still verifies the original hostname. Redirect destinations are revalidated and limited. The explicit private-network setting overrides the address restriction for diagnostic local networks. It is off by default.

Responses are limited to 2 MiB, clients time out after seven seconds, and connection establishment after five seconds. Request cancellation reaches fetches, OCSP and OpenSSL. Network logs include retrieval and verification events. Redirects and failed response reads do not yet have the same complete timing/size detail as successful GETs; this is a known observability gap.

Only encrypted input is passed through a 0600 temporary file to optional OpenSSL. Passwords go through stdin, subprocesses use CommandContext with a 15-second deadline, decrypted output is parsed in memory, and diagnostic stderr is never returned. Go cannot promise immediate memory erasure of passwords/keys. Passwords exist briefly in process memory and cross the local Wails binding during unlock.

Do not use the tool as a sole authorization/revocation authority. No private key export or external command execution based on imported content is supported.
