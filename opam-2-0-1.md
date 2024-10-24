title: "opam 2.0.1 is out!"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Louis Gesbert - OCamlPro" {"mailto:louis.gesbert(à)ocamlpro.com"}
]
date: "2018-10-24"
--BODY--

We are pleased to announce the release of [opam 2.0.1](https://github.com/ocaml/opam/releases/tag/2.0.1).

This new version contains mainly [backported fixes](https://github.com/ocaml/opam/pull/3560), some platform-specific:
- Cold boot for MacOS/CentOS/Alpine
- Install checksum validation on MacOS
- Archive extraction for OpenBSD now defaults to using `gtar`
- Fix compilation of mccs on MacOS and Nix platforms
- Do not use GNU-sed specific features in the release Makefile, to fix build on OpenBSD/FreeBSD
- Cleaning to enable reproducible builds
- Update configure scripts

And some opam specific:
- git: fix git fetch by sha1 for git < 2.14
- linting: add `test` variable warning and empty description error
- upgrade: convert pinned but not installed opam files
- error reporting: more comprehensible error message for tar extraction, and upgrade of git-url compilers
- opam show: upgrade given local files
- list: as opam 2.0.0 `list` doesn't return non-zero code if list is empty, add `--silent` option for a silent output and returns 1 if list is empty

---

Installation instructions (unchanged):

1. From binaries: run

    ```
    sh <(curl -sL https://opam.ocaml.org/install.sh)
    ```

    or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.0.1) to your PATH. In this case, don't forget to run `opam init --reinit -ni` to enable sandboxing if you had version 2.0.0~rc manually installed.

2. From source, using opam:

    ```
    opam update; opam install opam-devel
    ```

   (then copy the opam binary to your PATH as explained, and don't forget to run `opam init --reinit -ni` to enable sandboxing if you had version 2.0.0~rc manually installed)

3. From source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.0.1#compiling-this-repo).

We hope you enjoy this new major version, and remain open to [bug reports](https://github.com/ocaml/opam/issues) and [suggestions](https://github.com/ocaml/opam/issues).

> NOTE: this article is cross-posted on [opam.ocaml.org](https://opam.ocaml.org/blog/) and [ocamlpro.com](http://www.ocamlpro.com/category/blog/). Please head to the latter for the comments!
