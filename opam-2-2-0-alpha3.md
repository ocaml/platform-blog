title: "opam 2.2.0 alpha3 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Tarides" {"mailto:kit-ty-kate(à)outlook.com"}
  "David Allsopp - Tarides" {"mailto:david(à)tarides.com"}
]
date: "2023-11-13"
--BODY--

_Feedback on this post is welcomed on [Discuss](https://discuss.ocaml.org/t/ann-opam-2-2-0-alpha3/13431)!_

We are happy to announce the third alpha release of opam 2.2.0. It contains
some fixes and a new feature for Windows.  You can view the full list in the
[release note](https://github.com/ocaml/opam/releases/tag/2.2.0-alpha3).

This version is an alpha, we invite users to test it to spot previously
unnoticed bugs as we head towards the stable release.

## Major change: Environment variables handling on Windows

opam files now support a new `x-env-path-rewrite` field which
specifies rewriting rules for the environment variable updates defined in
the `setenv` and `build-env` fields. This field allows greater control over the
separator character used for PATH-like fields (i.e. `;` vs `:`), conversion of
slashes to backslashes, and even conversion from Windows native path format
(`C:\cygwin64\home\dra\.opam`) to Cygwin format (`/home/dra/.opam`).

The rewriting rules allow opam directory-like variables (e.g. the `%{lib}%` directory
of a switch) to be used in `setenv` and `build-env` fields in a portable way.

For example, given:
```
setenv: [
  [PKG_CONFIG_PATH += "%{lib}%/pkgconfig"]
  [PATH += "%{share}%/bin"]
]
x-env-path-rewrite: [
  [ PKG_CONFIG_PATH ":" "host" ]
  [ PATH (":" {os != "win32"} | ";" {os = "win32"}) ("target" {os != "win32"} | "target-quoted" {os = "win32"}) ]
]
```
the environment variable changes given by `opam env` on Windows would be:

```
PKG_CONFIG_PATH='/cygdrive/c/Users/DRA/AppData/Local/opam/default/lib/pkgconfig[:<rest-of-PKG_CONFIG_PATH, if given>]'
PATH='C:\Users\DRA\AppData\Local\opam\default\share\bin;C:\Users\DRA\AppData\Local\opam\default\bin;<rest-of-PATH>'
```

with the following interesting parts for Windows users:
- `PKG_CONFIG_PATH`, which is consumed by a Cygwin-tool, has the directory given in Unix-like syntax, and opam's `share` variable was automatically converted
- The correct separator is used for each (`:` for `PKG_CONFIG_PATH`, `;` for `PATH` is used when adding entries)
- In the `PATH` update, `/bin` was converted to `\bin`

*Note that the specification for `PATH` is opam's _default_ behaviour, so it's not actually necessary to have this formula for `PATH` in the `x-env-path-rewrite` field.*

The full syntax is described in full in the [manual](https://opam.ocaml.org/doc/Manual.html#env-update-rewrite).

Opam files carrying this new field are still compatible with the opam 2.0 syntax as it is an extension field, however its effect is only available with opam 2.2.0~alpha3 and above.
If you want to make sure users of the package containing it have a compatible opam, you can use the `available` field to that end:
```
available: opam-version >= "2.2.0~alpha3"
```
or, if the change is Windows-specific:
```
available: opam-version >= "2.2.0~alpha3" | os != "win32"
```

## Other noteworthy changes

* Sandbox: `/tmp` is now writable again, restoring POSIX compliance
* opam tree: `opam tree package.version` is now supported, displaying the dependency tree of a specific version of a package
* opam tree: `--recurse` and `--subpath` are supported for directory arguments
* opam admin: new `add-extrafiles` command to add/check/update the `extra-files:` field according to the files present in the `files/` directory
* opam lint: new syntax allow marking a set of warnings as errors e.g. `-W @1..9`
* Releases: Pre-built binaries now include ppc64le and s390x

## Miscellaneous changes

* A handful of issues related to the compilation of opam on Windows were fixed
* Bugs in the handling of the `OPAMCURL`, `OPAMFETCH` and `OPAMVERBOSE` environment variables were fixed
* Bugs in the handling of the `--assume-built` argument were fixed
* Sporadic crashes and segfaults during shell detection on Windows were fixed

Various other improvements were made and bugs were fixed.
API changes are denoted in the release note linked above.
This release also includes a handful of PRs improving the documentation and more than a dozen PRs improving and extending the tests.

## Windows Support

The main opam-repository Windows compliance is still a work in progress, we
recommend to use existing [compatible
repository](https://github.com/ocaml-opam/opam-repository-mingw) (originally
from [@fdopen](https://github.com/fdopen)) and [32/64 bit mingw-w64
packages](https://github.com/dra27/opam-repository/tree/windows-5.0) (by
[@dra27](https://github.com/dra27)).


### How to Test opam on Windows
If you're feeling adventurous, you can use the experimental pre-built binary for Windows available [here](https://github.com/ocaml/opam/releases/download/2.2.0-alpha3/opam-2.2.0-alpha3-preview-for-windows.exe). It should work but since it's our first public pre-built binary for Windows please use at your own risk.

Otherwise you can compile opam yourself using the following steps:

This alpha requires a preexisting Cygwin installation for compiling opam.

1. Check that you have all dependencies installed:
  * `autoconf`, `make`, `patch`, `curl`
  * MinGW compilers: `mingw64-x86_64-gcc-g++`, `mingw64-i686-gcc-g++`
  * Or if you want to use the MSVC port of OCaml, you'll need to install Visual Studio or Visual Studio Build Tools
2. Download & extract the [opam archive](https://github.com/ocaml/opam/releases/download/2.2.0-alpha3/opam-full-2.2.0-alpha3.tar.gz)
3. In the directory:
  * if you are using MSVC: launch `make cold`
  * if you are using MinGW: make sure the path to the `libc` dlls are in your `PATH` and launch `make cold`. For instance: `export PATH='C:\cygwin64\usr\x86_64-w64-mingw32\sys-root\mingw\bin':$PATH && make cold`. Don’t forget to update `PATH` accordingly or place the `opam` binary in the same directory as the `libc` dlls if you want to move the resulting opam binary.
  * alternatively, if you're using MinGW: `make cold CONFIGURE_ARGS=--with-private-runtime`. If you change the location of the resulting opam binary, don't forget to copy `Opam.Runtime.amd64` directory (or `Opam.Runtime.i386`) in the same directory.
4. A coffee later, you now have an opam executable!
5. Start your preferred Windows terminal (`cmd` or `PowerShell`), and initialise opam with the Windows _sunset_ repository:
  * `opam init git+https://github.com/ocaml-opam/opam-repository-mingw`

From here, you can try to install the [sunset
repository](https://discuss.ocaml.org/t/sunsetting-opam-repository-mingw/11632)
packages. If you find any bugs, please [submit an
issue](https://github.com/ocaml-opam/opam-repository-mingw#what-do-i-do-when-things-are-broken).
It will help opam-repository maintainers to add Windows repository packages
into the main repository.

## Try it!

In case you plan a possible rollback, you may want to first backup your
`~/.opam` directory.

The upgrade instructions are unchanged:

1. Either from binaries: run

    ```
    bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.2.0~alpha3"
    ```

    or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.2.0-alpha3) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.2.0-alpha3#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Thanks for trying this new release out, and we hope you will enjoy the new features!
