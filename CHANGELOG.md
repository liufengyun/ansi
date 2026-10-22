# Changelog

## Unreleased

### Added

- README with installation, API examples, and development instructions.
- GitHub Actions CI that runs the jtest suite with Jo 0.14.2 and uploads
  distributable package artifacts.
- This changelog and a maintainer release guide in [RELEASE.md](RELEASE.md).

### Fixed

- Remove the unsupported `jo test` shortcut; run tests with `jo run test`.

### Changed

- Use colon syntax for nested calls in examples and tests.

## [0.1.1](https://github.com/liufengyun/ansi/releases/tag/v0.1.1) — 2026-10-22

### Changed

- Upgrade the Jo requirement from 0.10 to 0.14, built and tested with Jo 0.14.2.
- Migrate to the module-based `jo.toml` build specification.
- Update the `fold` callback to the current Jo syntax.
- Migrate all 55 checks to jtest 0.2.1 suites and assertions, with a nonzero
  exit status when tests fail.
- Regenerate `jo.lock` for the current format and test dependencies.

## [0.1.0](https://github.com/liufengyun/ansi/releases/tag/v0.1.0) — 2026-03-31

### Added

- Pure Jo library for ANSI terminal escape codes, targeting Jo 0.10.
- Text styles, standard and bright foreground and background colors, and named
  SGR constants under `ansi.Code`.
- `apply`, `style`, and `strip` functions.
- Tests covering styling, escape removal, and nested styles.
