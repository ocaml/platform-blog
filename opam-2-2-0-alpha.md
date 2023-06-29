-----
title: "opam 2.2.0 alpha is ready!"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Louis Gesbert - OCamlPro" {"mailto:louis.gesbert(à)ocamlpro.com"}
]
date: "2023-06-30"
--BODY--

We are happy to announce the alpha release of opam 2.2.0. It contains numerous
fixes, enhancements, updates. You can view the full list in the [release
note](https://github.com/ocaml/opam/releases/tag/2.2.0-alpha).

This version is an alpha, so we invite users to test it to spot previously
unnoticed bugs and work towards a stable release.

## Windows Support

Opam 2.2 comes with native Windows compatibility. You can now use opam from
your preferred Windows terminal! We rely on the [Cygwin](https://cygwin.com/)
UNIX-like environment for Windows as a compatibility layer, but it is possible
for a package to generate native executables.

The main opam repository is not Windows compatible at the moment, but existing
work on a [compatible
repository](https://github.com/ocaml-opam/opam-repository-mingw) (originally
from [@fdopen](https://github.com/fdopen)) and [32/64 bit mingw-w64 packages](https://github.com/dra27/opam-repository) (by [@dra27](https://github.com/dra27)) is in the process of being merged.

### How to Test opam on Windows

This alpha requires a preexisting Cygwin installation. Support for full
management of a local Cygwin environment inside of opam (so that it's as
transparent as possible) is queued already and should be available
in 2.2.0~alpha2 as another option.

1. Check that you have all dependencies installed: Autoconf, Make, MinGW, or MSVC GCC
2. Download & extract the [opam archive](https://github.com/ocaml/opam/releases/download/2.2.0-alpha/opam-full-2.2.0-alpha.tar.gz)
3. In the directory launch `make cold [OCAML_PORT=mingw64|mingw|msvc64|msvc|auto]`
4. A coffee later, you now have an opam executable!
5. Start your preferred Windows terminal (cmd or PowerShell), and initialise opam with the Windows _sunset_ repository:
  * `opam init https://github.com/ocaml-opam/opam-repository-mingw`

From here, you can try to install sunset repository packages. If any bug is
found, please [submit an
issue](https://github.com/ocaml-opam/opam-repository-mingw#what-do-i-do-when-things-are-broken).
It will help opam repository maintainers to add Windows repository packages
into the main repository.

> Hint: if you use the MinGW compiler, don't forget to add to your `PATH` the path
to `libc` dlls.



## Recursive Pin

Recursive pinning allows opam to lookup opam files in
subdirectories in different ways:

* `--recursive` option, opam goes through subdirectories to look for opam
  files and pin them to their respective subdirectories 
* `--subpath <path>` option, opam goes to the given subdirectory to find the opam
  file and pin the corresponding package to this subdirectory 
* These options can be combined

You can use these options with `opam pin` and `opam install`.

```
$ tree .
.
├── ba
│   └── z
│       └── z.opam
├── bar
│   └── bar.opam
└── foo.opam

$ opam pin --recursive . -n
This will pin the following packages: foo, z, bar. Continue? [y/n] y
foo is now pinned to git+file:///tmp/recpin#master (version 0.1)
Package z does not exist, create as a NEW package? [y/n] y
z is now subpath-pinned to directory /ba/z in git+file:///tmp/recpin#master (version 0.1)
Package bar does not exist, create as a NEW package? [y/n] y
bar is now subpath-pinned to directory /bar in file:///tmp/recpin (version 0.1)

$ opam pin
bar.0.1  (uninstalled)  rsync  directory /bar in file:///tmp/recpin
foo.0.1  (uninstalled)  git    git+file:///tmp/recpin#master
z.0.1    (uninstalled)  git    directory /ba/z in git+file:///tmp/recpin#master
```

## Tree View

`opam tree` shows packages and their dependencies with a tree view. It is very
helpful to determine which packages bring which dependencies in your installed
switch.

```
$ opam tree cppo
cppo.1.6.9
├── base-unix.base
├── dune.3.8.2 (>= 1.10)
│   ├── base-threads.base
│   ├── base-unix.base [*]
│   └── ocaml.4.14.1 (>= 4.08)
│       ├── ocaml-base-compiler.4.14.1 (>= 4.14.1~ & < 4.14.2~)
│       └── ocaml-config.2 (>= 2)
│           └── ocaml-base-compiler.4.14.1 (>= 4.12.0~) [*]
└── ocaml.4.14.1 (>= 4.02.3) [*]
```

It can also display a reverse-dependency tree (through `opam why`, which is an
alias to `opam tree --rev-deps`). This is useful to examine how dependency
versions get constrained.

```
$ opam why cmdliner
cmdliner.1.2.0
├── (>= 1.1.0) b0.0.0.5
│   └── (= 0.0.5) odig.0.0.9
├── (>= 1.1.0) ocp-browser.1.3.4
├── (>= 1.0.0) ocp-indent.1.8.1
│   └── (>= 1.4.2) ocp-index.1.3.4
│       └── (= version) ocp-browser.1.3.4 [*]
├── (>= 1.1.0) ocp-index.1.3.4 [*]
├── (>= 1.1.0) odig.0.0.9 [*]
├── (>= 1.0.0) odoc.2.2.0
│   └── (>= 2.0.0) odig.0.0.9 [*]
├── (>= 1.1.0) opam-client.2.2.0~alpha
│   ├── (= version) opam.2.2.0~alpha
│   └── (= version) opam-devel.2.2.0~alpha
├── (>= 1.1.0) opam-devel.2.2.0~alpha [*]
├── (>= 0.9.8) opam-installer.2.2.0~alpha
└── user-setup.0.7
```

> Special thanks to [@cannorin](htpps://github.com/@cannorin) for collaborating on this feature.

## Recommended Development Tools

There is now a way for a project maintainer to share their project development
tools: the `with-dev-setup` dependency flag. It is used in the same way as
`with-doc` and `with-test`: by adding a `{with-dev-setup}` filter after a
dependency. It will be ignored when installing normally, but it's pulled in when the
package is explicitely installed with the `--with-dev-setup` flag specified on
the command line. The variable is also resolved in the `post-messages:` field
to allow maintainers to share more informations about that setup.

This is typically useful for tools that are required for bootstrapping or
regenerating artifacts.


For example
```
opam-version: "2.0"
depends: [
  "ocaml"
  "dune"
  "ocp-indent" {with-dev-setup}
]
build: [make]
install: [make "install"]
post-messages:
[ "Thanks for installing the package"
  "and its tool dependencies too, it will help for your futur PRs" {with-dev-setup} ]
```

## Software Heritage Binding

[Software Heritage](https://www.softwareheritage.org) is a project that aims to
archive all software source code in existence. This is done by first collecting
source code with a _loader_ that uploads software source code to the Software
Heritage distributed infrastructure. From there, any project/version is
available via the [search webpage](https://archive.softwareheritage.org/) and
via a unique identifier called the
[SWHID](https://docs.softwareheritage.org/devel/swh-model/persistent-identifiers.html#uri-scheme).
Some OCaml source code is [already
archived](https://www.softwareheritage.org/2021/04/20/connecting-ocaml/), and the
main opam and Coq repository packages are continuously uploaded.

Opam now integrates a fallback to Software Heritage archive retrieval, based on
SWHID. If an SWHID URL is present in an opam file, the fallback can be
activated.

To keep backwards compatibility of opam files, we added a specific
Software Heritage URL syntax to the `url.mirrors:` field, which is used to specify mirrors of the main URL. Opam 2.2.+ understands this specific syntax as a Software
Heritage fallback URL: `https://swhid.opam.ocaml.org/<SWHID>`.

```
url {
  src: "https://faili.ng/url.tar.gz"
  checksum: "sha512=e2146c1d7f53679fd22df66c9061b5ae4f8505b749513eedc67f3c304f297d92e54f5028f40fb5412d32c7d7db92592eacb183128d2b6b81d10ea716b7496eba"
  mirrors: [
    "https//failli.ng/mirror.tar.gz"
    "https://swhid.opam.ocaml.org/swh:1:dir:9f2be900491e1dabfc027848204ae01aa88fc71d"
  ]
}
```

To add a Software Heritage fallback URL to your package, use the
[`swhid`](https://github.com/OCamlPro/swhid) library. Specifically the
[`Compute.directory_identifier_deep`](https://github.com/OCamlPro/swhid/blob/master/src/compute.mli#L12)
function:

1. Download opam package archive
2. Extract the archive
3. Compute SWHID with `Compute.directory_identifier_deep`. You can use this oneliner in the directory:
```ocaml
ocaml -e '#use "topfind";; #require "digestif.ocaml";; #require "swhid";; Swhid_core.Object.pp Format.std_formatter (Result.get_ok (Swhid.Compute.directory_identifier_deep "."))'
```

> Special thanks to [@zapashcanon](https://github.com/@zapashcanon) for collaborating on this feature

## Formula (Experimental)

It is now possible to leverage the full expressivity of package dependency
formulas from the command line during switch creation and package operations.

For example, while installing a package, let's say `genet`, you can specify
that you want to install either `conf-mariadb & mariadb` or `conf-postgresql`:

```
opam install genet --formula '["mysql" ("conf-mariadb" & "mariadb" | "conf-postgresql")]'
```

Or create a switch with `ocaml-variant` or `ocaml-system`, excluding `ocaml-base-compiler`:

```
opam switch create ocaml --formula '"ocaml-variants" {>= "4.14.1"} | "ocaml-system"'
```

## New Options
Here are several of new options (possibly scripts breaking changes are marked with ✘):
  * `opam pin --current` to fix a package to its current state (disabling pending reinstallations or removals from the repository)
  * `opam pin remove --all` to remove all the pinned packages from a switch
  * Allow `opam pin remove` to take a package (`<pkg>.<version>`) as argument
  * `opam exec --no-switch` to remove opam environment from launched command
  * `opam source --no-switch`
  * `opam clean --untracked` to remove untracked files interactively
  * `opam switch -` that goes back to the previously selected global switch
  * Add `opam admin add-constraint <cst> --packages` to select a subset of packages to apply constraints
  * ✘ Change `--base` into `--invariant`. `opam switch` _compiler_ column now contains installed packages that verifier invariant formula, and empty synopsis shows switch invariant.


## Try It!

In case you plan a possible rollback, you may want to first backup your
`~/.opam` directory.

The upgrade instructions are unchanged:

1. From binaries: run
    ```
    bash -c "sh <(curl -fsSL https://raw.githubusercontent.com/ocaml/opam/master/shell/install.sh) --version 2.2.0~alpha"
    ```
Or download manually from [the Github "Releases" page](https://github.com/ocaml/opam/releases/tag/2.2.0-alpha) to your PATH.

2. From source, manually: see the instructions in the [README](https://github.com/ocaml/opam/tree/2.2.0-alpha#compiling-this-repo).

Then run:
```
opam init --reinit -ni
```


Please report any issues to [the bug-tracker](https://github.com/ocaml/opam/issues).

Thanks for trying this new release out, and we're hoping you will enjoy the new features!
