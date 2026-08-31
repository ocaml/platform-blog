title: "opam 2.6.0~beta2 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)exn.st"}
  "Nathan Rebours - OCamlPro" {"mailto:nathan.rebours(à)ocamlpro.com"}
  "David Allsopp - Jane Street" {"mailto:dallsopp(à)janestreet.com"}
]
date: "2026-08-31"
--BODY--

We are happy to announce the second beta release of opam 2.6.0.
You can view the full list of changes in the
[release note](https://github.com/ocaml/opam/releases/tag/2.6.0-beta2).

This version is a beta, we invite users to test it to spot previously
unnoticed bugs as we head towards the stable release.

## Try it!

The upgrade instructions are unchanged:

1. Either from binaries: run

For Unix systems
```
bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.6.0~beta2"
```
or from PowerShell for Windows systems
```
Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) } -Version 2.6.0~beta2"
```
or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.6.0-beta2) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.6.0-beta2#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


## Changes compared to 2.6.0~beta1

* Fix a performance regression where opam project trees were scanned for nothing, when pinning them ([#7098](https://github.com/ocaml/opam/issues/7098))

* The Windows binary generated during our release process is now reproducible ([#7097](https://github.com/ocaml/opam/issues/7097), [#7115](https://github.com/ocaml/opam/issues/7115))


Some other internal improvements were made.
API changes are also denoted in the release note linked above.
This release also includes a couple of extensions to our testsuite.


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Happy hacking!

---
**Special thanks to the Haematology department and Bone Marrow Transplant Unit of the NHS Greater Glasgow for making this release possible <3**
