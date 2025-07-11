title: "opam 2.4.0 alpha2 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)exn.st"}
  "David Allsopp - Tarides" {"mailto:david(à)tarides.com"}
]
date: "2025-05-05"
--BODY--

_Feedback on this post is welcomed on [Discuss](https://discuss.ocaml.org/t/ann-opam-2-4-0-alpha2/16609)!_

We are happy to announce the second alpha release of opam 2.4.0.
You can view the full list of changes in the
[release note](https://github.com/ocaml/opam/releases/tag/2.4.0-alpha2).

This version is an alpha, we invite users to test it to spot previously
unnoticed bugs as we head towards the stable release.

## Try it!

The upgrade instructions are unchanged:

1. Either from binaries: run

For Unix systems
```
bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.4.0~alpha2"
```
or from PowerShell for Windows systems
```
Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) } -Version 2.4.0~alpha2"
```
or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.4.0-alpha2) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.4.0-alpha2#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


## Changes

* `opam switch create [name] <version>` will not include compiler packages flagged with `avoid-version`/`deprecated` in the generated invariant anymore ([#6494](https://github.com/ocaml/opam/pull/6494)). This will allow opam to avoid the use of the `ocaml-system` package unless actually explicitly requested by the user. The opam experience when the `ocaml-system` compiler is used is known to be prone to a variety of bugs and configuration issues.
* Cygwin: Fallback to the existing `setup-x86_64.exe` if its upgrade failed to be fetched ([#6495](https://github.com/ocaml/opam/issues/6495), partial fix for [#6474](https://github.com/ocaml/opam/issues/6474))
* Fix a memory leak happening when running large numbers of commands or opening large number of opam files ([#6484](https://github.com/ocaml/opam/issues/6484)). *Thanks to [@hannesm](https://github.com/hannesm) for this contribution*
* Remove handling of the `OPAMSTATS` environment variable ([#6485](https://github.com/ocaml/opam/pull/6485)). *Thanks to [@hannesm](https://github.com/hannesm) for this contribution*

### Regression fixes from 2.4.0~alpha1

* Fix a crash when updating a repository that is deleting or adding empty files. ([#6448](https://github.com/ocaml/opam/issues/6448))
* Fix an extreme performance issue (takes several hours) when applying a large repository update ([#6448](https://github.com/ocaml/opam/issues/6448))
* Fix a crash when updating a git repository that moved a file to a new directory ([#6490](https://github.com/ocaml/opam/pull/6490))


API changes are also denoted in the release note linked above.
This release also includes a couple of improvement and extensions to our testsuite.



Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Happy hacking!
