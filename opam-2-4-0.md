title: "opam 2.4.0 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)exn.st"}
  "David Allsopp - Tarides" {"mailto:david(à)tarides.com"}
]
date: "2025-07-18"
--BODY--

_Feedback on this post is welcomed on [Discuss](https://discuss.ocaml.org/t/ann-opam-2-4-0-is-out/16989)!_

We are extremely happy to announce the release of opam 2.4.0 and encourage all users to upgrade.
Please read on for installation and upgrade instructions.

## Try it!

The upgrade instructions are unchanged:

1. Either from binaries: run

For Unix systems
```
bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.4.0"
```
or from PowerShell for Windows systems
```
Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) } -Version 2.4.0"
```
or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.4.0) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.4.0#compiling-this-repo).


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

* Fix `opam install <local_dir>` with and without options like `--deps-only` or `--show-action` having unexpected behaviours ([#6248](https://github.com/ocaml/opam/issues/6248), [#5567](https://github.com/ocaml/opam/issues/5567)) such as:
  * reporting `Nothing to do` despite dependencies or package not being up-to-date
  * asking to install the wrong dependencies

* `opam switch create [name] <version>` will not include compiler packages flagged with `avoid-version`/`deprecated` in the generated invariant anymore, unless compiler flagged `avoid-version`/`deprecated` are the only ones available ([#6494](https://github.com/ocaml/opam/pull/6494)). This will allow opam to avoid the use of the `ocaml-system` package unless actually explicitly requested by the user. The opam experience when the `ocaml-system` compiler is used is known to be prone to a variety of bugs and configuration issues.

* `opam install --deps-only` no longer requires unicity of package version between the request and the installed packages. In other words, if you have `pkg.1` installed, installing the dependencies of `pkg.2` no longer removes `pkg.1` if the installed packages are compatible. This also allows to install dependencies of conflicting packages when their dependencies are compliant. ([#6520](https://github.com/ocaml/opam/issues/6520))


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

* The prebuilt Windows binary now includes Cygwin's `setup-x86_64.exe` in the binary itself as fallback in case `cygwin.com` is inaccessible ([#6538](https://github.com/ocaml/opam/issues/6538)). Opam will also no longer fail if `cygwin.com` is inaccessible when checking for upgrades of `setup-x86_64.exe` ([#6495](https://github.com/ocaml/opam/issues/6495), partial fix for [#6474](https://github.com/ocaml/opam/issues/6474))

* Symlinks in repositories are no longer supported ([#5892](https://github.com/ocaml/opam/issues/5892))

* Fix sandboxing support in NixOS ([#6333](https://github.com/ocaml/opam/issues/6333))

* Add the `OPAMSOLVERTOLERANCE` environment variable to allow users to fix solver timeouts for good ([#3230](https://github.com/ocaml/opam/issues/3230))

* Fix a regression on `opam upgrade <package>` upgrading unrelated packages ([#6373](https://github.com/ocaml/opam/issues/6373)). *Thanks to [@AltGr](https://github.com/AltGr) for this contribution*

* Fix `pin-depends` for `with-*` dependencies when creating a lock file ([#5428](https://github.com/ocaml/opam/issues/5428))

* `opam admin check` now sets `with-test` and `with-doc` to `false` instead of `true`

* Add `apt-rpm`/ALTLinux family support for depexts. *Thanks to [@RiderALT](https://github.com/RiderALT) for this contribution*

* Remove handling of the `OPAMSTATS` environment variable ([#6485](https://github.com/ocaml/opam/pull/6485)). *Thanks to [@hannesm](https://github.com/hannesm) for this contribution*

* Fix the detection of installed external packages on OpenBSD to not just consider manually installed packages ([#6362](https://github.com/ocaml/opam/issues/6362)). *Thanks to [@semarie](https://github.com/semarie) for this contribution*

* Disable the detection of available system packages on SUSE-based distributions ([#6426](https://github.com/ocaml/opam/issues/6426))


And many other general, performance and UI improvements were made and bugs were fixed.
You can take a look to previous blog posts.
API changes and a more detailed description of the changes are listed in:
- [the release note for 2.4.0~alpha1](https://github.com/ocaml/opam/releases/tag/2.4.0-alpha1)
- [the release note for 2.4.0~alpha2](https://github.com/ocaml/opam/releases/tag/2.4.0-alpha2)
- [the release note for 2.4.0~beta1](https://github.com/ocaml/opam/releases/tag/2.4.0-beta1)
- [the release note for 2.4.0~rc1](https://github.com/ocaml/opam/releases/tag/2.4.0-rc1)
- [the release note for 2.4.0](https://github.com/ocaml/opam/releases/tag/2.4.0)

This release also includes PRs improving the documentation and improving
and extending the tests.


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

We hope you will enjoy the new features of opam 2.4!


---
*Special thanks to the whole haematology department of the NHS Greater Glasgow for making this release – in the state that it is – possible. Fighting cancer is not easy but it is much more bearable with great people <3*
