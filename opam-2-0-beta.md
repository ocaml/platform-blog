title: "OPAM 2.0 alpha is out"
authors: "Louis Gesbert" {"mailto:louis.gesbert(à)ocamlpro.com"}
date: "2016-08-16"
--BODY--

We are pleased to announce the new OPAM 2.0 development release, with over 600
patches since [1.2.2](https://opam.ocaml.org/blog/opam-1-2-2-release/). Version
[2.0~alpha4](https://github.com/ocaml/opam/releases/2.0-alpha4) has just been
released, and is ready to be more widely tested.

This version brings many new features and changes, the most notable one being
that OCaml compiler packages are no longer special entities, and are replaced
by standard package definition files. This in turn means that OPAM users have
more flexibility in how switches are managed, including for managing non-OCaml
environments such as [Coq](http://coq.io/opam/) using the same familiar tools.

## A few highlights

This is just a sample, see the full
[changelog](https://github.com/ocaml/opam/blob/2.0-alpha4/CHANGES) for more:

- **Automatic file tracking:**: OPAM now tracks the files installed by packages
  and is able to cleanly remove them when no existing files were modified.
  The `remove:` field is now optional as a result.

- **Sandboxed builds:** Command wrappers can be configured to, for example,
  restrict permissions of the build and install processes using Linux namespaces.

- **Better error mitigation:** Through clever ordering of the shell actions and
  separation of `build` and `install`, most build failures do not affect the
  current installation.

- **Configuration file:** This can be used to direct choices at `opam init`
  automatically (e.g. specific repositories, wrappers, variables, fetch
  commands, or the external solver). This can be used to override all of OPAM's
  OCaml-related settings.

- **Simpler library:** the OCaml API is completely rewritten and should make it
  much easier to write external tools and plugins. Existing tools will need to be
  ported.

- **Compilers as packages:** This brings many advantages for OPAM workflows,
  such as being able to upgrade the compiler in a given switch, better tooling for
  local compilers, and the possibility to define `coq` as a compiler or even
  use OPAM as a generic shell scripting engine with dependency tracking.

- **Local switches:** Create switches within your projects for easier management.

- **Inplace build:** Use opam to build directly from your source directory.

## Roll out

You are very welcome to try out the Alpha, and report any issues. The repository
at `opam.ocaml.org` will remain in 1.2 format (with a 2.0 mirror at
`opam.ocaml.org/2.0~dev` in sync) until after the release is out, which means
the extensions can not be used there yet, but you are welcome to test on local
or custom repositories, or package pinnings. The reverse translation (2.0 to
1.2) is planned, to keep supporting 1.2 installations after that date.

Inline documentation should be up-to-date, but be aware that
http://opam.ocaml.org/doc/2.0/ has not yet caught up with the changes -- in case
of doubt, don't hesitate to ask questions directly during the alpha period.

## Interface changes

Commands `opam switch` and `opam list` have been rehauled for more consistency
and flexibility: the former won't implicitly create new switches unless called
with the `create` subcommand, and `opam list` now allows to combine filters and
finely specify the output format. They may not be fully backwards compatible, so
please check your scripts.

Most other commands have also seen fixes or improvements. For example, opam
doesn't forget about your set of installed packages on the first error, and the
new `opam install --restore` can be used to reinstall your selection after a
failed upgrade.

## Repository changes

While users of opam 1.2 should feel at home with the changes, the 2.0 repository
and package formats are not compatible. Indeed, the move of the compilers to
standard packages implies some conversions, and updates to the relationships
between packages and their compiler. For example, package constraints like

    available: [ ocaml-version >= "4.02" ]

are now written as normal package dependencies:

    depends: [ "ocaml" {>= "4.02"} ]

To make the transition easier,
- upgrade of a custom repository is simply a matter of running `opam-admin
  upgrade-format` at its root;
- the official repository at `opam.ocaml.org` already has a 2.0 mirror, to which
  you will be automatically redirected;
- packages definition are automatically converted when you pin a package.

Note that the `ocaml` package on the official repository is actually a wrapper,
that depends on one of `ocaml-base-compiler`, `ocaml-system` or
`ocaml-variants`, that contain the different flavours of the actual compiler.

## Package format changes

The opam package definition format is very similar to before, but there are
quite a few extensions and some changes:

- it is now mandatory to separate the `build:` and `install:` steps (this allows
  tracking of installed files, better error recovery, and some optional security
  features);
- the url and description can now optionally be included in the `opam` file
  using the section `url {}` and fields `synopsis:` and `description:`;
- it is now possible to have dependencies toggled by globally-defined opam
  variables (_e.g._ for a dependency needed on some OS only), or even rely on
  the package information (_e.g._ have a dependency at the same version);
- the new `setenv:` field allows packages to export updates to environment
  variables;
- custom fields `x-foo:` can be used for extensions and external tools;
- allow `"""` delimiters around unescaped strings
- `&` is now parsed with higher priority than `|`
- field `ocaml-version:` can no longer be used
- the `remove:` field should not be used anymore for simple cases (just removing
  files)

## Let's go then -- how to try it ?

First, be aware that you'll be prompted to update your `~/.opam` to 2.0 format
before anything else, so if you value it, make a backup.

Install as usual:
- Using pre-built binaries and our install script:

    wget https://raw.github.com/ocaml/opam/2.0-alpha/shell/opam_installer.sh -O - | sh -s /usr/local/bin

  Equivalently,
  [pick your version](https://github.com/ocaml/opam/releases/2.0-alpha4) and
  download it to your PATH;

- Using the development packages with your package manager, as they become
  available: see [https://opam.ocaml.org/2.0/Install.md] for up-to-date
  information;

- Building from our inclusive source tarball:
  [download here](https://github.com/ocaml/opam/releases/download/2.0-alpha/opam-full-2.0-alpha.tar.gz)
  and build using `./configure && make lib-ext && make && make install` if you
  have OCaml >= 4.01 already available, `make cold && make install` otherwise;

- From an existing opam installation, you can also use the packaged version
  `opam-devel` with _e.g._:

    opam install opam-devel.2.0~alpha && sudo cp $(opam config var opam-devel:lib)/* /usr/local/bin/opam`.
