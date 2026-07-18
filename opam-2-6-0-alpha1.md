title: "opam 2.6.0~alpha1 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)exn.st"}
  "Nathan Rebours - OCamlPro" {"mailto:nathan.rebours(à)ocamlpro.com"}
  "David Allsopp - Jane Street" {"mailto:dallsopp(à)janestreet.com"}
]
date: "2026-07-20"
--BODY--

We are happy to announce the first alpha release of opam 2.6.0.
You can view the full list of changes in the
[release note](https://github.com/ocaml/opam/releases/tag/2.6.0-alpha1).

This version is an alpha, we invite users to test it to spot previously
unnoticed bugs as we head towards the stable release.

## Try it!

The upgrade instructions are unchanged:

1. Either from binaries: run

For Unix systems
```
bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.6.0~alpha1"
```
or from PowerShell for Windows systems
```
Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) } -Version 2.6.0~alpha1"
```
or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.6.0-alpha1) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.6.0-alpha1#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


## Major change: in-place env hook

For people using the shell hooks, this release changed the way `PATH` is kept up-to-date. Previously, opam took priority over any other elements of `PATH` by making sure to always be in front.
However this causes problem for users expecting manual `export PATH=some-custom-bindir:$PATH` to prioritise their directory.
To resolve this problem, the shell hook now instead replaces the directory managed by opam in-place, keeping the order asked by the user.

To benefit from this, make sure `opam init --reinit -ni` was ran once after upgrading to this version (automatically done by our install script if it detects an existing opam installation).

([#6859](https://github.com/ocaml/opam/pull/6859), [#6815](https://github.com/ocaml/opam/issues/6815)). *Thanks to [@gridbugs](https://github.com/gridbugs) for this contribution.*

## Major change: reduce the disk space usage of opam

When installing a package, opam doesn't exactly go easy on disk usage. For people with limited disk space it is a problem which can result in a "no space left on device" type error. 
While no-one really can get rid of this type of error completely, this release comes with some quite substential improvements to the disk space used during installs.

In particular the `build` directory is now deleted as soon as possible during a build instead of waiting until the end. ([#6906](https://github.com/ocaml/opam/pull/6906), [#5884](https://github.com/ocaml/opam/issues/5884)).
We also used to cache both the extracted sources and the original archive of packages. However this is redundant and inefficient on some file-systems, thus opam now doesn't cache the extracted sources anymore.
([#6440](https://github.com/ocaml/opam/pull/6440), [#4056](https://github.com/ocaml/opam/issues/4056), [#5448](https://github.com/ocaml/opam/issues/5448)).

While the disk usage used by opam can be reduced over time while simply reinstalling packages, you can liberate some free GB in one go using `opam clean --all-switches`.

## Major change: performance improvements on certain file-systems (e.g. NTFS on Windows or IO constrained machines)

Some file-systems, such as NTFS on Windows famously take forever for commands such as `opam update`, `opam init` or any command upon upgrading opam.
This is due to how opam stored its repositories: i.e. opam-repository is just a large number of directories and files that opam has to scan through to get the informations it needs to run.
Once a repository is read once it's usually not a problem anymore as opam caches it internally, however everytime `opam update` is called the new repository has to be re-read,
which on platforms such as Windows or a busy VPS causes long delays because the files and directories being read are not in the OS's internal cache anymore.

To remedy this while keeping the same repository format, we now leverage the existing `index.tar.gz` file expected to be served by HTTP opam repositories and simply don't extract it.
Instead we now use the `ocaml-tar` library to read the file in-memory, thus only needing one `read` syscall (against tens of thousands previously, counting on OS-level caches to be fast).

While this only helps HTTP repositories (e.g. the default opam-repository), other types of repositories are usually either smaller (local repositories) or less impacted (VCS repositories) and overall less used
than the default HTTP repository so this is less of an issue. However we will still look into it in the future.

([#6625](https://github.com/ocaml/opam/pull/6625), [#5346](https://github.com/ocaml/opam/issues/5346), [#5741](https://github.com/ocaml/opam/issues/5741), [#5648](https://github.com/ocaml/opam/issues/5648), [#5484](https://github.com/ocaml/opam/issues/5484), [#5559](https://github.com/ocaml/opam/issues/5559), [#3050](https://github.com/ocaml/opam/issues/3050), [#6974](https://github.com/ocaml/opam/issues/6974)).

## Other noteworthy changes

* Add `root` and `rootexec` sections to `.install` files to install files from the root prefix ([#6938](https://github.com/ocaml/opam/pull/6938), [#6919](https://github.com/ocaml/opam/issues/6919)). *Thanks to [@WardBrian](https://github.com/WardBrian) for this contribution.*

* Reorder the list of actions by increased priority ([#6864](https://github.com/ocaml/opam/pull/6864), [#6863](https://github.com/ocaml/opam/issues/6863))

* Improved depexts handling by caching system package availability during `opam update`, avoiding redundant system checks at install time ([#6489](https://github.com/ocaml/opam/pull/6489), [#6461](https://github.com/ocaml/opam/issues/6461))

* Allow detection of installed system packages through their virtual names on ALT Linux, RHEL-based and SUSE-based distributions ([#6431](https://github.com/ocaml/opam/pull/6431), [#6426](https://github.com/ocaml/opam/issues/6426))

* Added `--ignore-available-on` option to allow ignoring the `available:` field of certain packages ([#6836](https://github.com/ocaml/opam/pull/6836), [#5283](https://github.com/ocaml/opam/issues/5283)). *Thanks once-again to [@WardBrian](https://github.com/WardBrian) for this contribution.*

* Fix an opam 2.5 regression where `opam pin list` failed abruptly when the source of the pinned package doesn't exist ([#6910](https://github.com/ocaml/opam/pull/6910), [#6597](https://github.com/ocaml/opam/pull/6597))

* `opam update` now supports updating a repository that changed a file to a directory of the same name and vice versa ([#6915](https://github.com/ocaml/opam/pull/6915), [#3830](https://github.com/ocaml/opam/issues/3830))

* Do not fail on directories named `opam` when scanning the `packages` directory of a repository during `opam repo add` or `opam init` (worked on subsequent `opam update`) ([#6995](https://github.com/ocaml/opam/pull/6995))

* Fix "undefined variable" error when a lock file filter contains an undefined variables: fail gracefully with strict mode, continue and default the variable to false otherwise ([#6947](https://github.com/ocaml/opam/pull/6947), [#6946](https://github.com/ocaml/opam/issues/6946))

* Fix `opam lock` support of dependency formula that include disjunctions ([#6990](https://github.com/ocaml/opam/pull/6990), [#6944](https://github.com/ocaml/opam/issues/6944))

* Fix package installation during `opam pin add <url to archive>` ([#7012](https://github.com/ocaml/opam/pull/7012), [#6999](https://github.com/ocaml/opam/issues/6999)). *Thanks to [@zoggy](https://codeberg.org/zoggy) for this contribution.*

* Make `git` calls more deterministic regardless of the global or system config ([#6992](https://github.com/ocaml/opam/pull/6992), [#6937](https://github.com/ocaml/opam/issues/6937))

* Read full lines when asking for user input when `TERM=dumb` (e.g. emacs' `M-x shell`) ([#6829](https://github.com/ocaml/opam/pull/6829), [#6828](https://github.com/ocaml/opam/issues/6828). *Thanks to [@arvidj](https://github.com/arvidj) for this contribution.*


Various performance and other improvements were made and bugs were fixed.
API changes are also denoted in the release note linked above.
This release also includes a handful of improvement the documentation and more than two dozen improvement and extensions to our testsuite.


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Happy hacking!
