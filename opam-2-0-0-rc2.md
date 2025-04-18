title: "opam 2.0.0 Release Candidate 2 is out!"
authors: "Louis Gesbert - OCamlPro" {"mailto:louis.gesbert(à)ocamlpro.com"}
date: "2018-05-22"
--BODY--

We are pleased to announce the release of a second release candidate for opam 2.0.0.

This new version brings us very close to a final 2.0.0 release, and in addition to many fixes, features big performance enhancements over the RC1.

Among the new features, we have squeezed in full sandboxing of package commands for both Linux and macOS, to protect our users from any [misbehaving scripts](http://opam.ocaml.org/blog/camlp5-system/).

> NOTE: if upgrading manually from 2.0.0~rc, you need to run
> `opam init --reinit -ni` to enable sandboxing.

The new release candidate also offers the possibility to setup a hook in your shell, so that you won't need to run `eval $(opam env)` anymore. This is specially useful in combination with local switches, because with it enabled, you are guaranteed that running `make` from a project directory containing a local switch will use it.

The documentation has also been updated, and a preview of the opam 2 webpages can be browsed at http://opam.ocaml.org/2.0-preview/ (please report issues [here](https://github.com/ocaml/opam2web/issues)). This provides the list of packages available for opam 2 (the `2.0` branch of [opam-repository](https://github.com/ocaml/opam-repository/tree/2.0.0)), including the [compiler packages](https://opam.ocaml.org/2.0-preview/packages/ocaml-base-compiler/).

Installation instructions:

1. From binaries: run

    ```
    sh <(curl -sL https://opam.ocaml.org/install.sh)
    ```

    or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.0.0-rc2) to your PATH. In this case, don't forget to run `opam init --reinit -ni` to enable sandboxing if you had version 2.0.0~rc manually installed.

2. From source, using opam:

    ```
    opam update; opam install opam-devel
    ```

   (then copy the opam binary to your PATH as explained, and don't forget to run `opam init --reinit -ni` to enable sandboxing if you had version 2.0.0~rc manually installed)

3. From source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.0.0-rc2#compiling-this-repo).

Thanks a lot for testing out this new RC and [reporting](https://github.com/ocaml/opam/issues) any issues you may find.


> NOTE: this article is cross-posted on [opam.ocaml.org](https://opam.ocaml.org/blog/) and [ocamlpro.com](http://www.ocamlpro.com/category/blog/). Please head to the latter for the comments!
