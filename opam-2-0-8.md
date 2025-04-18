title: "opam 2.0.8 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Louis Gesbert - OCamlPro" {"mailto:louis.gesbert(à)ocamlpro.com"}
]
date: "2021-02-08"
--BODY--

We are pleased to announce the minor release of [opam 2.0.8](https://github.com/ocaml/opam/releases/tag/2.0.8).

This new version contains some [backported](https://github.com/ocaml/opam/pull/4425) fixes:
 * **Critical for fish users!** Don't add `.` to `PATH`. [[#4078](https://github.com/ocaml/opam/issues/4078)]
 * Fix sandbox script for newer `ccache` versions. [[#4079](https://github.com/ocaml/opam/issues/4079) and [#4087](https://github.com/ocaml/opam/pull/4087)]
 * Fix sandbox crash when `~/.cache` is a symlink. [[#4068](https://github.com/ocaml/opam/issues/4068)]
 * User modifications to the sandbox script are no longer overwritten by `opam init`. [[#4020](https://github.com/ocaml/opam/pull/4092) & [#4092](https://github.com/ocaml/opam/pull/4092)]
 * macOS sandbox script always mounts `/tmp` read-write, regardless of `TMPDIR` [[#3742](https://github.com/ocaml/opam/pull/3742), addressing [ocaml/opam-repository#13339](https://github.com/ocaml/opam-repository/issues/13339)]
 * `pre-` and `post-session` hooks can now print to the console [[#4359](https://github.com/ocaml/opam/issues/4359)]
 * Switch-specific pre/post sessions hooks are now actually run [[#4472](https://github.com/ocaml/opam/issues/4472)]
 * Standalone `opam-installer` now correctly builds from sources [[#4173](https://github.com/ocaml/opam/issues/4173)]
 * Fix `arch` variable detection when using 32bit mode on ARM64 and i486 [[#4462](https://github.com/ocaml/opam/pull/4462)]

A more complete [release note](https://github.com/ocaml/opam/releases/tag/2.0.8) is available.

---

Installation instructions (unchanged):

1. From binaries: run

    ```
    bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.0.8"
    ```

    or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.0.8) to your PATH. In this case, don't forget to run `opam init --reinit -ni` to enable sandboxing if you had version 2.0.0~rc manually installed or to update you sandbox script.

2. From source, using opam:

    ```
    opam update; opam install opam-devel
    ```

   (then copy the opam binary to your PATH as explained, and don't forget to run `opam init --reinit -ni` to enable sandboxing if you had version 2.0.0~rc manually installed or to update you sandbox script)

3. From source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.0.8#compiling-this-repo).

We hope you enjoy this new minor version, and remain open to [bug reports](https://github.com/ocaml/opam/issues) and [suggestions](https://github.com/ocaml/opam/issues).

> NOTE: this article is cross-posted on [opam.ocaml.org](https://opam.ocaml.org/blog/) and [ocamlpro.com](http://www.ocamlpro.com/category/blog/), and published in [discuss.ocaml.org](https://discuss.ocaml.org/t/ann-opam-2-0-8-release/7242). Please head to the latter for the comments!
