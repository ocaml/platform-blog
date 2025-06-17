title: "opam 2.4.0 beta1 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)exn.st"}
  "David Allsopp - Tarides" {"mailto:david(à)tarides.com"}
]
date: "2025-06-18"
--BODY--

We are happy and relieved to announce the first beta release of opam 2.4.0.
You can view the full list of changes in the
[release note](https://github.com/ocaml/opam/releases/tag/2.4.0-beta1).

This version is an beta, we invite users to test it to spot previously
unnoticed bugs as we head towards the stable release, which hopefully
shouldn't take too long.

## Try it!

The upgrade instructions are unchanged:

1. Either from binaries: run

For Unix systems
```
bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.4.0~beta1"
```
or from PowerShell for Windows systems
```
Invoke-Expression "& { $(Invoke-RestMethod https://opam.ocaml.org/install.ps1) } -Version 2.4.0~beta1"
```
or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.4.0-beta1) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.4.0-beta1#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


## Changes

* Fixed some bugs in `opam install --deps-only` (and other commands simulating package pins, such as `--depext-only`) more visible in 2.4:
  * When a package `pkg` is already installed and `opam install ./pkg --deps` is called, if there is a conflict between the installed `pkg` dependencies and the definition of the local `pkg`, the conflict was not seen and the already installed `pkg` was kept ([#6529](https://github.com/ocaml/opam/issues/6529))
  * No longer fetch and write the sources when simulating packages that were already pinned ([#6532](https://github.com/ocaml/opam/issues/6532))
  * opam was triggering the reinstall of the package based on the already pinned packages instead of the expected newly simulated pinned packages ([#6501](https://github.com/ocaml/opam/issues/6501))
  * opam was using the opam description of the wrong package in some cases ([#6535](https://github.com/ocaml/opam/issues/6535))
* Change the behaviour of `--deps-only`, where it no longer requires unicity of package version between the request and the installed packages. In other words, if you have `pkg.1` installed, installing dependencies of `pkg.2` no longer removes `pkg.1`. This also allows to install dependencies of conflicting packages when their dependencies are compliant. ([#6520](https://github.com/ocaml/opam/issues/6520))
  
### Regression fixes from 2.4.0~alpha1
* Fix a regression where the internal `sources` directory was removed unexpectedly on reinstall actions, making opam re-fetch the package ([#6554](https://github.com/ocaml/opam/issues/6554))
* Fixed a couple of regressions in `opam update`:
  * An unexpected stack overflow exception was raised when updating repositories with large files when opam is compiled with OCaml < 5.1 ([#6513](https://github.com/ocaml/opam/issues/6513))
  * Updating a repository where a line was added at the end of a file without final newline character was making the update fail ([#6527](https://github.com/ocaml/opam/issues/6527))
    
### Build changes
* Bump the downloaded-if-missing dune to 3.19.0, cppo to 1.8.0, ocamlgraph to 2.2.0, uutf to 1.0.4 and patch to 3.0.0~beta1 ([#6527](https://github.com/ocaml/opam/issues/6527))
* Allows `./configure --without-dune` to build with OCaml 5.4 ([#6527](https://github.com/ocaml/opam/issues/6527))
* Add a `--with-cygwin-setup` option to the `configure` script, only available on Windows and disabled by default, which includes the optionally given `setup-x86_64.exe` binary inside the opam binary. If the option is given without a filename, the file with be fetched from `cygwin.com` ([#6498](https://github.com/ocaml/opam/issues/6498))

### Release scripts
* Improve the prebuilt Windows binaries by including Cygwin's `setup-x86_64.exe` in the binary itself as fallback, in case `cygwin.com` is inaccessible ([#6538](https://github.com/ocaml/opam/issues/6538))
  

API changes are also denoted in the release note linked above.
This release also includes a couple of improvement and extensions to our testsuite.



Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Happy hacking!
