title: "A Travel Guide to Build Systems"
+authors: "Thomas Gazagnaire" {"http://gazagnaire.org"}
+date: "2014-08-22"
+--BODY--

One the main source of concerns for both new and experimented users of
OCaml are related to the build system: there is many tools that one
has to master to start a fresh project, all working at different
levels with a non-empty overlap and with under-specified interactions.
In this post, I will try to shed light on that mysterious and dark corner of
the OCaml ecosystem and propose a strategy to improve the currently
confusing state of affairs.

## Language features: Staying Safe is not an easy job

The first level of complexity in building OCaml projects lies OCaml's
strengths: OCaml is compiled, safe and strongly typed
language. Because of that, the OCaml compiler has to keep track very
precisely of the dependencies between the logical components of a
project.  For instance, implementations (the `.ml` files) should
always be compiled against up-to-date typed interfaces (the `.mli`
files) and implementations that are linked together to form an
executable binary needs to all be compiled against the same typed
interfaces. This is quite specific to OCaml as other mainstrean
languages are either:

- Interpreted (i.e. not compiled): for instance, in Python, Javascript
and PHP you usually build you application with a fixed set of
dependencies and you hope that nothing will break at runtime when you
update the dependencies, or when one of your user deploy the
application on his machine (where the dependencies can be different);

- Or not statically type checked. For instance In C, nothing prevent you
from linking together object files built with different interfaces. In that
case you are likely to have a segfault at runtime (FIXME: is that true?)

In practice, this level of safety is achieved by the OCaml compiler by
recording the md5 hashes of typed interfaces and implementations at compile time,
and check them at link time. These checksums can be seen using `ocamlobjinfo`:

```
$ ocamlobjinfo `ocamlc -where`/list.cmi
File /usr/local/lib/ocaml/list.cmi
Unit name: List
Interfaces imported:
	   8988208489274193e4e3f69dc6ec2f75	List
	   5de66fdff01f2735974be770893841e1	Pervasives
	   a88f91d0f04fd66bc0bbaaf347081e95	CamlinternalFormatBasics
```

## Base Tooling: the (clay) Foundations

The last section highlighted the intrinsic complexity of tracking
precise dependencies. Now, the hard part: the tooling
complexity. First of all, the are actually two main OCaml compilers:
the byte-code compiler (`ocamlc`) and the native code compiler
(`ocamlopt`).

FIXME: Not really. You explained what the compiler does to ensure
safety. I think one should not assume that the reader will immediately
understand where the problem is. Presenting explicitly a few cases
where things are difficult — ideally, suited so that you come back
later to them to show how your tool solves the difficulty. Or maybe it
is too early but then the wording should be changed.

### Two compilers

The first source of confusion is that they do have overlapping
dependency graphs: byte-code module implementations (`.cmo`) only
depends on compiled module interfaces (`.cmi`) and native module
implementations (`.cmx`) also depends on compiled module interfaces
(`.cmi`) but also on the presence, at compile time, on native compiled
module implementation (`.cmx`) for enabling cross-modules
optimizations: the generation of compiled module interface can be done
by both the bytecode and native compilers and is often a source of
errors on parallel build as it can lead to the generation of an empty
`.cmi` in some case.

They share 95% of the command-line arguments, but they
have few differences that make them not easily interchangeable. The
main one being the extension of input and output files: `cmi` and
`cmo` for bytecode, `cmi` and `cmx` for native code. Then some
command-line arguments work for one compiler and not the other, for
instance, the `-for-pack` argument might be omitted in byte-code but
is mandatory in native code, the `-vmthread` only works in bytecode, etc.

FIXME: some options are passed on the command-line, other on the
environment, other are side-effect.

FIXME: introduce "compiler" libraries

### Pre-processors

Then, they is the pre-processors story. The two compilers accept a `-pp`
and `-ppx` options which are used to run pre-processors such as
`camlp5`, `camlp4[o|r][f]` (which is, confusingly enough, a newer
version of `camlp5` which works for different variation of the OCaml
syntax) and `ppx` re-writers. The compilers have a built-in support
for these tools because they want to report potential errors
relatively to the original source file and not of the pre-processed
one. There is a "hack" which let the pre-processors generate marshaled
AST with precise source locations and let the compilers read this files:
To do so, the compilers accepts `-intf ASTFILE` and `-impl
ASTFILE` arguments, where `FILE` is a dump of an implementation or
signature AST with the correct source location and `camlp4` dumps such
AST if no `-o` options is given and its standard output is redirected
to a file (!!):

```
$ camlp4o foo.ml        # print the pre-processed text
$ camlp4o foo.ml -o foo # output the pre-processed text in foo
$ camlp4o foo.ml > foo  # dump the pre-processed *AST* in foo
```

Using that trick is possible to pre-process every source files only
once, but this is fragile and not very flexible: for instance if your
pre-processor is a no-op, you cannot dump the AST without writing your
[own tool][dumpast] as there is no option in the compiler to do it.

FIXME: The composition model of pre-processing tools is inherently
broken, the result of your file depends on the order of -pp
arguments. If you are willing to accept that then why don't you just
program in cpp and forget about module systems altogether. Bottom
line: dump all your pre-processing tools, they just fuck up and slow
down your build system.

[dumpast]: https://github.com/samoht/ocaml-dumpast

It is also difficult to compose pre-processing phases, as the `-pp`
and `-ppx` takes the filenames as implicit last arguments, without the
`-impl` and `-intf` flags.

### OCamldep

Lastly, as we already saw in the previous section, computing precise
dependency is a major requirement of ensuring language safety
properties. However, that computation usually means that you need an
already existing typing environment (as you want to resolve module
names into some kind of unique entities) that you can get from
compiled module interfaces. But when you start compiling your project,
these module interfaces do not yet exist, so you need a tool to
over-approximate the dependency: this tool is called `ocamldep`.

OCamldep parses valid OCaml files (so they either need to be
pre-processed, or the `-pp` or `-ppx` options need to be provided) and
provides two features:

- It can compute an over-approximation of the compiled module
  interfaces used by a module. The output is either a list of modules,
  or a a set of Makefile rules, which assume that the source files and
  the build artifacts lie in the same directory.

- It sort module implementation in a valid order to be linked. It can
  only sort source files, not compiled objects.

FIXME: I think OCamldep is aging and needs to be rewritten from
       scratch (possibly using some kind of magic already done by Jenga)

FIXME: mention the new -alias option

## The Higher-level Tooling: the (Pisa) Towers

As we saw in the last section, the compiler tools are already quite
complex. To deal with that, the community started to develop
higher-level tools to hide the complexity and make it more manageable.

### OCamlfind

First of all, the most ubiquitous tool used by OCamlers is
`ocamlfind`. It organizes OCaml libraries into a hierarchical package
name-space, using `META` files describing various options to pass to
the compilers and linkers. FIXME: examples (`cohttp` and
`cohttp.lwt`). It comes with two flavors: a library, `findlib`, that
is rarely used directly in practice and a binary `ocamlfind` which is
a command-line wrapper to the library and which is used pervasively by
*all* the build systems available in OCaml (more on this later).

OCamlfind does a pretty good job at hiding the compiler magic behind a
simple interface. But is also has some (fixable) quirks:

FIXME: duplicate of include outputs (not very critical)
FIXME: limited query language (need `-X`)

FIXME: Also introduce some complexity: META files can define arbitrary
predicates, but very few are introduced in practice the query language
is not well-defined (example with `threads` [threads][])

```
$ ocamlfind query -r -predicates byte -format "%d/%a" -r threads.vm
/usr/local/lib/ocaml/unix.cma
$ ocamlfind query -r -predicates byte -format "%d/%a" -r threads.posix
/usr/local/lib/ocaml/unix.cma
$ ocamlfind query -r -predicates byte,mt,mt_vm -format "%d/%a" -r threads.vm
/usr/local/lib/ocaml/vmthreads/unix.cma
/usr/local/lib/ocaml/vmthreads/threads.cma
$ ocamlfind query -r -predicates byte,mt,mt_posix -format "%d/%a" -r threads.posix
/usr/local/lib/ocaml/unix.cma
/usr/local/lib/ocaml/threads/threads.cma
```

[threads]: https://github.com/samoht/assemblage/issues/88

### ocamlbuild

FIXME: ocamlbuild is a build system which comes with the compiler, which
should make it the default one used by the community. Use a lot of files
scatered around (myocamlbuild.ml, _tags, .mllib, .mlpack, .odoc, ...) ->
hard to understand a project structure, support for automatic dependency
discovery (which make it very flexible but also rather slow), build
artefacts not build in isolation as it mirror the source hierarchy
(TODO: ex of scary bugs with packs or private/public interface not respected)

FIXME: works very well on simple project, but very hard to compose in
a predictable way.

### oasis

FIXME: hide again more things, use ocamlbuild. Introduce more
complexity options to tweak all intermediate layers:
ocamlbuild, ocamlfind, the compilers so need an expertise in all of
these to debug.

FIXME: Note that ocamlbuid could be replaced in theory but no other
plugin has been developed so far.

FIXME: To be fair, I think positive things should also be said about
oasis. The fact that the whole structure of the project is apparent in
a single file and that it is done in a declarative way stand out.

### omake, OMakefile, ocp-build, obuild, jenga

FIXME: omake not supported anymore, slow filesystem scanning on
startup, weird input language

FIXME: OMakefile, very nice alternative but but not very flexible
FIXME: You need to substantiate your claim.

FIXME: ocp-build (ocamlpro) powerful but not documented and released, input
language not very satisfactory

FIXME: obuild (former Citrix) seems to work quite well, simple input
language, problems with computation of dependencies last time I
checked (i.e. need to reconfigure). Need to look at it again.
FIXME: AFAIK, it is neither ready for large projects.

FIXME: jenga (Jane Street) v. powerful, monadic based (vs. applicative
http://neilmitchell.blogspot.co.uk/2014/07/applicative-vs-monadic-build-systems.html)
cannot easily interact with globally installed packages.

### OPAM

FIXME: This is not a build system, but it has a notion of packages and
dependencies. Something clever to say here?

FIXME: Not clever, but personally in the three headed structure opam,
ocamlfind and the compiler there's one head too much. I would really
like to see the notion of package/subpackage migrate to the compilers
(which could be the occasion to slightly overhaul their
interface). One benefit in doing so would be that it could actually
solve the problem that we still have, despite module aliases, that we
are unable to deal with compilation units with the same toplevel in
different packages, even if their interface is not exposed, which
feels a little bit retarded (having the notion of package as libs +
interface, the name of the hidden modules could be mangled by the
compiler). That's just a non well-formed idea though.

## Where we should go

FIXME: So far, one has an idea of the various tools but not of why
their limitation is a problem. IMHO, things should maybe be presented
with a bit more of structure: there are the basic tools (the
compilers, talking also about pre-processors) that everybody must use,
then findlib for managing chains of dependencies among OCaml modules
(examples showing its interest easy to give), and ocamldep to discover
these dependencies within your project. Then there is a tour of
existing build systems to put it all together. In presenting the build
systems, I think one should give examples of their strengths but also
of their weaknesses so that, at the end of that section, the reader is
left with the feeling that it would be great if... that you use as a
stepping stone to present your solution.

FIXME:

FIXME: The solution we propose: compiler-as-a-service, Library everywhere.
http://fsharp.github.io/FSharp.Compiler.Service/

- make compiler-libs pure (and re-entrant), have a clean external
  interface with abstract type everywhere
  (and not serialize/deserialize to the command-line and loose types)

- make the findlib library by removing all side-effects, or design a
  new library which doesn't depend on a untyped key-value predicate
  store.

FIXME: Here again in some sense findlib is just something that maps
(package) names to paths/files, I don't really see why this couldn't
be handled by the compiler itself and directly integrated on their
command line.

- promote a declarative / compositional approach at every level. Get
  ride of all the duplication of concerns.

- treat the shell command-line as a specific implementation detail. If
  we really want high-performance build, we could do everything in
  memory and version control all the build artifacts using something
  like [Irmin][].

FIXME: call to contribution

### Assemblage

We started at the highest level by a library to describe OCaml
projects. Currently Makefile backend to ease the debugging dev. but
could move to a pure OCaml solution (using Jenga or other) later.

FIXME: Point to the API one must implement — assuming there is one —
to target a new build system.

FIXME: current status, what is supported what is not, what part of the API
is likely to change in a near future

FIXME: Examples

FIXME: To repeat myself: I think some simple example are good but one
should also show how assemblage solves the difficulties presented
above.