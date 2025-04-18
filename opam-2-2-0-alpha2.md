title: "opam 2.2.0 alpha2 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Tarides" {"mailto:kit-ty-kate(à)outlook.com"}
]
date: "2023-07-26"
--BODY--

_Feedback on this post is welcomed on [Discuss](https://discuss.ocaml.org/t/ann-opam-2-2-0-alpha2-release/12699)!_


We are happy to announce the second alpha release of opam 2.2.0. It contains
some fixes and a new feature for Windows.  You can view the full list in the
[release note](https://github.com/ocaml/opam/releases/tag/2.2.0-alpha2).

This version is an alpha, we invite users to test it to spot previously
unnoticed bugs to head towards the stable release.

## Windows Support

The first alpha came with native Windows compatibility. This second alpha comes
with a more simple init for Windows: we no longer rely on an already present
[Cygwin](https://cygwin.com/) UNIX-like environment for Windows as a
compatibility layer. During initialisation, opam now proposes to embed its own fully
managed Cygwin install.

The main opam-repository Windows compliance is still a work in progress, we
recommend to use existing [compatible
repository](https://github.com/ocaml-opam/opam-repository-mingw) (originally
from [@fdopen](https://github.com/fdopen)) and [32/64 bit mingw-w64
packages](https://github.com/dra27/opam-repository) (by
[@dra27](https://github.com/dra27)).


### How to Test opam on Windows

This alpha requires a preexisting Cygwin installation for compiling opam.

1. Check that you have all dependencies installed:
  * `autoconf`, `make`, `patch`, `curl`
  * MinGW compilers: `mingw64-x86_64-gcc-g++`, `mingw64-i686-gcc-g++`
  * Or if you want to use the MSVC port of OCaml, you'll need to install Visual Studio or Visual Studio Build Tools
2. Download & extract the [opam archive](https://github.com/ocaml/opam/releases/download/2.2.0-alpha2/opam-full-2.2.0-alpha2.tar.gz)
3. In the directory launch `make cold`
4. A coffee later, you now have an opam executable!
5. Start your preferred Windows terminal (`cmd` or `PowerShell`), and initialise opam with the Windows _sunset_ repository:
  * `opam init git+https://github.com/ocaml-opam/opam-repository-mingw`

From here, you can try to install the [sunset
repository](https://discuss.ocaml.org/t/sunsetting-opam-repository-mingw/11632)
packages. If you find any bugs, please [submit an
issue](https://github.com/ocaml-opam/opam-repository-mingw#what-do-i-do-when-things-are-broken).
It will help opam-repository maintainers to add Windows repository packages
into the main repository.

> Hint: if you use the MinGW compiler, don't forget to add to your `PATH` the
path to `libc` dlls (usually
`C:\cygwin64\usr\x86_64-w64-mingw32\sys-root\mingw\bin`). Or compile opam with
`make cold CONFIGURE_ARGS=--with-private-runtime`, and if you change opam
location, don't forget to copy `Opam.Runtime.amd64` (or `Opam.Runtime.i386`)
with it.

## Updates & fixes
  * opam var have now a more informative error message in case of package variable
  * opam lint: update Error 29 on package variables on filters to check also `conflicts:` field
  * opam admin lint: clean output when called not from a terminal
  * configure: error if no complementary compiler is found on Windows


## Try it!

In case you plan a possible rollback, you may want to first backup your
`~/.opam` directory.

The upgrade instructions are unchanged:

1. Either from binaries: run

    ```
    bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.2.0~alpha2"
    ```

    or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.2.0-alpha2) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.2.0-alpha2#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Thanks for trying this new release out, and we hope you will enjoy the new features!
