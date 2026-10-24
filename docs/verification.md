# Verification — 2026-10-20

Environment: Go 1.26.5, macOS ARM64.

Passed:

- `go mod tidy`
- `go vet ./...`
- `go test ./...`
- `go test -race ./...`
- `go test ./internal/core -run '^$' -fuzz FuzzInspectDER -fuzztime 2s` (177,808 executions)
- `go test ./internal/core -run '^$' -fuzz FuzzLoad -fuzztime 2s` (160,470 executions)
- `node --check` on the extracted embedded JavaScript.
- `CGO_ENABLED=0` builds for darwin/linux/windows, each amd64/arm64.

Go build/test commands used `GOCACHE=/private/tmp/certview-go-cache` because the default host cache was sandbox-restricted. Test servers needed permission to bind loopback. The fuzz durations are short smoke checks, not exhaustive security assurance.

Windows/Linux executables were cross-compiled but not executed. CI was configured but not run. The full PLAN.md acceptance matrix remains incomplete; see implementation-status.md.
