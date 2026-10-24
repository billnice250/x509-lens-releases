# Desktop verification — 2026-10-20

Environment: macOS ARM64, Go 1.26.5, Xcode SDK, Wails 2.17.0.

Passed:

- `go mod tidy`
- `go vet -tags desktop ./...`
- `go test -race -tags desktop ./...`
- `node --check internal/desktop/frontend/app.js`
- `python3 scripts/test_packaging.py` — Linux per-user install/uninstall exercised in isolated temporary directories, including spaces/dollar signs in installation paths and preservation of existing defaults/unrelated files.
- Native production macOS ARM64 app bundle build, with document type declarations and application icon.
- macOS packaging script end to end: ad-hoc codesign, signature verification, application ZIP, drag-to-Applications DMG, checksums.
- `CGO_ENABLED=0 GOOS=windows GOARCH=amd64 go build -tags desktop,production -ldflags '-H windowsgui' ...` and equivalent ARM64 cross-build.
- Desktop session tests: file replacement, no network on initial open, canceled/superseded operations, size/directory rejection, public PEM/DER/P7B export round-trips, invalid export rejection, encrypted-key unlocking, private-export rejection and metadata preservation through re-verification.

Native UI observations:

- X509 Lens launches in its own native window with application menus and no browser/localhost server.
- A certificate passed as a launch filename opens automatically. The title shows `test-cert.pem — X509 Lens`, General shows the expected subject/issuer/validity, untrusted self-signed status, and no network requests.
- Native Open panels appeared. Accessibility automation could not reliably complete file selection and later timed out navigating a tab. A process sample showed the app main thread idle in the normal AppKit event loop, not blocked on a Go operation. The final build uses the standard Wails file sheet with unrestricted macOS types/alias resolution. Manual acceptance of picker selection, drag/drop, save-dialog completion and password-dialog interaction remains necessary; automated session tests cover their backend behavior.

Windows and Linux native runtime/installer execution, CI execution, Developer ID notarization and Authenticode signing are not verified here. The Windows/Linux installer configuration is supplied for target-host testing. Previous CGO-free browser binaries are separate from the desktop app.

Restricted-environment commands used `GOCACHE=/private/tmp/certview-go-cache` and `GOMODCACHE=/private/tmp/certview-mod-cache`. The local Wails CLI was installed into `/private/tmp/certview-tools`; the packaging script accepts its path through WAILS_BIN.
