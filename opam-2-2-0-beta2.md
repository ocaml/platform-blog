title: "opam 2.2.0 beta2 release"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)outlook.com"}
  "David Allsopp - Tarides" {"mailto:david(à)tarides.com"}
]
date: "2024-04-09"
--BODY--

_Feedback on this post is welcomed on [Discuss](https://discuss.ocaml.org/t/ann-opam-2-2-0-beta2/14461)!_

We are indescribably thrilled to announce the second beta release of opam 2.2.0.

It contains everything required to be able to make opam-repository compatible
with Windows, as well as a whole bunch of fixes. You can view the full list
of changes in the
[release note](https://github.com/ocaml/opam/releases/tag/2.2.0-beta2).

**We'll post another blog post very soon with more directions on how to test
opam on Windows with this release.**

This version is a beta, we invite users to test it to spot previously
unnoticed bugs as we head towards the stable release.

## Changes

### Windows support

This beta introduces a handful of changes necessary to be able to
make the default opam-repository support Windows out of the box:

* Add a new `sys-ocaml-system` init default global eval variable
* Hijack the `"%{var?string-if-true:string-if-false-or-undefined}%"` syntax to
  support extending the variables of packages with + in their name
  (`conf-c++` and `conf-g++` already exist) using `"%{?pgkname:var:}%"`
* Add `winsymlinks:native` to the `CYGWIN` environment variable when installing
  a package on Windows. In particular, this provides a workaround when extracting
  ocamlbuild's sources.
* Internal Cygwin installation's bin directory is placed as far down `PATH` as
  is necessary not to shadow `bash`, `tar`, `sort` or `git`
* Disable ACL in Cygwin internal install to avoid permission mismatch errors

We expect to be able to show the proposed changed to opam-repository
very soon to take advantage of all these changes.

### opam-repository scalability

The current draft resolution resulting from the discussion in
[ocaml/opam-repository#23789](https://github.com/ocaml/opam-repository/issues/23789)
about the scalability of opam-repository includes the removal of some
packages. However currently, opam uses the `patch` system command to apply
changes from a repository. The behaviour of that command is thus very
important and it is a known behaviour for the macOS and BSDs `patch`
command to not be able to delete files which leads to failures and
inconsistencies for opam. Package managers on those platforms installing
opam already make opam depend on GNU patch, however a certain number of
people do not install opam via a system package manager (e.g. our own install script!)
and end up using their system version of `patch`. This is in particular
a problem on macOS as the name of the GNU patch command is not `gpatch` like
on BSDs but simply `patch` when installed via Homebrew.

This issue is surprisingly tricky to fix, and after many trials and errors,
we've decided to:

* Warn if GNU patch is not detected when a patch is applied
* Use `gpatch` by default instead of `patch` on NetBSD and DragonFlyBSD
* Use `gpatch` if it exists and is detected as GNU patch when `patch` is not
  GNU patch

These changes will make their way to the upcoming opam 2.1.6, in a few weeks.

### Other noteworthy changes

* Recommend enabling Developer Mode on Windows.
  This allows the creation of symlinks without requiring elevation.
  Longer-term, the aim is that we should never _require_ Developer Mode,
  but at the moment more things work with it than without it!
* Mark the internal Cygwin installation as recommended.
  Please don't try to maintain your own Cygwin install
  for use with opam unless you really know what you're doing!
* Fix MSYS2 support. For 2.2.0, the focus has been on Cygwin,
  so configuring opam to use MSYS2 is quite manual.
  Please note that even if opam can use a MSYS2 installation,
  it is not yet officially supported and opam repository is not 
  yet MSYS2 compatible. Use opam with MSYS2 only if you 
  _really really_ know what you're doing!
* Fix issues when using fish
* Improve the internal Cygwin installation during init on Windows
* Unixify Windows paths in init shells scripts
* Disable Software Heritage fallback by default as there currently no CI job
  in opam-repository to check validity of proposed `swhid` regarding release
  archive (neither publication tools support) and some concerns were raised
  regarding the degree of trust of the hashing method used by Software
  Heritage (sha1).
* Make sure `opam source --dev` with git sources, clones the whole repository
  instead of using `--depth=1`
* Sandbox: Mark the user temporary directory
  (as returned by `getconf DARWIN_USER_TEMP_DIR`) as writable when TMPDIR
  is not defined on macOS
* Add Warning 69: Warn for new syntax when package name in variable in string
  interpolation contains several '+' (this is related to the "hijack" item above)
* Add support for Wolfi OS, treat it like Alpine family as it also uses apk
* Upgrade the vendored dune package to 3.14.2 to allow to compile opam when
  the environment contains unicode characters on Windows (in particular,
  this means opam now works if your username contains accented characters)
* Upgrade other vendored packages
  (cmdliner 1.2.0, re 1.11.0, ocamlgraph 2.1.0, opam-file-format 2.1.6)

Various other general and performance improvements were made and bugs were fixed.
API changes are denoted in the
[release note](https://github.com/ocaml/opam/releases/tag/2.2.0-beta2).
This release also includes PRs improving the documentation and improving
and extending the tests.

## Windows Support

As we've said above we're writing a separate blog post to present how to test
this new release of opam on Windows.

Stay tuned!

## Try it!

In case you plan a possible rollback, you may want to first backup your
`~/.opam` directory.

The upgrade instructions are unchanged:

1. Either from binaries: run

    ```
    bash -c "sh <(curl -fsSL https://opam.ocaml.org/install.sh) --version 2.2.0~beta2"
    ```

    or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.2.0-beta2) to your PATH.

2. Or from source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.2.0-beta2#compiling-this-repo).


You should then run:
```
opam init --reinit -ni
```


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Thanks for trying this new release out, and we hope you will enjoy the new features!
