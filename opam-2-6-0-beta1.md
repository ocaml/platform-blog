title: "opam 2.6.0~beta1 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)exn.st"}
  "Nathan Rebours - OCamlPro" {"mailto:nathan.rebours(à)ocamlpro.com"}
  "David Allsopp - Jane Street" {"mailto:dallsopp(à)janestreet.com"}
]
date: "2026-08-21"
--BODY--

_Feedback on this post is welcome on [Discuss](https://discuss.ocaml.org/t/ann-opam-2-6-0-alpha1/18372/3)!_

We are happy to announce the first beta release of opam 2.6.0.
You can view the full list of changes in the
[release note](https://github.com/ocaml/opam/releases/tag/2.6.0-beta1).

This version is a beta, we invite users to test it to spot previously
unnoticed bugs as we head towards the stable release.

## Try it!

The upgrade instructions are unchanged:

1. Either from binaries: run

For Unix systems
```
bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.6.0~beta1"
```
or from PowerShell for Windows systems
```
Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) } -Version 2.6.0~beta1"
```
or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.6.0-beta1) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.6.0-beta1#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


## Changes compared to 2.6.0~alpha1

* `opam init --reinit` will now stop asking to retry the command when upgrading from a 2.1 root ([#7057](https://github.com/ocaml/opam/issues/7057))

* `opam init --reinit` now regenerate the list of valid switches, fix switch internal data (cache, config, packages) ([#7066](https://github.com/ocaml/opam/issues/7066))

* Safe mode doesn't reset debuglevel to 0 anymore. Consider updating your scripts to discard `stderr` or add `--debug-level=0` if your script isn't resistant to output on stderr ([#7000](https://github.com/ocaml/opam/issues/7000))

* To avoid git underlying maintenance operation from interfering with opam (possible race condition), opam now disable git gc/maintenance on repositories it maintains ([#7031](https://github.com/ocaml/opam/issues/7031))

* opam now respects safe mode when encountering an outdated cache file ([#7066](https://github.com/ocaml/opam/issues/7066))


Some other internal improvements were made.
API changes are also denoted in the release note linked above.
This release also includes a handful of improvement and extensions to our testsuite.


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Happy hacking!

---
**Special thanks to the Haematology department and Bone Marrow Transplant Unit of the NHS Greater Glasgow for making this release possible <3**
