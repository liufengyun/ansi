# Releasing ansi

Releases are published as GitHub Releases in `liufengyun/ansi`. The Jo registry
discovers their package assets automatically; it does not publish directly from
Git tags. The package is already registered.

## Prerequisites

- Write access to the repository and an authenticated [GitHub CLI](https://cli.github.com/).
- Jo 0.14.2, JDK 17 or later, and Python 3.12 or later, matching CI.
- A clean checkout of `main`, up to date with `origin/main`.

## Prepare a version

1. Choose an unused `MAJOR.MINOR.PATCH` version and update
   `[module.ansi.package].version` in `jo.toml`.
2. Move the relevant `Unreleased` entries in [CHANGELOG.md](CHANGELOG.md) to a
   section for the new version, with its release date and GitHub release link.
   Keep an `Unreleased` section for future changes.
3. Update the README if the public API, compiler requirement, or development
   commands changed. Keep the compiler version in CI and these instructions
   aligned if upgrading Jo.
4. If dependencies changed, run `jo lock` and include `jo.lock` in the commit.

The commands below read the version from `jo.toml`. Run them in the repository
root, using Bash and Python 3.12 or later:

```sh
set -euo pipefail
version=$(python3 -c 'import tomllib; print(tomllib.load(open("jo.toml", "rb"))["module"]["ansi"]["package"]["version"])')
tag="v${version}"
artifact_dir=".build/ansi/release"
```

## Validate and package

```sh
jo --version
jo clean
jo run test
jo package
git diff --check

(
  cd "$artifact_dir"
  sha512sum --check "ansi-v${version}.joy.sha512"
  sha512sum --check "ansi-v${version}-sources.zip.sha512"
)
```

Confirm all tests pass. Inspect `meta.toml` inside the `.joy` archive to confirm
the package name, version, Jo requirement, and `runtime = "pure"`. Test-only
dependencies such as jtest must not appear in the package metadata.

Expected artifacts:

```text
ansi-v<VERSION>.joy
ansi-v<VERSION>.joy.sha512
ansi-v<VERSION>-sources.zip
ansi-v<VERSION>-sources.zip.sha512
```

## Commit and tag

Commit the reviewed release changes, adding any changed sources and tests as
well as the release metadata:

```sh
git add jo.toml jo.lock CHANGELOG.md README.md
git commit -m "Release ansi ${version}"
git push origin main
```

Wait for the CI run for that commit to pass before tagging:

```sh
gh run list --workflow ci.yml --branch main
# Use the run ID for the release commit:
gh run watch RUN_ID --exit-status
```

With a clean working tree at the passing commit:

```sh
git tag -a "$tag" -m "ansi ${tag}"
git push origin "$tag"
```

## Publish

Write release notes for this version to a file, based on its changelog entry.
Then upload all four artifacts, using the notes file's actual path:

```sh
gh release create "$tag" \
  "$artifact_dir/ansi-v${version}.joy" \
  "$artifact_dir/ansi-v${version}.joy.sha512" \
  "$artifact_dir/ansi-v${version}-sources.zip" \
  "$artifact_dir/ansi-v${version}-sources.zip.sha512" \
  --repo liufengyun/ansi \
  --verify-tag \
  --title "$tag" \
  --notes-file /path/to/release-notes.md
```

Do not reuse a published version or replace its artifacts. Correct a published
package by releasing a new version.

## Verify publication

```sh
gh release view "$tag" --repo liufengyun/ansi
curl -fsSL https://pkg.jo-lang.org/ansi.jsonl
jo info "ansi@${version}"
```

Confirm the release is public and includes all four assets. Registry discovery
is asynchronous: the version is available to Jo package consumers once it
appears in `ansi.jsonl`. If it is absent, allow time for indexing and check
again. If indexing fails, verify the asset names and SHA-512 checksums before
investigating the registry.
