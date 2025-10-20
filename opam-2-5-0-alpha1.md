title: "opam 2.5.0 alpha1 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)exn.st"}
  "David Allsopp - Tarides" {"mailto:david(à)tarides.com"}
]
date: "2025-10-20"
--BODY--

We are happy to announce the first alpha release of opam 2.5.0.
You can view the full list of changes in the
[release note](https://github.com/ocaml/opam/releases/tag/2.5.0-alpha1).

This version is an alpha, we invite users to test it to spot previously
unnoticed bugs as we head towards the stable release.

## Try it!

The upgrade instructions are unchanged:

1. Either from binaries: run

For Unix systems
```
bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.5.0~alpha1"
```
or from PowerShell for Windows systems
```
Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) } -Version 2.5.0~alpha1"
```
or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.5.0-alpha1) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.5.0-alpha1#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


## Major changes: speedup opam update up to 70%

Thanks to [@arozovyk](https://github.com/arozovyk), `opam update` now load opam
file incrementally, only parsing the files that have changed since the last
time you called `opam update`. Before that, the repository was reading all
repository files to load them. The performance improvement of this change thus
depends on how often you call `opam update` and what type of repository and OS
you are using.
([#5824](https://github.com/ocaml/opam/issues/5824))

## Major changes: improved shell integration

A number of users have been hitting issues with opam's shell integration where
parts of a previous environment was kept in the current environment, causing a
number of issues.  These can be triggered by, for example, nuking your opam
root directory (by default `~/.opam` or `%LocalAppData%\opam`). For this
particular case we are still working on a fix, but many other users have
reported similar issues without nuking their root directory and in that case we
believe to have fixed the majority of issues.
([dbuenzli/topkg#142](https://github.com/dbuenzli/topkg/issues/142), [#4649](https://github.com/ocaml/opam/issues/4649), [#5761](https://github.com/ocaml/opam/issues/5761))

On top of that we've also changed for `bash` users the default file to which
`opam init` write the opam shell integration to be `.bashrc` instead of the
previous `.profile` or `.bash_profile`.  Doing it this way prevents some issues
with existing `.profile` files that source the `.bashrc` file and causing an
infinity loop when opam asks users to make sure to source their
`.bashrc` file into their `.profile` file.
([#5819](https://github.com/ocaml/opam/issues/5819), [#4201](https://github.com/ocaml/opam/issues/4201), [#3990](https://github.com/ocaml/opam/issues/3990))

We incite users to test this change for the editor integration, to spot any bug early.

## Change to the install script

The opam install script now installs an appropriate `apparmor` profile on
systems configured with `apparmor`.  This change is not strictly speaking
related to this release as it is deployed for every versions.
([#5968](https://github.com/ocaml/opam/issues/5968))

We incite `apparmor` users, especially Ubuntu ones, to test this feature and
let us know if some adjustments are needed.

## Other noteworthy changes

* Archives without checksums now fetch only once per call of `opam install` if their url match ([#5638](https://github.com/ocaml/opam/issues/5638))

* Do not ignore `extra-files` whose name is invalid and fail early in that case ([#6679](https://github.com/ocaml/opam/issues/6679))

* Refine the specification of `pkg:opamfile` variable and update its computation accordingly ([#5346](https://github.com/ocaml/opam/issues/5346))

* Make global option `default-invariant` modifiable ([#6730](https://github.com/ocaml/opam/issues/6730))

* Check the status of git submodules when checking if a repository is up-to-date ([#6132](https://github.com/ocaml/opam/issues/6132))

* Fix `opam install pkg --depext-only` exiting with code 0 instead of 20 (not found) ([#6488](https://github.com/ocaml/opam/issues/6488))

* Fix parsing of `OPAMFETCH` (support quotes / proper POSIX shell syntax) ([#5490](https://github.com/ocaml/opam/issues/5490))

* Fix `opam remove --force` that was launching commands in current directory ([#6570](https://github.com/ocaml/opam/issues/6570))

* Fix `opam lock`'s error message on pinned packages with no git remote by handling exit code 2 from `git remote get-url` ([#6422](https://github.com/ocaml/opam/issues/6422))

* Remove `getconf` from the list of required runtime tools, which allows `opam init` to work out-of-the-box on Haiku ([#6632](https://github.com/ocaml/opam/issues/6632))

* `./configure --enable-static` is now supported on OpenBSD. This mode is used in the prebuilt release binaries ([#6705](https://github.com/ocaml/opam/issues/6705), thanks to [@flumf](https://github.com/flumf))

Half a dozen UI improvements, such as improved error messages, handling of uncaught exceptions, displaying the invalid character in erroneous package names (contributed by [@lefessan](https://github.com/lefessan)), etc.


Various performance and other improvements were made and bugs were fixed.
API changes are also denoted in the release note linked above.
This release also includes a handful of improvement the documentation and more than two dozen improvement and extensions to our testsuite.


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Happy hacking!
