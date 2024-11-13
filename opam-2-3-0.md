title: "opam 2.3.0 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)outlook.com"}
  "David Allsopp - Tarides" {"mailto:david(à)tarides.com"}
]
date: "2024-11-13"
--BODY--

_Feedback on this post is welcomed on [Discuss](https://discuss.ocaml.org/t/ann-opam-2-3-0-is-out/XXXXXX)!_

As mentioned in [our talk at the OCaml Workshop 2024](https://icfp24.sigplan.org/details/ocaml-2024-papers/10/Opam-2-2-and-beyond),
we decided to switch to a time-based release cycle (every 6 months), starting with opam 2.3.

As promised, we are very pleased to announce the release of opam 2.3.0, and encourage all users to upgrade. Please read on for installation and upgrade instructions.

## Try it!

In case you plan a possible rollback, you may want to first backup your
`~/.opam` or `$env:LOCALAPPDATA\opam` directory.

The upgrade instructions are unchanged:

1. Either from binaries: run

For Unix systems
```
bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh)"
```
or from PowerShell for Windows systems
```
Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) }"
```
or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.3.0) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.3.0#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```

## Major breaking change: extra-files

When loading a repository, opam now ignores files in packages' `files/` directories which aren't listed in the `extra-files` field of the opam file.
This was done to simplify the opam specification where we hope the opam file to be the only thing that you have to look at when reading a package specification. The optionality of the `extra-files:` field goes against that principle. This change also reduces the surface area for potential file corruption as it ensures that extra-files are checked against their checksums.

This is a breaking change and means that if you are using the `files/` directory without the `extra-files:` field, you need to make sure that all files in that directory are listed in the `extra-files` field.
Once done, the resulting opam file is backward-compatible and you don't need to worry about anything else.

If you have an opam repository, you should make sure all files are listed so every packages continues to work without any issue, which can be done automatically using the `opam admin update-extrafiles` command.

## Major changes

* Packages requiring an unsupported version of opam are now marked unavailable, instead of causing a repository error. This means an opam repository can now allow smoother upgrade in the future where some packages can require a newer version of opam without having to fork the repository to upgrade every package to that version as was done for the upgrade from opam 1.2 to 2.0

* Add a new `opam list --latests-only` option to list only the latest versions of packages. Note that this option is respects the order options were given on the command line. For example: `--available --latests-only` will first list all the available packages, then choose only the latest packages in that set; while `--latests-only --available` will first list all the latest packages, then only show the ones that are available in that set

* Fix and improve `opam install --check`, which now checks if the whole dependency tree of the package is installed instead of only the root dependencies

* Add a new `--verbose-on` option to enable verbose output for specified package names. *Thanks to [@desumn](https://github.com/desumn) for this contribution*

* Add a new `opam switch import --deps-only` option to install only the dependencies of the root packages listed in the opam switch export file

* `opam switch list-available` will now not display compilers flagged with `avoid-version`/`deprecated` unless `--all` is given, meaning that the "trunk" build of OCaml no longer appears to be the latest version

* `opam switch create --repos` now accepts git URLs suffixed with `.git` instead of requiring the `git+https://` protocol prefix. This is consistant with other commands such as `opam repository add`. *Thanks to [@Keryan-dev](https://github.com/Keryan-dev) for this contribution*

* `opam switch set-invariant` now displays the switch invariant using the same syntax as the `--invariant` flag

* The `builtin-0install` solver was improved and should now be capable of being your default solver instead of `builtin-mccs+glpk`. It was previously mostly only suited for automated tasks such as Continuous Integration. If you wish to give it a try, simply call `opam option solver=builtin-0install`

* Most of the unhelpful conflict messages were fixed. ([#4373](https://github.com/ocaml/opam/issues/4373))

* Fix the value of the `arch` variable when the current OS is 32bit on a 64bit machine (e.g. Raspberry Pi OS). ([#5949](https://github.com/ocaml/opam/issues/5949))

* opam now fails when git submodules fail to update instead of ignoring the error and just showing a warning

* opam's libraries now compile with OCaml >= 5.0 on Windows

* Fix an opam 2.1 regression where the initial pin of a local VCS directory would store untracked and ignored files.
  Those files would usually be cleaned before building the package, however git submodules would not be cleaned and would cause issues when paired with the new behaviour added in 2.3.0\~alpha1 which makes opam error when git submodules fail to update (it was previously a warning). ([#5809](https://github.com/ocaml/opam/issues/5809))

* Fix the installed packages internal cache, which was storing the wrong version of the opam file after a build failure.
  This could be triggered easily for users with custom repositories with non-populated extra-files. ([#6213](https://github.com/ocaml/opam/pull/6213))

* Several improvements to the pre-built release binaries were made:
  * The Linux binaries are now built on Alpine 3.20
  * The FreeBSD binary is now built on FreeBSD 14.1
  * The OpenBSD binary is now built on OpenBSD 7.6 and looses support for OpenBSD 7.5 and older
  * A Linux/riscv64 binary is now available
  * A NetBSD/x86_64 binary is now available


And many other general and performance improvements were made and bugs were fixed.
You can take a look to previous blog posts.
API changes and a more detailed description of the changes are listed in:
- [the release note for 2.3.0~alpha1](https://github.com/ocaml/opam/releases/tag/2.3.0-alpha1)
- [the release note for 2.3.0~beta1](https://github.com/ocaml/opam/releases/tag/2.3.0-beta1)
- [the release note for 2.3.0~beta2](https://github.com/ocaml/opam/releases/tag/2.3.0-beta2)
- [the release note for 2.3.0~rc1](https://github.com/ocaml/opam/releases/tag/2.3.0-rc1)
- [the release note for 2.3.0](https://github.com/ocaml/opam/releases/tag/2.3.0)

This release also includes PRs improving the documentation and improving
and extending the tests.


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

We hope you will enjoy the new features of opam 2.3!
