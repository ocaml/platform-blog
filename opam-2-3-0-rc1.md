title: "opam 2.3.0 rc1 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)outlook.com"}
  "David Allsopp - Tarides" {"mailto:david(à)tarides.com"}
]
date: "2024-10-30"
--BODY--

_Feedback on this post is welcomed on [Discuss](https://discuss.ocaml.org/t/ann-opam-2-3-0-rc1/15533)!_

We're happy to announce the first and hopefully only release candidate of opam 2.3.0.

This version does not have any significant change compared to the previous 2.3.0~beta2 release
and we hope the final release to also have no significant change.
Regardless, we invite users to test this version to make sure there isn't any regressions.

Unless a regression is spotted or another problem arises, we hope to have the final release of 2.3.0 out on the 12th of November.

## Try it!

The upgrade instructions are unchanged:

1. Either from binaries: run

For Unix systems
```
bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.3.0~rc1"
```
or from PowerShell for Windows systems
```
Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) } -Version 2.3.0~rc1"
```
or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.3.0-rc1) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.3.0-rc1#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Happy hacking!
