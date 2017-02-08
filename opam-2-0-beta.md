title: "opam 2.0 BETA is out!"
authors: "Louis Gesbert" {"mailto:louis.gesbert(à)ocamlpro.com"}
date: "2017-02-08"
--BODY--

We are pleased to announce that the beta release of opam 2.0 is now live! You
can try it already, bootstrapping from a working 1.2 opam installation, with:

```
opam update; opam install opam-devel
```

With about a thousand patches since the last stable release, we took the time to
gather feedback after [our last announcement](opam-2-0-preview) and implemented
a couple additional, most-wanted features:

- An `opam build` command, that, from the root of a source tree containing one
  or more package definitions, can automatically handle initialisation and
  building of the sources in a local switch.
- Support for
  [repository signing](https://github.com/hannesm/conex-paper/raw/master/paper.pdf)
  through the external [Conex](https://github.com/hannesm/conex) tool, being
  developed in parallel.

There are many more, like the new `opam clean` and `opam admin` commands, a new
archive caching system, etc., but we'll let you check the full
[changelog](https://github.com/ocaml/opam/blob/2.0.0-beta/CHANGES).

We also improved still on the
[already announced features](opam-2-0-preview/#Afewhighlights), including
compilers as packages, local switches, per-switch repository configuration,
package file tracking, etc.

The updated documentation is at http://opam.ocaml.org/doc/2.0/. If you are
involved in opam-related tools, you may also want to browse the
[new APIs](https://opam.ocaml.org/doc/2.0/api/index.html).


## Try it out

Please try out the beta, and report any issues or missing features. You can:

- Install it from source through opam, as shown above (`opam install opam-devel`)
- Use the [pre-build binaries](https://github.com/ocaml/opam/releases/tag/2.0.0-beta).
- Building from the inclusive source tarball:
  [download here](https://github.com/ocaml/opam/releases/download/2.0.0-beta/opam-full-2.0.0-beta.tar.gz)
  and build using `./configure && make lib-ext && make` if you have OCaml >=
  4.01 already available, `make cold` otherwise
- Or directly from the
  [git tree](https://github.com/ocaml/opam/tree/2.0.0-beta), following the
  included instructions from the README. Some files have been moved around, so
  if your build fails after you updated an existing git clone, try to clean it
  up (`git clean -fdx`).

A bunch of users have been using the alpha for the past months without problems,
but you may want to keep your opam 1.2 installation intact until the release is
out. In that case, it's as simple as an alias:

```
alias opam2="OPAMROOT=~/.opam2 path/to/opam-2-binary"
```


## Changes to be aware of

### Command-line interface

- `opam switch create` is now needed to create new switches, and `opam switch`
  is overall much more expressive
- `opam list` is much more expressive but may not be fully backwards-compatible
- new commands:
    - `opam build`: setup and build the local source tree
    - `opam clean`: various cleanup operations
    - `opam admin`: manage software repositories (replaces the `opam-admin` tool)
    - `opam env`, `opam exec`, `opam var`: shortcuts for the `opam config` subcommands
- `opam repository add` will now setup the new repository for the current switch
  only, unless you precise `--all`
- Some flags, like `--test`, now apply to the packages listed on the
  command-line only. For example, `opam install lwt --test` will build and
  install lwt and all its dependencies, but only build/run the tests of the
  `lwt` package. Test-dependencies of its dependencies are also ignored
- The new `opam install --soft-request` is useful for batch runs, it will maximise the
  installed packages among the requested ones, but won't fail if all can't be
  installed

As before, opam is self-documenting, so be sure to check `opam COMMAND --help`
first when in doubt. The bash completion scripts have also been thoroughly
improved, and may help get around the new options.


### Metadata

There are both a few changes (mostly, extensions) to the package description
format, and more drastic changes to the repository format, mainly related to
translating the old compiler definitions into packages.

- opam will automatically update, internally, definitions of pinned packages as
  well as repositories in the 1.2 format
- it is, however, preferred (and faster) to use repositories in the 2.0 format
  directly. To that end, please use the `opam admin upgrade` command on your
  repositories. The `--mirror` option will create a 2.0 mirror and put in place
  proper redirections, without affecting your original repository

The official opam repository at https://opam.ocaml.org remains in 1.2 format for
now, but has a live-updated 2.0 mirror to which you should be automatically
redirected. However, this means that you shouldn't contribute package
definitions in 2.0 format just yet.

#### Package format

- Any `available:` constraints based on the OCaml compiler version should be
  rewritten into dependencies to the `ocaml` package
- Separating `build:` and `install:` instructions is now required
- It is now preferred to include the archive URL and the description of the
  package into the package definition (`opam`) file: use the
  [`synopsis:`](http://opam.ocaml.org/doc/2.0/Manual.html#opamfield-synopsis)
  and
  [`description:`](http://opam.ocaml.org/doc/2.0/Manual.html#opamfield-description)
  fields, as well as the
  [url {}](http://opam.ocaml.org/doc/2.0/Manual.html#opamsection-url) file
  section. This allows one-file package definitions
- Building tests and documentation should now be part of the main `build:`
  instructions, using the `{test}` and `{doc}` filters
- It is now possible to use opam variables within dependencies, for example
  `depends: [ "foo" {= version} ]`, for a dependency to package `foo` at the
  same version as the package being defined, or `depends:
  [ "bar" {os = "linux"} ]` for a dependency that only applies on Linux.
- A new `conflict-class:` field allows to easily declare mutual conflicts among
  a set of packages. Useful for example when there are many concurrent,
  incompatible implementations.
- Field `ocaml-version:` has been deprecated for a long time and isn't accepted
  anymore. This should now be a dependency towards the `ocaml` package
- Three types of checksums are now accepted: you should use `md5=<hex-value>`,
  `sha256=<hex-value>` or `sha512=<hex-value>`. We'll be progressively
  deprecating md5 in favour of the more secure ones ; multiple checksums are
  allowed
- Supplied patch fields are now required to apply with `patch -p1`
- A new `setenv:` field allows packages to export updates to environment
  variables;
- Custom fields `x-foo:` can be used for extensions and external tools
- Allowed `"""` delimiters around unescaped strings
- `&` is now higher priority than `|` in formulas, as is customary
- As installed files are now automatically tracked, the `remove:` field is not
  required, and should not be present in general

The full, up-to-date specification of the format can already be browsed in the
[manual](http://opam.ocaml.org/doc/2.0/Manual.html#opam).

#### Repository format

On the official, default repository, and when migrating repositories from older
format versions, there is:

- A virtual `ocaml` package, that depends on any implementation of the OCaml
  compiler. This is what packages should depend on, and the version is the
  corresponding base OCaml version (e.g. `4.04.0` for the `4.04.0+fp` compiler).
  It also defines a bunch of configuration variables, see `opam config list
  ocaml`.
- Three mutually-exclusive packages providing an actual implementation of the
  OCaml toolchain:
    - `ocaml-base-compiler` is the official releases from Inria
    - `ocaml-variants.<base-version>+<variant-name>` gathers all the other
      variants
    - `ocaml-system-compiler` relies on a compiler installed on the system
      outside of opam

The layout is otherwise the same, with these exceptions:
- The `compilers/` directory is ignored
- A `repo` file should be present, containing at least the line `opam-version: "2.0"`
- The indexes for serving over HTTP have been simplified, and `urls.txt` is no
  longer needed. See `opam admin index --help`
- The `archives/` directory is no longer used, the cache now uses a different
  format and is configured through the `repo` file, defaulting to a `cache/`
  directory on the same server. See `opam admin cache --help`

## Feedback

Thanks for trying out the beta! Please post your feedback, to the
[opam tracker](https://github.com/ocaml/opam/issues); other options include the
[opam-devel](mailto:opam-devel@lists.ocaml.org) list and #opam IRC channel on
Freenode.
