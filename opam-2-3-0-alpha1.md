title: "opam 2.3.0 alpha1 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)outlook.com"}
  "David Allsopp - Tarides" {"mailto:david(à)tarides.com"}
]
date: "2024-09-19"
--BODY--

_Feedback on this post is welcomed on [Discuss](https://discuss.ocaml.org/t/ann-opam-2-3-0-alpha1/15325)!_

As mentioned in [our talk at the OCaml Workshop 2024](https://icfp24.sigplan.org/details/ocaml-2024-papers/10/Opam-2-2-and-beyond),
we decided to switch to a time-based release cycle (every 6 months), starting with opam 2.3.

As promised, we are happy to announce the first alpha release of opam 2.3.0.
You can view the full list of changes in the
[release note](https://github.com/ocaml/opam/releases/tag/2.3.0-alpha1).

This version is an alpha, we invite users to test it to spot previously
unnoticed bugs as we head towards the stable release.

## Major breaking change

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

* Most of the unhelpful conflict messages were fixed

* Fix the value of the `arch` variable when the current OS is 32bit on a 64bit machine (e.g. Raspberry Pi OS)

* opam now fails when git submodules fail to update instead of ignoring the error and just showing a warning

* opam's libraries now compile with OCaml >= 5.0 on Windows

Various performance and other improvements were made and bugs were fixed.
API changes are denoted in the release note linked above.
This release also includes a handful of PRs improving the documentation and more two dozen PRs improving and extending the tests.

## Try it!

The upgrade instructions are unchanged:

1. Either from binaries: run

For Unix systems
```
bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.3.0~alpha1"
```
or from PowerShell for Windows systems
```
Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) } -Version 2.3.0~alpha1"
```
or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.3.0-alpha1) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.3.0-alpha1#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Happy hacking!
