title: "OPAM as a build system"
authors: "Louis Gesbert" {"mailto:louis.gesbert(à)ocamlpro.com"}
date: "2015-04-01"
--BODY--

It is a well-known fact that there are too many build systems for OCaml. Between
omake, OCamlMakefile, ocamlbuild, oasis, ocp-build, topkg, jenga, assemblage,
etc. it's all too difficult to choose. As always in such cases, there is a
simple solution: start a new, better one from scratch and
[replace them all](http://xkcd.com/IwastolazytosearchbutyoumustknowthecomicIamthinkingabout).
Since the OPAM brand is quite successful in package management, and has become
quite popular in the community, a build system leveraging its marketing value is
bound to succeed. And there is also interest for OPAM itself, which could
benefit from better control over packages' builds and library installs.


### How it works

OPAM 1.2.1 includes a cool library that can execute external processes in
parallel, following a dependency graph. That's already the core of a build
system: there are rules calling the compiler appropriately to build a set of
targets, and dependency between targets that form a graph (a DAG, hopefully). By
putting these through OPAM's parallel library, we in fact get an efficient build
engine, also with nice colorful feedback to the terminal. Job done.

You may point out that correctly defining the build rules, and giving a proper
way of specifying a project, are important and difficult points in build
systems. Fair enough: the analogy between packages and build targets doesn't
stop here. Indeed, OPAM already has a specification language, it's its
repository format! The user only needs to put a `packages/` directory at the
root of his project, with a fake OPAM package for each build target. This allows
to specify dependencies between targets in a very flexible way, and then a call
to e.g. `opam install --root=. main.native` will build the whole project as
many, single-package modules.

Writing this repository by hand may be a little tedious, but it's the low level
interface, and it has all the flexibility we need. To help generate it, we have
a prototype of a `src2opam` tool that will scan your project and build that for
you. It is actually able to guess what syntax extensions are used, how to build
stubs and where they are needed (using clever heuristics), so once it's released
no project specification at all should be needed anymore (in other terms, the
project being its own specification).


### Bootstrapping

We of course started by trying out this new build system to build OPAM itself.
Bootstrapping is always a good thing, and this worked beautifully. Expect us to
remove the dirty OCamlMakefiles that were needed to build from stage 0 with
maximum compatibility, we'll now ship with a bytecode version of OPAM that you
can run with `ocamlrun` on any architecture to bootstrap. Some build systems,
like `ocamlbuild`, already do it that way.


### Changes to OPAM and its repository

To make OPAM more reliable and give more control to its users, it'll be a good
thing when all packages migrate to this new build system. Once we publish the
tools and guidelines, we'll discuss a roadmap for the migration of all packages,
with strict deadlines after which all non-complying packages will be removed.
This may seem a bit harsh, but it's needed to make the build process really
included into OPAM, and the benefits are too many to list here ; packages as
small repositories included in the larger repository, all generalised by the
same architecture! Once this is done we intend to replace the OCaml compiler in
the same way, and build individual modules as tiny OPAM repositories, which
would give us a beautiful, fractal system.
