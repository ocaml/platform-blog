title: "opam releases: 2.5.0~rc1 & opam-publish 2.7.1"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)exn.st"}
  "David Allsopp - Tarides" {"mailto:david(à)tarides.com"}
]
date: "2025-11-19"
--BODY--

We are happy to announce the first release candidate of opam `2.5.0`,
which boasts a grand total of zero (0) changes compared to `2.5.0~beta1`!

We also take this opportunity to announce the release of opam-publish `2.7.1`,
whose full release notes can be seen
[here](https://github.com/ocaml-opam/opam-publish/releases/tag/2.7.1).

## Try the new opam 2.5.0 release candidate:

The upgrade instructions are unchanged:

1. Either from binaries: run

For Unix systems
```
bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.5.0~rc1"
```
or from PowerShell for Windows systems
```
Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) } -Version 2.5.0~rc1"
```
or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.5.0-rc1) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.5.0-rc1#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```

## Changes in opam-publish 2.7.1

In `2.7.0`, opam-publish changed the way user's branch are pushed to their GitHub forks before opening a PR.
Previously this was done using a regular SSH key, but it turns out that some people do not have SSH keys or cannot easily use it in automated contexts.
To improve this, [@filipeom](https://github.com/filipeom) switched the push method to one that uses GitHub's API token that opam-publish already requires.
However some problems could appear when upstream opam-repository changes their GitHub Action workflow files as the `workflow` permission is now sadly required to push to the user's fork.

In `2.7.1`, this is fixed by checking that the token has the right scope, as well as advertising this scope when requesting new tokens ([#180](https://github.com/ocaml-opam/opam-publish/issues/180)). More generally, existing tokens with the wrong permissions could be kept ([#187](https://github.com/ocaml-opam/opam-publish/issues/187)), as well as previous setups using SSH ([#178](https://github.com/ocaml-opam/opam-publish/issues/178)).

This release also adds support for the opam `2.5` API ([#181](https://github.com/ocaml-opam/opam-publish/pull/181), [#173](https://github.com/ocaml-opam/opam-publish/pull/173)).


Please report any issues to [the opam bug-tracker](https://github.com/ocaml/opam/issues)
or [the opam-publish bug-tracker](https://github.com/ocaml/opam-publish/issues).

Happy hacking!
