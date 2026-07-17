# Changelog

This project follows Semantic Versioning once tagged releases begin. Changes are grouped using Conventional Commit categories.

## Unreleased

## v0.2.0-alpha.1 - 2026-07-17

### Added

- GitHub Action release tags now download a checksum-verified CLI binary from the matching GitHub Release instead of always compiling with `go run`.
- CI now runs `gofmt` checks, `go vet`, and a six-target cross-compile smoke build.
- Golden Markdown, JSON, and SARIF snapshots for the vulnerable fixture set.

### Changed

- Floating tags (`v0`), branches, and commit SHAs still fall back to `go run` from the checked-out action source for PR dogfooding and local `uses: ./`.
- Release workflow marks tags containing `-alpha`, `-beta`, or `-rc` as GitHub prereleases.
- Generated installer/profile workflows and examples now use `actions/checkout@v7` and `actions/upload-artifact@v7`.
- `docs/roadmap.md` is a stub that points at the canonical root `ROADMAP.md`.
- `SUPPORT.md` points at GitHub Issues only.
- Architecture docs no longer describe unused facade packages.

### Removed

- Unused `internal/rules`, `internal/reporters`, and `internal/taint` packages that were never imported by the CLI.

## v0.1.1 - 2026-06-01

### Changed

- Generated workflows now use `actions/upload-artifact@v5`.
- Generated workflows now opt into the GitHub Actions Node 24 JavaScript action runtime.
- Workflows and generated templates now use `actions/checkout@v6`.
- Release documentation now uses a reusable tag-and-floating-major flow.

## v0.1.0 - 2026-06-01

### Added

- Initial Go CLI with `version`, `init`, `audit`, `diff`, `packet`, and `explain`.
- v0 OpenAI Codex GitHub Action provider rule pack, `CODX001` through `CODX010`.
- Safe generated profiles for read-only PR review, CI failure analysis, release notes drafting, security review, and label-gated maintainer tasks.
- Markdown, JSON, and SARIF report output.
- Deterministic `rules` metadata export for downstream tooling.
- JSON schemas for audit reports and rule metadata exports.
- Tag-triggered release workflow that publishes cross-platform CLI archives and checksums.
- `install` command with artifact and SARIF guard workflow presets.
- Generated workflow examples and installer documentation.
- CI dogfooding of the repository's own workflows.

### Changed

- Release workflow now runs only for semver-like `vMAJOR.MINOR.PATCH` tags so the floating `v0` action tag does not create a separate release.
