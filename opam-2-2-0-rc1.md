title: "opam 2.2.0 rc1 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)outlook.com"}
  "David Allsopp - Tarides" {"mailto:david(à)tarides.com"}
]
date: "2024-06-21"
--BODY--

_Feedback on this post is welcomed on [Discuss](https://discuss.ocaml.org/t/ann-opam-2-2-0-rc1-release/14842)!_

We are very excited to announce the first (and hopefully only) release candidate for opam 2.2.0.

We've squashed a few more bugs and we consider this to be ready for the final release. You can view the full list of changes in the [release note](https://github.com/ocaml/opam/releases/tag/2.2.0-rc1).

We invite users to test it one final time just in case there are any previously unnoticed bugs!

## Changes

* Fix `opam upgrade` wanting to keep rebuilding the compiler (as now it contains an `x-env-path-rewrite` field)
* Provide defaults so `opam init -y` no longer asks questions on Windows
* Fix `OpamConsole.menu` when there are more than 9 options (can happen on Windows)
 
A couple more improvements were made and bugs were fixed.
The single API change is also denoted in the
[release note](https://github.com/ocaml/opam/releases/tag/2.2.0-rc1).
This release also includes PRs extending the tests.

## Try it!

In case you plan a possible rollback, you may want to first backup your
`~/.opam` or `$env:LOCALAPPDATA\opam` directory.

The upgrade instructions are unchanged:

1. Either from binaries: run

For Unix systems
```
bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.2.0~rc1"
```
or from PowerShell for Windows systems
```
Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) }"
```
or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.2.0-rc1) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.2.0-rc1#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Thanks for trying this new release out, and we hope you will enjoy the new features of opam 2.2!
