# ansi

[![CI](https://github.com/liufengyun/ansi/actions/workflows/ci.yml/badge.svg)](https://github.com/liufengyun/ansi/actions/workflows/ci.yml)

ANSI terminal escape codes for styled text in [Jo](https://jo-lang.org/).
The library is pure Jo with no dependencies and supports both Python and Ruby
applications. All functions return strings.

## Install

Use Jo 0.14 or later. The current release is built and tested with Jo 0.14.2.
Add `ansi` to your application's packages in `jo.toml`:

```toml
jo = "0.14"

[module.app]
kind = "app"
platform = "python"
src = ["src/"]
packages = [{ name = "ansi", version = "0.1" }]
```

Set `platform = "ruby"` for a Ruby application. Run `jo lock` after adding the
dependency, then `jo run app` to build and run your application.

## Usage

```jo
import ansi
import jo.IO.stdout

def main(): Unit receives stdout =
  println: ansi.bold("hello") + " " + ansi.red("world")
  println:
    ansi.style: "error", [ansi.Code.bold, ansi.Code.red]
  println:
    ansi.bold: ansi.underline("title")
  println:
    ansi.strip: ansi.green("plain text")
```

- Text styles: `bold`, `dim`, `italic`, `underline`, `blink`, `inverse`, `hidden`,
  and `strikethrough`.
- Foreground colors: `black`, `red`, `green`, `yellow`, `blue`, `magenta`, `cyan`,
  and `white`, plus bright variants such as `brightRed`.
- Background colors: `bgBlack` through `bgWhite`, plus bright variants such as
  `bgBrightBlue`.
- `apply(text, code)` applies one SGR code; `style(text, codes)` applies several.
  Named codes are available under `ansi.Code`, including `ansi.Code.reset`.
- `strip(text)` removes escape sequences of the form `ESC[...m`.

Styled strings end with a reset sequence. Styles can be nested; an inner reset
returns to terminal defaults. Color support depends on the terminal. Call
`strip` when writing plain text to files or terminals without color support.

## Development

Install [Jo](https://jo-lang.org/usage/install.html), JDK 17 or later, and
Python 3.12 or later. Select the compiler used by CI:

```sh
jo versions install 0.14.2
jo versions use 0.14.2
jo lock
jo run test
```

`jo run test` runs all 55 tests with [jtest](https://github.com/typescope/jtest)
and exits unsuccessfully if a test fails. Select a suite by passing its path
prefix:

```sh
jo run test -- strip
```

Build distributable artifacts with:

```sh
jo package
```

The compiled `.joy` package, source archive, and SHA-512 checksum files are
written to `.build/ansi/release/`. CI runs the tests and builds these artifacts
on pushes to `main` and pull requests.

## Releases

Download packages and sources from [GitHub Releases](https://github.com/liufengyun/ansi/releases).
New releases become available to `jo lock` after the Jo registry indexes them.
See [CHANGELOG.md](CHANGELOG.md) for version history and
[RELEASE.md](RELEASE.md) for the maintainer release process.

## License

MIT, as declared in `jo.toml`.
