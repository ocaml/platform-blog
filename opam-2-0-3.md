title: "opam 2.0.3 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Louis Gesbert - OCamlPro" {"mailto:louis.gesbert(à)ocamlpro.com"}
]
date: "2019-01-28"
--BODY--

We are pleased to announce the release of [opam 2.0.3](https://github.com/ocaml/opam/releases/tag/2.0.3).

This new version contains some [backported fixes](https://github.com/ocaml/opam/pull/3715):
* Fix manpage remaining $ (OPAMBESTEFFORT)
* Fix OPAMROOTISOK handling
* Regenerate missing environment file

---

Installation instructions (unchanged):

1. From binaries: run

    ```
    sh <(curl -sL https://opam.ocaml.org/install.sh)
    ```

    or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.0.3) to your PATH. In this case, don't forget to run `opam init --reinit -ni` to enable sandboxing if you had version 2.0.0~rc manually installed or to update you sandbox script.

2. From source, using opam:

    ```
    opam update; opam install opam-devel
    ```

   (then copy the opam binary to your PATH as explained, and don't forget to run `opam init --reinit -ni` to enable sandboxing if you had version 2.0.0~rc manually installed or to update you sandbox script)

3. From source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.0.3#compiling-this-repo).

We hope you enjoy this new major version, and remain open to [bug reports](https://github.com/ocaml/opam/issues) and [suggestions](https://github.com/ocaml/opam/issues).

> NOTE: this article is cross-posted on [opam.ocaml.org](https://opam.ocaml.org/blog/) and [ocamlpro.com](http://www.ocamlpro.com/category/blog/). Please head to the latter for the comments!
