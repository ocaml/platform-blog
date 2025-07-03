title: "opam 2.4.0 rc1 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)exn.st"}
  "David Allsopp - Tarides" {"mailto:david(à)tarides.com"}
]
date: "2025-07-04"
--BODY--

We are happy to announce the first release candidate of opam 2.4.0.
You can view the full list of changes in the
[release note](https://github.com/ocaml/opam/releases/tag/2.4.0-rc1).

This version is an beta, we invite users to test it to spot previously
unnoticed bugs as we head towards the stable release, which hopefully
shouldn't take too long.

## Try it!

The upgrade instructions are unchanged:

1. Either from binaries: run

For Unix systems
```
bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.4.0~rc1"
```
or from PowerShell for Windows systems
```
Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) } -Version 2.4.0~rc1"
```
or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.4.0-rc1) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.4.0-rc1#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


## Changes

* Fix a regression in `opam switch create <version>` when all compilers of that version are flagged with `avoid-version` ([#6563](https://github.com/ocaml/opam/issues/6563))
* Performance improvement: ignore VCS directories of non-VCS repositories during `opam update` ([#6560](https://github.com/ocaml/opam/issues/6560))
* Do not remove the generated patch file during updates when debug-mode is on ([#6575](https://github.com/ocaml/opam/pull/6575))

### Release script
* Update some of the platforms the prebuilt binaries are built on to Alpine 2.21, FreeBSD 14.3, OpenBSD 7.7 and NetBSD 10.1 ([#6510](https://github.com/ocaml/opam/pull/6510))

### Build changes
* Use coreutils' `sha512sum` instead of perl's `shasum` utility when using `./configure --with-cygwin-setup` ([#6557](https://github.com/ocaml/opam/issues/6557))
* Upgrade the download-if-missing dependencies to `dune 3.19.1`, `opam-file-format 2.2.0`, `spdx_licenses 1.4.0` and `patch 3.0.0` ([#6580](https://github.com/ocaml/opam/pull/6580))

API changes are also denoted in the release note linked above.
This release also includes a couple of improvement and extensions to our testsuite.



Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Happy hacking!
