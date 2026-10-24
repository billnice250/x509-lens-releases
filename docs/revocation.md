# Revocation

Revocation is separate from trust. For each nonterminal certificate in a path, X509 Lens identifies its issuer, attempts native OCSP, and falls back to CRLs if OCSP is not definitive. Missing issuers produce UNAVAILABLE. Terminal anchors do not have their own issuer-based revocation check. An untrusted one-node object reports an unavailable issuer.

OCSP uses `x/crypto/ocsp.CreateRequest` and `ParseResponseForCert`. It checks signatures, serial matching, delegated responder authorization and certificate validity. A future ThisUpdate tolerates five minutes of skew; expired NextUpdate is rejected. Responses with no NextUpdate are limited to 24 hours. Invalid time intervals are rejected. GOOD is never inferred from a transport or parsing success alone.

CRLs require an issuer name/AKI match, issuer signature, a ThisUpdate no more than five minutes in the future, and a nonzero unexpired NextUpdate. Local CRLs are checked before distribution-point downloads. Serial matches produce REVOKED and include revocation time. Inspected CRL entries also expose reason codes.

Delta CRLs, issuing-distribution-point scope, indirect certificate-issuer entries, and unknown critical extensions are rejected as unsupported for status determination. This deliberately avoids claiming GOOD from an unsupported scoped list. RFC 5280 distribution point coverage and reason partition aggregation remain incomplete.

GET response bytes and per-certificate/issuer revocation results are reused for the duration of an analysis. AIA responses also persist in the user cache directory for up to 24 hours; reanalysis rechecks revocation over the network. There is no persistent OCSP cache. Disabled network still permits local CRL evaluation. Statuses are diagnostic; aggregate GOOD covers checked path members, not terminal anchors or all possible certification paths.
