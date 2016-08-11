title: "Opam 2.0~beta is out"
authors: "Louis Gesbert" {"mailto:louis.gesbert(à)ocamlpro.com"}
date: "2016-08-11"
--BODY--

With over 600 patches since 1.2.2, the release of opam 2.0 is drawing near, and
a first beta has just been released.

This version brings many new features and changes, the most notable one being
the disparition of the `.comp` compiler description files -- replaced by
standard package definition files, using their new extensions. This in turn
means more flexibility in how switches are setup and managed.

## Repository changes

While users of opam 1.2 should feel at home with the changes, the 2.0 repository
and package formats are _not_ compatible. Indeed, the move of the compilers to
standard packages implies some conversions, and updates to the relationships
between packages and their compiler. For example, package constraints like

    available: [ ocaml-version >= "4.02" ]

are now written as normal package dependencies:

    depends: [ "ocaml" {>= "4.02"} ]

To make the transition easier,
- upgrade of a custom repository is simply a matter of running `opam-admin
  upgrade-format` at its root;
- the official repository at `opam.ocaml.org` already has a 2.0 mirror, to which
  you will be automatically redirected.

Note that the `ocaml` package is actually a wrapper, that depends on one of
`ocaml-base-compiler`, `ocaml-system` or `ocaml-variants`, that contain the
different flavours of the actual compiler.

## Interface changes

Commands `opam switch` and `opam list` have been rehauled for more consistency
and flexibility, and compatibility is not guaranteed. The former won't
implicitely create new switches unless called with the `create` subcommand, and
`opam list` now allows to combine filters and finely specify the output format.

Most other commands have also seen fixes or improvements. For example, opam
doesn't forget about your set of installed packages on the first error, and the
new `opam install --restore` can be used to reinstall your selection after
_e.g._ a failed upgrade.

## Package format changes

The opam package definition format is much similar to before, but there are
quite a few extensions and some changes:

- it is now mandatory to separate the `build:` and `install:`steps (this allows
  tracking of installed files, better error recovery, and some optional security
  features);
- the url and description can now optionally be included in the `opam` file
  using the section `url {}` and fields `synopsis:` and `description:`;
- it is now possible to make dependencies depend on globally-defined opam
  variables (_e.g._ for a dependency needed on some OS only);
- the new `setenv:` field allows packages to export updates to environment
  variables;
- custom fields `x-foo:` can be used for extensions and external tools;
- allow `"""` delimiters around unescaped strings
- `&` is now parsed with higher priority than `|`
- field `ocaml-version:` can no longer be used
- the `remove:` field should not be used anymore for simple cases

## Other new features

This is just a sample, see the full
[changelog](https://github.com/ocaml/opam/blob/2.0-beta/CHANGES) for more:

- opam now stores its metadata in a single subdirectory `.opam-switch` at the
  switch prefix;

- better error mitigation in general; in particular, through more clever
  ordering of the actions and separation of `build` and `install`, most build
  failures are now without consequence on the installation;

- package command wrappers can be configured to, for example, restrict
  permissions of the build and install processes using Linux namespaces;

- a configuration file can be used to direct choices at `opam init` (specific
  repositories, wrappers, variables, fetch commands, solver...). This can
  completely override all of opam OCaml-bound settings;

- opam now tracks files installed by packages, and is able to cleanly remove
  them when no existing files where modified, in which case the `remove:` field
  isn't needed and should be skipped;

- the API is completely rewritten and should make it much easier to write
  external tools and plugins. Existing tools will, however, need to be ported.

## Roll out

You are very welcome to try out the Beta, and report any issues. The repository
at `opam.ocaml.org` will remain in 1.2 format (with the 2.0 mirror at
`opam.ocaml.org/2.0~dev` in sync) until after the release is out, which means
the extensions can not be used there yet, but you are welcome to test on local
or custom repositories, or package pinnings. The reverse translation (2.0 to
1.2) is planned, to keep supporting 1.2 installations after that date.

Inline documentation should be up-to-date, but be aware that
http://opam.ocaml.org/doc/2.0/ is still lagging behind -- in case of doubt,
don't hesitate to ask questions directly during the beta period.

## Let's go then -- how to try it ?

First, be aware that you'll be prompted to update your `~/.opam` to 2.0 format
before anything else, so if you value it, make a backup.

Install as usual:
- ...
