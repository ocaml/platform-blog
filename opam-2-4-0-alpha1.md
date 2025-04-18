title: "opam 2.4.0 alpha1 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)exn.st"}
  "David Allsopp - Tarides" {"mailto:david(à)tarides.com"}
]
date: "2025-04-18"
--BODY--

We are happy to announce the first alpha release of opam 2.4.0.
You can view the full list of changes in the
[release note](https://github.com/ocaml/opam/releases/tag/2.4.0-alpha1).

This version is an alpha, we invite users to test it to spot previously
unnoticed bugs as we head towards the stable release.

## Try it!

The upgrade instructions are unchanged:

1. Either from binaries: run

For Unix systems
```
bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.4.0~alpha1"
```
or from PowerShell for Windows systems
```
Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) } -Version 2.4.0~alpha1"
```
or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.4.0-alpha1) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.4.0-alpha1#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


## Major changes

* On `opam init` the compiler chosen for the default switch will no longer be `ocaml-system` ([#3509](https://github.com/ocaml/opam/issues/3509))
  This was done because the system compiler (as-is your ocaml installed system wide, e.g. /usr/bin/ocaml) is known to be under-tested and prone to a variety of bugs and configuration issues.
  Removing it from the default compiler allows new-comers a more smooth experience.
  *Note: if you wish to use it anyway, you are always able to do it explicitly using `opam init --compiler=ocaml-system`*

* GNU `patch` and the `diff` command are no longer runtime dependencies. Instead the OCaml `patch` library is used ([#6019](https://github.com/ocaml/opam/issues/6019), [#6052](https://github.com/ocaml/opam/issues/6052), [#3782](https://github.com/ocaml/opam/issues/3782), [ocaml/setup-ocaml#933](https://github.com/ocaml/setup-ocaml/pull/933))
  Doing this we've removed some rarely used features of GNU Patch such as the support of [Context diffs](https://www.gnu.org/software/diffutils/manual/html_node/Example-Context.html).
  The new implementation only supports [Unified diffs](https://www.gnu.org/software/diffutils/manual/html_node/Example-Unified.html) including the [git extended headers](https://git-scm.com/docs/diff-format), however file permission changes via said extended headers have no effect.

* Add Nix support for external dependencies (depexts) by adding support for stateless package managers ([#5982](https://github.com/ocaml/opam/issues/5982)). *Thanks to [@RyanGibb](https://github.com/RyanGibb) for this contribution*

* Fix `opam install <local_dir>` with and without options like `--deps-only` or `--show-action` having unexpected behaviours (([#6248](https://github.com/ocaml/opam/issues/6248)), [#5567](https://github.com/ocaml/opam/issues/5567)) such as:
  * reporting `Nothing to do` despite dependencies or package not being up-to-date
  * asking to install the wrong dependencies


## UI changes

* `opam show` now displays the version number of packages flagged with `avoid-version`/`deprecated` gray ([#6354](https://github.com/ocaml/opam/issues/6354))

* `opam upgrade`: Do not show the message about packages "not up-to-date" when the package is tagged with `avoid-version`/`deprecated` ([#6271](https://github.com/ocaml/opam/issues/6271))

* Fail when trying to pin a package whose definition could not be found instead of forcing interactive edition (e.g. this could happen when making a typo in the package name of a pin-depends) ([#6322](https://github.com/ocaml/opam/issues/6322))


## New commands / options

* Add `opam admin compare-versions` to compare package versions for sanity checks. *Thanks to [@mbarbin](https://github.com/mbarbin) for this contribution*

* Add `opam lock --keep-local` to keep local pins url in `pin-depends` field ([#4897](https://github.com/ocaml/opam/issues/4897))

* Add `opam admin migrate-extrafiles` which moves all `extra-files` of an existing opam repository into `extra-sources`. *Thanks to [@hannesm](https://github.com/hannesm) for this contribution*

* The `-i`/`--ignore-test-doc` argument has been removed from `opam admin check` ([#6335](https://github.com/ocaml/opam/issues/6335))


## Other noteworthy changes

* `opam pin`/`opam pin list` now displays the current revision of a pinned repository in a new column. *Thanks to [@desumn](https://github.com/desumn) for this contribution*

* Symlinks in repositories are no longer supported ([#5892](https://github.com/ocaml/opam/issues/5892))

* Fix sandboxing support in NixOS ([#6333](https://github.com/ocaml/opam/issues/6333))

* Add the `OPAMSOLVERTOLERANCE` environment variable to allow users to fix solver timeouts for good ([#3230](https://github.com/ocaml/opam/issues/3230))

* Fix a regression on `opam upgrade <package>` upgrading unrelated packages ([#6373](https://github.com/ocaml/opam/issues/6373)). *Thanks to [@AltGr](https://github.com/AltGr) for this contribution*

* Fix `pin-depends` for `with-*` dependencies when creating a lock file ([#5428](https://github.com/ocaml/opam/issues/5428))

* `opam admin check` now sets `with-test` and `with-doc` to `false` instead of `true`

* Add `apt-rpm`/ALTLinux family support for depexts. *Thanks to [@RiderALT](https://github.com/RiderALT) for this contribution*

* Fix the detection of installed external packages on OpenBSD to not just consider manually installed packages ([#6362](https://github.com/ocaml/opam/issues/6362)). *Thanks to [@semarie](https://github.com/semarie) for this contribution*

* Disable the detection of available system packages on SUSE-based distributions ([#6426](https://github.com/ocaml/opam/issues/6426))


Various performance and other improvements were made and bugs were fixed.
API changes are also denoted in the release note linked above.
This release also includes a handful of improvement the documentation and more than two dozen improvement and extensions to our testsuite.



Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Happy hacking!
