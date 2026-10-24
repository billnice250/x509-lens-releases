# Development and distribution

For desktop builds, installers and file associations, see [desktop.md](desktop.md). Builds require platform GUI tools/CGO on macOS and Linux.

Run from the repository root:

```sh
go version
go mod tidy
make check                     # go fmt, go vet, go test
make test-race
make build                     # native desktop app and host package
make run                       # Wails development mode
# Run fuzz targets separately:
go test ./internal/core -run '^$' -fuzz FuzzInspectDER -fuzztime 10s
go test ./internal/core -run '^$' -fuzz FuzzLoad -fuzztime 10s
```

Go 1.26.5 on macOS ARM64 was used locally. Use GOCACHE pointing to a writable directory if required by the sandbox. OCSP tests use ephemeral local HTTP responders and need loopback-listener permission. OpenSSL-dependent fixture tests need OpenSSL on PATH; native-only tests do not need it. The included fixtures use password `secret` and are test material.

CI verifies three host operating systems, builds AMD64/ARM64 for each, creates SHA-256 checksums, uploads artifacts, and publishes binaries on pushed release tags. Local builds do not establish Windows/Linux runtime behavior. Native desktop app bundles and installation/association tooling are now provided separately; Developer ID notarization and Windows Authenticode signing are still release tasks. Source ZIPs exclude generated binaries, caches, and host metadata; binary artifacts and checksums live in dist.

The workspace did not contain a Git repository, so no commits or pull request were created. Earlier browser visual QA was blocked by unavailable browser tooling; current native testing is recorded in desktop-verification.md.
