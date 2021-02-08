title: "opam 2.0.8 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Louis Gesbert - OCamlPro" {"mailto:louis.gesbert(à)ocamlpro.com"}
]
date: "2021-02-08"
--BODY--

We are pleased to announce the minor release of [opam 2.0.8](https://github.com/ocaml/opam/releases/tag/2.0.8).

This new version contains some [backported](https://github.com/ocaml/opam/pull/4425) fixes:
 * Add colon for fish MANPATH fix. [[#4084](https://github.com/ocaml/opam/pull/4084) [@rjbou](https://github.com/rjbou) - fix [#4078](https://github.com/ocaml/opam/issues/4078)]
 * No error when linked directory doesn't exist (e.g. XDG defined) [[#4278](https://github.com/ocaml/opam/pull/4278) [@kit-ty-kate](https://github.com/kit-ty-kate)]
 * Add quotes to avoid space unwanted behaviors [[#4278](https://github.com/ocaml/opam/pull/4278) [@kit-ty-kate](https://github.com/kit-ty-kate)]
 * Handle `CCACHE_DIR` environment variable in sandbox script.  [[#4087](https://github.com/ocaml/opam/pull/4087) [@rjbou](https://github.com/rjbou) - fix [#4079](https://github.com/ocaml/opam/issues/4079)]
 * Follow links of `~/.cache` & `~/.cache/dune` for bwrap call.  [[#4087](https://github.com/ocaml/opam/pull/4087) [@rjbou](https://github.com/rjbou) - fix [#4068](https://github.com/ocaml/opam/issues/4068)]
 * Don't overwrite user's sandbow script modification. [#4020 [#4092](https://github.com/ocaml/opam/pull/4092) [@rjbou](https://github.com/rjbou)]
 * On MacOS sandbox script, always read write mount `/tmp` [#3742 @rjbou - fix ocaml/opam-repository#13339]
 * The stdout of `pre-` and `post-session` hooks is now propagated to the user [[#4382](https://github.com/ocaml/opam/pull/4382) [@AltGr](https://github.com/AltGr) - fix [#4359](https://github.com/ocaml/opam/issues/4359)]
 * Run switch pre/post sessions hooks [[#4476](https://github.com/ocaml/opam/pull/4476) [@rjbou](https://github.com/rjbou) - fix [#4472](https://github.com/ocaml/opam/issues/4472)]
 * Use version var in opam file instead of equal current version number in opamlib dependencies [[#4178](https://github.com/ocaml/opam/pull/4178) [@rjbou](https://github.com/rjbou)]
 * Opam file build using dune [[#4178](https://github.com/ocaml/opam/pull/4178) [@rjbou #4229 @kit-ty-kate](https://github.com/rjbou #4229 @kit-ty-kate) - fix [#4173](https://github.com/ocaml/opam/issues/4173)]
 * Update opam file to 2.0 [[#4371](https://github.com/ocaml/opam/pull/4371) [@AltGr](https://github.com/AltGr)]
 * Fix `arch` detection when using 32bit mode on ARM64 [[#4462](https://github.com/ocaml/opam/pull/4462) [@kit-ty-kate](https://github.com/kit-ty-kate)]
 * Fix `arch` detection of i486 [[#4462](https://github.com/ocaml/opam/pull/4462) [@kit-ty-kate](https://github.com/kit-ty-kate)]

> Note: To homogenise macOS name on system detection, we decided to keep `macos`, and convert `darwin` to `macos` in opam. For the moment, in order to avoid breaking jobs & CIs, we keep uploading `darwin` & `macos` binaries, but from the 2.1.0 release, only `macos` ones will be kept.

---

Installation instructions (unchanged):

1. From binaries: run

    ```
    sh <(curl -sL https://raw.githubusercontent.com/ocaml/opam/master/shell/install.sh)
    ```

    or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.0.8) to your PATH. In this case, don't forget to run `opam init --reinit -ni` to enable sandboxing if you had version 2.0.0~rc manually installed or to update you sandbox script.

2. From source, using opam:

    ```
    opam update; opam install opam-devel
    ```

   (then copy the opam binary to your PATH as explained, and don't forget to run `opam init --reinit -ni` to enable sandboxing if you had version 2.0.0~rc manually installed or to update you sandbox script)

3. From source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.0.8#compiling-this-repo).

We hope you enjoy this new minor version, and remain open to [bug reports](https://github.com/ocaml/opam/issues) and [suggestions](https://github.com/ocaml/opam/issues).

> NOTE: this article is cross-posted on [opam.ocaml.org](https://opam.ocaml.org/blog/) and [ocamlpro.com](http://www.ocamlpro.com/category/blog/). Please head to the latter for the comments!

