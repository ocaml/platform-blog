title: "opam 2.0.4 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Louis Gesbert - OCamlPro" {"mailto:louis.gesbert(à)ocamlpro.com"}
]
date: "2019-04-09"
--BODY--

We are pleased to announce the release of [opam 2.0.4](https://github.com/ocaml/opam/releases/tag/2.0.4).

This new version contains some [backported fixes](https://github.com/ocaml/opam/pull/3805):
* Sandboxing on MacOS: considering the possibility that TMPDIR in unset
* display: Fix `opam config var` display, aligned on `opam config list`
* pin:
  * update source of (version) pinned directory
  * fix `--ignore-pin-depends` with autopin
  * fix pinnings not installing/upgrading already pinned packages (introduced in 2.0.2)
* opam clean: Ignore errors trying to remove directories
* remove wrong _mismatching extra-files_ warning
* urls: fix hg opam 1.2 url parsing
* lint: update message of warning 47
* system:
  * lock & signals: don't interrupt at non terminal signals
  * shell: fix fish manpath setting
  * git: use `diff.noprefix=false` config argument to overwrite user defined configuration
* dirtrack: fix precise tracking mode

> Note: To homogenise MacOS name on system detection, we decided to keep `macos`, and convert `darwin` to `macos` in opam. For the moment, to not break jobs & CIs, we keep uploading `darwin` & `macos` binaries, but from the 2.1.0 release, only `macos` ones will be kept.

---

Installation instructions (unchanged):

1. From binaries: run

    ```
    sh <(curl -sL https://raw.githubusercontent.com/ocaml/opam/master/shell/install.sh)
    ```

    or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.0.4) to your PATH. In this case, don't forget to run `opam init --reinit -ni` to enable sandboxing if you had version 2.0.0~rc manually installed or to update you sandbox script.

2. From source, using opam:

    ```
    opam update; opam install opam-devel
    ```

   (then copy the opam binary to your PATH as explained, and don't forget to run `opam init --reinit -ni` to enable sandboxing if you had version 2.0.0~rc manually installed or to update you sandbox script)

3. From source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.0.4#compiling-this-repo).

We hope you enjoy this new major version, and remain open to [bug reports](https://github.com/ocaml/opam/issues) and [suggestions](https://github.com/ocaml/opam/issues).

> NOTE: this article is cross-posted on [opam.ocaml.org](https://opam.ocaml.org/blog/) and [ocamlpro.com](http://www.ocamlpro.com/category/blog/). Please head to the latter for the comments!
