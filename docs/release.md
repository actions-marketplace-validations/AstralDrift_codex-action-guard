# Release process

This project uses Conventional Commits and Semantic Versioning.

## Versioning

- Patch releases fix bugs and false positives without changing intended behavior.
- Minor releases add rules, profiles, output fields, or CLI features.
- Major releases are reserved for breaking CLI or stable JSON/SARIF contract changes after v1.
- Prerelease tags use a suffix such as `v0.2.0-alpha.1`, `v0.2.0-beta.1`, or `v0.2.0-rc.1`. The Release workflow marks those GitHub Releases as prereleases. CLI flags, report schemas, and Action behavior may still change during alpha.

## Pre-release checklist

Run:

```sh
go test ./...
go vet ./...
go run ./cmd/codex-action-guard audit --all --fail-on high
```

Also verify:

- `README.md` still matches CLI behavior.
- `docs/rules.md` matches `internal/guard/rules.go`.
- Generated profiles audit cleanly.
- `CHANGELOG.md` has release notes.
- The version tag follows `vMAJOR.MINOR.PATCH` or a prerelease form such as `vMAJOR.MINOR.PATCH-alpha.N`.

## Tagging

```sh
go test ./...
go vet ./...
go run ./cmd/codex-action-guard audit --all --fail-on high
version=v0.2.0-alpha.1
git tag "$version"
git push origin "$version"
```

Pushing a `vMAJOR.MINOR.PATCH` (or prerelease) tag runs the Release workflow. It builds `codex-action-guard` for:

- Linux `amd64` and `arm64`
- macOS `amd64` and `arm64`
- Windows `amd64` and `arm64`

The workflow uploads compressed archives and `SHA256SUMS` to the GitHub Release for the tag. If the release already exists, the workflow uploads artifacts with `--clobber`. Tags whose names contain `-alpha`, `-beta`, or `-rc` are published with `--prerelease`.

## Release flow

1. Run the local gates:

   ```sh
   go test ./...
   go vet ./...
   go run ./cmd/codex-action-guard audit --all --fail-on high
   ```

2. Verify the six release targets build locally:

   ```bash
   for target in \
     "linux amd64" "linux arm64" \
     "darwin amd64" "darwin arm64" \
     "windows amd64" "windows arm64"
   do
     read -r goos goarch <<< "$target"
     ext=""
     if [ "$goos" = "windows" ]; then
       ext=".exe"
     fi
     GOOS="$goos" GOARCH="$goarch" CGO_ENABLED=0 \
       go build -trimpath -o "/tmp/codex-action-guard-$goos-$goarch$ext" ./cmd/codex-action-guard
   done
   ```

3. Tag and push the release. Replace `v0.2.0-alpha.1` with the version being released:

   ```sh
   version=v0.2.0-alpha.1
   git tag "$version"
   git push origin "$version"
   ```

4. Verify the GitHub Release has Linux, macOS, and Windows archives for `amd64` and `arm64`, plus `SHA256SUMS`:

   ```sh
   gh release view "$version"
   gh release download "$version" --pattern SHA256SUMS --dir /tmp/codex-action-guard-release
   ```

5. Only after a non-prerelease cut is validated, update the floating `v0` action tag to the same commit:

   ```sh
   git tag -f v0 "$version"
   git push -f origin v0
   ```

Do **not** move the floating `v0` tag onto an alpha or beta prerelease. Early adopters should pin the explicit prerelease tag (for example `AstralDrift/codex-action-guard@v0.2.0-alpha.1`).

The Release workflow listens only for semver-like `v*.*.*` tags, so pushing the floating `v0` tag does not create a separate GitHub Release.

## GitHub Action wrapper

Tagged releases (`vMAJOR.MINOR.PATCH` and prereleases such as `v0.2.0-alpha.1`) download the matching platform archive from the GitHub Release, verify its `SHA256SUMS` entry, and execute the binary.

Floating tags (`v0`), branches, and commit SHAs fall back to `go run` from the checked-out action source so PR dogfooding and local `uses: ./` keep working without a published release for that ref.

## Local artifact smoke test

Before tagging, verify the same target set locally:

```bash
for target in \
  "linux amd64" "linux arm64" \
  "darwin amd64" "darwin arm64" \
  "windows amd64" "windows arm64"
do
  read -r goos goarch <<< "$target"
  ext=""
  if [ "$goos" = "windows" ]; then
    ext=".exe"
  fi
  GOOS="$goos" GOARCH="$goarch" CGO_ENABLED=0 go build -o "/tmp/codex-action-guard-$goos-$goarch$ext" ./cmd/codex-action-guard
done
```

## Ship checklist for Marketplace / alpha

```sh
version=v0.2.0-alpha.1
go test ./...
go vet ./...
go run ./cmd/codex-action-guard audit --all --fail-on high
# confirm CHANGELOG.md has a matching section
git tag "$version"
git push origin "$version"
# wait for Release workflow; confirm archives + SHA256SUMS
gh release view "$version"
# edit the release on GitHub and publish to Marketplace if desired:
# https://github.com/AstralDrift/codex-action-guard/releases/new?marketplace=true
# or open the created release and enable Marketplace listing
```

Leave `v0` on the last validated non-prerelease until alpha is promoted.
