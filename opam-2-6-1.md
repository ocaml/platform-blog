title: "opam 2.6.1 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)exn.st"}
  "Nathan Rebours - OCamlPro" {"mailto:nathan.rebours(à)ocamlpro.com"}
  "David Allsopp - Jane Street" {"mailto:dallsopp(à)janestreet.com"}
]
date: "2026-10-07"
--BODY--

We are pleased to announce the release of opam 2.6.1 fixing a couple of regressions and minor annoyances.

We advise everyone to upgrade. Please read on for installation and upgrade instructions.


## Regression fixes

* Fix the depexts installation on `opam install --deps-only` when the system packages already exist in a repository ([#7153](https://github.com/ocaml/opam/issues/7153))


## Improvements

* Add support for using git repositories owned by another local user (common in docker containers) ([#6963](https://github.com/ocaml/opam/issues/6963))

* Use `/dev/null` on both Unix and Windows when setting `GIT_CONFIG_*` (works around a bug in Git-for-Windows 2.56.0.windows.1) ([#7085](https://github.com/ocaml/opam/issues/7085))


## Try it!

The upgrade instructions are unchanged:

1. Either from binaries: run

For Unix systems
```
bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.6.1"
```
or from PowerShell for Windows systems
```
Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) } -Version 2.6.1"
```
or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.6.1) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.6.1#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Happy hacking!
