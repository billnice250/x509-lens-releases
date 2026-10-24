# X509 Lens desktop

X509 Lens uses a native application window built with stable Wails v2.17. It has no localhost TCP server, browser launch, or HTTP password API.

## Install and open files

**macOS 12 or later:** open the disk image and drag X509 Lens into Applications. The app is also available as a ZIP. Right-click a certificate and choose Open With → X509 Lens. To make it the default for an extension, use Finder → Get Info → Open with → X509 Lens → Change All. The bundle registers Viewer/Alternate roles; it does not replace Keychain Access as your default automatically. Local builds are ad-hoc signed, not Developer ID signed/notarized; public distribution still requires those release credentials.

**Windows 10/11:** run the per-user setup installer, which installs to LocalAppData, adds a Start Menu entry and uninstall support, and registers Open With handlers. Set your preferred default in Windows Default Apps or Open With. Existing defaults are preserved. The Microsoft WebView2 runtime is required; the build uses Wails' official download/prompt strategy when it is missing. The portable ZIP runs without association registration. Installers are configured in CI; Windows runtime and installer behavior have not been tested on this Mac.

**Linux:** extract the desktop archive and run `python3 install.py`. This installs for the current user without sudo, adds an application launcher, icon, MIME declarations and Open With support. It preserves existing default applications. To uninstall, run `python3 ~/.local/share/certview/install.py --uninstall` (or the corresponding XDG_DATA_HOME path). Runtime libraries: GTK 3 and WebKitGTK 4.1. The native build must match the target architecture. Linux desktop/runtime installation has not been executed on this Mac.

Associated types: `.cer`, `.crt`, `.pem`, `.der`, `.p7b`, `.p7c`, `.p12`, `.pfx`, `.crl`, `.key`, `.pub`. Recognition still uses file contents; extensionless files can be opened through the dialog. macOS file filtering is deliberately unrestricted to avoid UTType misclassification of PEM files.

## Review workflow

- Open a file, drop it into the window, or open it from Finder/Explorer. Opening a second file replaces the first document.
- General shows subject, issuer, validity and separate check results. Files initially open offline.
- Details has searchable fields, extension bytes, revoked serials, copyable values and an optional ASN.1 structure view.
- Certification Path shows roots through the selected certificate; select any node to inspect it. Alternative paths are available in a picker.
- Bundles/identities use one small object picker, rather than a multi-file explorer.
- Verify Online explicitly enables issuer/revocation retrieval for that operation. Disable online verification in settings for offline checks. Settings also include system/light/dark appearance.
- Password-protected files show an unlock dialog. Passwords are not retained. Subsequent chain checks use the parsed public objects and preserve private-key metadata without unlocking again.
- Export uses a native Save dialog for public PEM, DER, P7B or SHA-256 text. Private-key export is unavailable. Scope can be a single object or a selected path.

Native menu shortcuts include Cmd/Ctrl+O (Open), Cmd/Ctrl+S (Export), and Quit. The app uses the operating system's title bar, file dialogs, window management and application menu. On supported hosts, subsequent association launches are routed to the running single-document app. Multiple files received in a single launch select the first; consecutive macOS file-open events replace the document.

## Build

Requires Go 1.26+, Python 3, and platform tools:

- macOS: Xcode/Command Line Tools; CGO is required. Builds target macOS 12+ and link UniformTypeIdentifiers for native file dialogs.
- Windows: WebView2 at runtime; NSIS `makensis` on PATH to build the setup installer. Windows desktop builds do not require CGO.
- Linux: C compiler, pkg-config, `libgtk-3-dev`, `libwebkit2gtk-4.1-dev` (Debian/Ubuntu names). CGO is required. Builds use `webkit2_41`.

```sh
# Build and package for the current host/architecture:
python3 scripts/build_desktop.py
# Explicit architecture (macOS/Windows cross-architecture builds supported):
python3 scripts/build_desktop.py --arch arm64
# Windows setup installer:
python scripts/build_desktop.py --arch amd64 --installer
```

The script downloads/runs the pinned Wails CLI through Go, embeds the static frontend (no npm dependencies), generates the app icon, and writes artifacts/checksums into `dist/desktop`. Set WAILS_BIN to an already-installed Wails 2.17.0 binary to avoid rebuilding the CLI. Linux cross-architecture builds require an appropriate native toolchain; CI uses native ARM64/AMD64 runners.

```sh
go test -race ./...
# On macOS/Windows:
go test -race -tags desktop ./...
# On Linux with GTK/WebKit installed:
go test -race -tags desktop,webkit2_41 ./...
```

The frontend is `internal/desktop/frontend`; native lifecycle/dialogs live in `internal/desktop`; single-document state and public export live in `internal/viewer`.

The existing PKIX/crypto limitations still apply; see implementation-status.md and revocation.md. This update changes the desktop workflow, not the scope of certificate validation assurances.
