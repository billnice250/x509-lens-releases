# Formats and exports

Content detection attempts native X.509, certificate collections, CRLs, supported key encodings, PKCS#7 SignedData and a structural PFX header. Filename extensions do not determine parsing. PEM bundles may contain mixed supported public objects and private metadata. Duplicate certificate imports do not create duplicate nodes.

Native PKCS#12 handles the algorithms supported by the official `x/crypto/pkcs12` package. Only its typed NotImplementedError invokes OpenSSL; incorrect-password and malformed-input failures do not. Friendly-name and local-key-ID headers are retained when exposed by the decoder. Full bag semantics are not implemented.

Legacy PEM encryption uses Go's compatibility API; it is not recommended for creating new encrypted material. PKCS#8 encryption uses optional OpenSSL; PEM and DER encrypted input are accepted. OpenSSL passwords containing line breaks are rejected because the CLI uses line-delimited stdin input.

The UI exports individual certificate/CRL PEM or DER and public keys as PEM. Select leaf-only, leaf plus intermediates, or complete path for PEM/P7B export. If multiple paths are available, export asks for the path index. P7B export is certificate-only degenerate SignedData, with no signed content or CRLs. It does not export private keys. Leaf-plus-intermediates omits the final path node; inspect incomplete paths before choosing this mode.

ASN.1 inspection applies to certificate/CRL DER. It bounds depth to 32, nodes to 10,000, and input to 8 MiB. Hex previews show up to 256 bytes per node; full public DER remains in the raw panel. Basic string/time values are decoded; full semantic ASN.1 decoding and individual-node copy buttons remain outstanding.
