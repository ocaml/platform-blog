title: "opam 2.2.0 beta1 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Tarides" {"mailto:kit-ty-kate(à)outlook.com"}
  "David Allsopp - Tarides" {"mailto:david(à)tarides.com"}
]
date: "2024-01-18"
--BODY--

_Feedback on this post is welcomed on [Discuss](https://discuss.ocaml.org/t/ann-opam-2-2-0-beta1/13913)!_

We are happy to announce the first beta release of opam 2.2.0. It contains
some fixes and a new feature for Windows.  You can view the full list in the
[release note](https://github.com/ocaml/opam/releases/tag/2.2.0-beta1).

This version is a beta, we invite users to test it to spot previously
unnoticed bugs as we head towards the stable release.

## Major change: Check and advertise to use Git for Windows

Opam 2.2 is based on a Cygwin installation (previously installed, or managed
internally by opam). Cygwin's Git has three known usability issues when used
outside a Cygwin environment: it uses a different set of trusted certificate
authorities, has its own Credential Manager and potentially uses a separate Git
configuration.  We therefore recommend using [Git for
Windows](https://gitforwindows.org) either installed manually or via `winget`.

At `opam init`, opam checks for available Git(s), and asks the user to choose
one of the available, or to rerun `opam init` after installing another Git.

## Other noteworthy changes

* When compiling opam on Windows with MinGW, the resulting opam binary now contains `libstdc++` instead of requiring the DLL to be distributed alongside it or present in the environment
* Fix `opam env` containing carriage return on Cygwin - `eval $(opam env)` now works from a Cygwin bash terminal

## Miscellaneous changes

* Remove stray comments from `pwsh` and `cmd` opam env output
* Add `./configure --enable-static` to compile the opam binary statically on Linux
* Fix debug logs showing up regardless of verbosity on macOS 12.7.1 / 13.6.3 / 14.2 and FreeBSD
* Upgrade to, and require mccs >= 1.1+17
* Fix `opam tree` options `--dev` and `--no-switch`

Various other improvements were made and bugs were fixed.
API changes are denoted in the release note linked above.
This release also includes PRs improving the documentation and improving and extending the tests.

## Windows Support

The main opam-repository Windows compliance is still a work in progress, we
recommend to use existing [compatible
repository](https://github.com/ocaml-opam/opam-repository-mingw) (originally
from [@fdopen](https://github.com/fdopen)) and [32/64 bit mingw-w64
packages](https://github.com/dra27/opam-repository/tree/windows-5.0) (by
[@dra27](https://github.com/dra27)).


### How to Test opam on Windows

If you're feeling adventurous, you can use the experimental pre-built binary for Windows available [here](https://github.com/ocaml/opam/releases/download/2.2.0-beta1/opam-2.2.0-beta1-x86_64-preview-for-windows.exe).

Otherwise you can compile opam yourself using the following steps:

This beta requires a preexisting Cygwin installation for compiling opam.

1. Check that you have all dependencies installed:
  * `autoconf`, `make`, `patch`, `curl`
  * MinGW compilers: `mingw64-x86_64-gcc-g++`, `mingw64-i686-gcc-g++`
  * Or if you want to use the MSVC port of OCaml, you'll need to install Visual Studio or Visual Studio Build Tools
2. Download & extract the [opam archive](https://github.com/ocaml/opam/releases/download/2.2.0-beta1/opam-full-2.2.0-beta1.tar.gz)
3. In the directory, launch `make cold`
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
    bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.2.0~beta1"
    ```

    or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.2.0-beta1) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.2.0-beta1#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Thanks for trying this new release out, and we hope you will enjoy the new features!
