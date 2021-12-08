title: "opam 2.1.2 release"
authors: [
  "Kate Deplaix - OCamlLabs" {"mailto:kit.ty.kate(à)disroot.org"}
  "David Allsopp - OCamlLabs" {"mailto:David.Allsopp(à)cl.cam.ac.uk"}
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Louis Gesbert - OCamlPro" {"mailto:louis.gesbert(à)ocamlpro.com"}
]
date: "2021-12-08"
--BODY--

_Feedback on this post is welcomed on [Discuss](TODO)!_

We are pleased to announce the minor release of [opam 2.1.2](https://github.com/ocaml/opam/releases/tag/2.1.2).

This opam release consist of backported fixes, including:

* Fallback on dnf if yum does not exist on RHEL-based systems [#4825](https://github.com/ocaml/opam/pull/4825)
* Use --no-depexts in CLI 2.0 mode [#4908](https://github.com/ocaml/opam/pull/4908)

---

Opam installation instructions (unchanged):

1. From binaries: run

    ```
    bash -c "sh <(curl -fsSL https://raw.githubusercontent.com/ocaml/opam/master/shell/install.sh) --version 2.1.2"
    ```

    or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.1.2) to your PATH. In this case, don't forget to run `opam init --reinit -ni` to enable sandboxing if you had version 2.0.0~rc manually installed or to update you sandbox script.

2. From source, using opam:

    ```
    opam update; opam install opam-devel
    ```

   (then copy the opam binary to your PATH as explained, and don't forget to run `opam init --reinit -ni` to enable sandboxing if you had version 2.0.0~rc manually installed or to update your sandbox script)

3. From source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.1.2#compiling-this-repo).

We hope you enjoy this new minor version, and remain open to [bug reports](https://github.com/ocaml/opam/issues) and [suggestions](https://github.com/ocaml/opam/issues).
