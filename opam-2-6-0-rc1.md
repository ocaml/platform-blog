title: "opam 2.6.0~rc1 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)exn.st"}
  "Nathan Rebours - OCamlPro" {"mailto:nathan.rebours(à)ocamlpro.com"}
  "David Allsopp - Jane Street" {"mailto:dallsopp(à)janestreet.com"}
]
date: "2026-09-08"
--BODY--

We are happy to announce the first release candidate of opam 2.6.0.
You can view the full list of changes in the
[release note](https://github.com/ocaml/opam/releases/tag/2.6.0-rc1).

This version is a pre-release, we invite users to test it to spot previously
unnoticed bugs as we head towards the stable release.

## Try it!

The upgrade instructions are unchanged:

1. Either from binaries: run

   For Unix systems
   ```
   bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.6.0~rc1"
   ```
   or from PowerShell for Windows systems
   ```
   Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) } -Version 2.6.0~rc1"
   ```
   or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.6.0-rc1) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.6.0-rc1#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


## Changes compared to 2.6.0~beta2

* Fix a 2.6 performance regression where tar.gz repositories were read entirely twice per package installed (#7131)

* The release archive and opam's "lockfile" (used on request when building the opam source code) now contain most of opam's dependencies at their latest version (#7116)


Some other internal improvements were made.
This release also includes a couple of extensions to our testsuite and documentation.


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Happy hacking!

---
**Special thanks to the Haematology department and Bone Marrow Transplant Unit of the NHS Greater Glasgow for making this release possible <3**
