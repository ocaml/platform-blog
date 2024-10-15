title: "opam 2.3.0 beta1 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)outlook.com"}
  "David Allsopp - Tarides" {"mailto:david(à)tarides.com"}
]
date: "2024-10-15"
--BODY--

_Feedback on this post is welcomed on [Discuss](https://discuss.ocaml.org/t/ann-opam-2-3-0-beta1/XXXXX)!_

We're happy to announce the first beta release of opam 2.3.0.
Compared to the previous 2.3.0\~alpha1 release, you can view the full list of changes in the
[release note](https://github.com/ocaml/opam/releases/tag/2.3.0-beta1).

This version is a beta, we invite users to test it to spot previously
unnoticed bugs as we head towards the stable release.

## Changes

* Fix an opam 2.1 regression where the initial pin of a local VCS directory would store untracked and ignored files.
  Those files would usually be cleaned before building the package, however git submodules would not be cleaned and would cause issues when paired with the new behaviour added in 2.3.0\~alpha1 which makes opam error when git submodules fail to update (it was previously a warning)

* Fix a regression which would make opam crash on platforms where `getconf LONG_BIT` is not available (e.g. OpenBSD)

* Fix the installed packages internal cache, which was storing the wrong version of the opam file after a build failure.
  This could be triggered easily for users with custom repositories with non-populated extra-files

* Fix a regression in lint W59 with local urls that are not archives

* Bump to lang dune to 2.8 and bump the requirement to dune >= 2.8, which was actually required in the previous alpha release

* Bump the vendored version of dune to 3.16.0, cppo to 1.7.0 and extlib to 1.8.0

* Fix compilation with OCaml 5.3 when using the vendored extlib by updating to the 5.3 compatible version

* Fix the compilation of opam on Windows with OCaml >= 5.0 (again)

* Several improvements to the pre-built release binaries were made:
  * The Linux binaries are now built on Alpine 3.20
  * The FreeBSD binary is now built on FreeBSD 14.1
  * The OpenBSD binary is now built on OpenBSD 7.6 and looses support for OpenBSD 7.5 and older
  * A Linux/riscv64 binary is now available

API changes are denoted in the release note linked above.
This release also includes a couple of PRs improving and extending the tests.

## Try it!

The upgrade instructions are unchanged:

1. Either from binaries: run

For Unix systems
```
bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.3.0~beta1"
```
or from PowerShell for Windows systems
```
Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) } -Version 2.3.0~beta1"
```
or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.3.0-beta1) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.3.0-beta1#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Happy hacking!
