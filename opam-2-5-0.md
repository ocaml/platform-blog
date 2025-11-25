title: "opam 2.5.0 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)exn.st"}
  "David Allsopp - Tarides" {"mailto:david(à)tarides.com"}
]
date: "2025-11-26"
--BODY--

We're happy to announce the release of opam 2.5.0 and encourage all users to upgrade.
Please read on for installation and upgrade instructions.

## Try it!

The upgrade instructions are unchanged:

1. Either from binaries: run

For Unix systems
```
bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.5.0"
```
or from PowerShell for Windows systems
```
Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) } -Version 2.5.0"
```
or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.5.0) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.5.0#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```

## Major changes: speedup opam update up to 70%

Thanks to [@arozovyk](https://github.com/arozovyk), `opam update` now loads opam
file incrementally, only parsing the files that have changed since the last
time you called `opam update`. Before that, opam files in opam repositories were
all loaded from the file system after an update if there was any change. The performance improvement of this change thus
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

On top of that, for `bash` users, we've also changed the default file to which
`opam init` writes the opam shell integration to be `.bashrc` instead of the
previous `.profile` or `.bash_profile`.  Doing it this way prevents some issues
with existing `.profile` files that source the `.bashrc` file and causing an
infinity loop when opam asks users to make sure to source their
`.bashrc` file into their `.profile` file.
([#5819](https://github.com/ocaml/opam/issues/5819), [#4201](https://github.com/ocaml/opam/issues/4201), [#3990](https://github.com/ocaml/opam/issues/3990))

We invite users to test this change for the editor integration, to spot any bug early.

## Change to the install script

The opam install script now installs an appropriate `apparmor` profile on
systems configured with `apparmor`.  This change is not strictly speaking
related to this release as it is deployed for every versions.
([#5968](https://github.com/ocaml/opam/issues/5968))

We invite `apparmor` users (enabled by default on Ubuntu), to test this feature and
let us know if some adjustments are needed.

## Other noteworthy changes

* Allow the macOS sandbox to write in the `/var/folders/` and `/var/db/mds/` directories as it is required by some macOS core tools ([#4389](https://github.com/ocaml/opam/issues/4389), [#6460](https://github.com/ocaml/opam/issues/6460))

* Archives without checksums are now fetched only once per call of `opam install` if their url match ([#5638](https://github.com/ocaml/opam/issues/5638))

* `extra-files` whose name is invalid now make opam fail early instead of ignoring the error ([#6679](https://github.com/ocaml/opam/issues/6679))

* The specification of the `pkg:opamfile` variable was refined and its computation was updated accordingly ([#5346](https://github.com/ocaml/opam/issues/5346))

* The global option `default-invariant` is now modifiable. This is used as default value when creating a local switch ([#6730](https://github.com/ocaml/opam/issues/6730))

* opam now makes sure that git submodules are up-to-date when checking if a source git repository is up-to-date. This caused issues on subsequent package updates when git submodules originally failed to download or when the submodules got updated in some cases ([#6132](https://github.com/ocaml/opam/issues/6132))

* Fix `opam install pkg --depext-only` exiting with code 0 instead of 20 (not found) ([#6488](https://github.com/ocaml/opam/issues/6488))

* Fix the parsing of `OPAMFETCH` (support quotes / proper POSIX shell syntax) ([#5490](https://github.com/ocaml/opam/issues/5490))

* Fix `opam remove --force` that was launching commands in current directory ([#6570](https://github.com/ocaml/opam/issues/6570))

* Stop `opam switch create --dry-run` from creating any directory. *Thanks to [@hannesm](https://github.com/hannesm) for this contribution.* ([#5918](https://github.com/ocaml/opam/issues/5918))

* Fix `opam lock`'s error message on pinned packages with no git remote by handling exit code 2 from `git remote get-url` ([#6422](https://github.com/ocaml/opam/issues/6422))

* `getconf` was removed from the list of required runtime tools, which allows `opam init` to work out-of-the-box on Haiku ([#6632](https://github.com/ocaml/opam/issues/6632))

* `./configure --enable-static` is now supported on OpenBSD. This mode is used in the prebuilt release binaries ([#6705](https://github.com/ocaml/opam/issues/6705), thanks to [@flumf](https://github.com/flumf))

Half a dozen UI improvements, such as improved error messages, handling of uncaught exceptions, displaying the invalid character in erroneous package names (contributed by [@lefessan](https://github.com/lefessan)), etc.


This release also includes a handful of improvement the documentation and more than two dozen improvement and extensions to our testsuite.


Various performance and other improvements were made and bugs were fixed.
You can take a look to previous blog posts.
API changes and a more detailed description of the changes are listed in:
- [the release note for 2.5.0~alpha1](https://github.com/ocaml/opam/releases/tag/2.5.0-alpha1)
- [the release note for 2.5.0~beta1](https://github.com/ocaml/opam/releases/tag/2.5.0-beta1)
- [the release note for 2.5.0~rc1](https://github.com/ocaml/opam/releases/tag/2.5.0-rc1)
- [the release note for 2.5.0](https://github.com/ocaml/opam/releases/tag/2.5.0)


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Happy hacking!
