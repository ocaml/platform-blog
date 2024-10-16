title: "opam 2.2.0 beta3 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)outlook.com"}
  "David Allsopp - Tarides" {"mailto:david(à)tarides.com"}
]
date: "2024-06-10"
--BODY--

_Feedback on this post is welcomed on [Discuss](https://discuss.ocaml.org/t/ann-opam-2-2-0-beta3/14772)!_

We are very pleased to announce the third and final beta release of opam 2.2.0.

We've done our best to polish everything and squash as many bugs as possible in order
to be ready for the final release. You can view the full list of changes in the
[release note](https://github.com/ocaml/opam/releases/tag/2.2.0-beta3).

This version is a beta, we invite users to test it to spot previously
unnoticed bugs as we near the stable release.

## Changes

* Greatly enhance the opam init user experience on Windows, and the number
  of recognised configurations
* New option `opam init --cygwin-extra-packages=CYGWIN_PKGS --cygwin-internal-install`,
  to specify additional packages for the internal Cygwin installation
* Redirect the opam root to `C:\opamroot\opam-xxx` when the opam root contains spaces on Windows
* Out-of-the-box UTF-8 paged `--help` on Windows
* Fix a performance regression when calling `opam install --deps-only` on an already installed package
* Fix several edge cases related to environment reverting
* Fixed some issues that could appear when upgrading from previous versions of opam
* Fix various issues with `opam tree --with-test`
* Fix parsing opam 2.1 switch import files containing extra-files
* Fix download URLs containing invalid characters on Windows
* Fix some failure cases when extracting tarballs which contain symlinks on Windows
 
Various other general and performance improvements were made and bugs were fixed.
API changes are denoted in the
[release note](https://github.com/ocaml/opam/releases/tag/2.2.0-beta3).
This release also includes PRs improving the documentation and improving
and extending the tests.

## Try it on Windows!

**BEWARE: the command shown below is experimental, use caution and please do report any issues that you are experiencing. If you prefer to not use our experimental script, feel free to get the Windows binary directly from [the Release Page](https://github.com/ocaml/opam/releases/tag/2.2.0-beta3) and put it in your directory of choice instead.**

Now that the [Windows support was merged in opam-repository](https://github.com/ocaml/opam-repository/pull/25861),
testing is as simple as calling the following command from a PowerShell terminal:
```
Invoke-Expression "& { $(Invoke-RestMethod https://raw.githubusercontent.com/kit-ty-kate/opam/windows-installer/shell/install.ps1) }"
```
opening a new terminal, and a simple `opam init` will work out-of-the-box.

## Try it on other platforms!

In case you plan a possible rollback, you may want to first backup your
`~/.opam` directory.

The upgrade instructions are unchanged:

1. Either from binaries: run

    ```
    bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.2.0~beta3"
    ```

    or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.2.0-beta3) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.2.0-beta3#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Thanks for trying this new release out, and we hope you will enjoy the new features!
