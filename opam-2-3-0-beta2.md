title: "opam 2.3.0 beta2 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)outlook.com"}
  "David Allsopp - Tarides" {"mailto:david(à)tarides.com"}
]
date: "2024-10-23"
--BODY--

_Feedback on this post is welcomed on [Discuss](https://discuss.ocaml.org/t/ann-opam-2-3-0-beta2/15496)!_

We're happy to announce the second beta release of opam 2.3.0.

As this version is a beta, we invite users to test it to spot previously
unnoticed bugs as we head towards the stable release.

## Changes

Compared to the previous 2.3.0~beta1 release, the main change is:

* Fix a regression in the detection of the current terminal size that leads to opam output that tries to fit itself into 80 columns regardless of the current terminal size ([#6243](https://github.com/ocaml/opam/issues/6243)).

* A NetBSD/x86_64 pre-built release binary is now available

You can view the full list of changes, including API changes, in the
[release note](https://github.com/ocaml/opam/releases/tag/2.3.0-beta2).

## Try it!

The upgrade instructions are unchanged:

1. Either from binaries: run

For Unix systems
```
bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.3.0~beta2"
```
or from PowerShell for Windows systems
```
Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) } -Version 2.3.0~beta2"
```
or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.3.0-beta2) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.3.0-beta2#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Happy hacking!
