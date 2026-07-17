# Draft: v0.2.0-alpha.1 (Marketplace / GitHub Release)

Status: **draft only** — do not tag or publish until explicitly approved.

## Proposed GitHub Release title

`v0.2.0-alpha.1`

## Proposed body (paste into GitHub Release / Marketplace)

**Summary**

First intentional alpha of `codex-action-guard`: a deterministic CLI and GitHub Action that generates safe-by-default OpenAI Codex workflow profiles and audits existing `openai/codex-action` / `codex exec` workflows for unsafe trust boundaries.

This is an independent community project. It is not affiliated with, endorsed by, or certified by OpenAI.

**Highlights**

- Checksum-verified Action binaries for pinned release tags (`@v0.2.0-alpha.1`). Floating `@v0`, branches, and SHAs still use `go run` from source for dogfooding.
- Cross-platform CLI archives + `SHA256SUMS` (linux/darwin/windows × amd64/arm64).
- Markdown, JSON, and SARIF findings with rules `CODX001`–`CODX010`.
- `install` presets and safe `init` profiles.

**Install / pin**

```yaml
- uses: AstralDrift/codex-action-guard@v0.2.0-alpha.1
  with:
    fail-on: high
    format: sarif
    output: codex-action-guard.sarif
```

CLI:

```sh
go install github.com/AstralDrift/codex-action-guard/cmd/codex-action-guard@v0.2.0-alpha.1
```

**Alpha caveats**

- CLI flags, report JSON/SARIF fields, and rule tuning may still change before a non-prerelease `v0.2.0`.
- Do not move the floating `v0` action tag onto this alpha; leave `v0` on the last validated non-prerelease (`v0.1.1`) until alpha is promoted.
- Scope remains Codex GitHub Actions only (no multi-provider scanning, no default LLM analysis).

**Full changelog**

See `CHANGELOG.md` for `v0.2.0-alpha.1`.

## Ship commands (when approved)

```
version=v0.2.0-alpha.1
go test ./...
go vet ./...
go run ./cmd/codex-action-guard audit --all --fail-on high
git tag "$version"
git push origin "$version"
# wait for Release workflow success
gh release view "$version"
# then publish to Marketplace from the release UI if desired
```

Leave `v0` untouched for this alpha cut.
