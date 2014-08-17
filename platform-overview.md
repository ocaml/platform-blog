title: "Introducing the OCaml Platform, the natural evolution of OPAM"
authors: "Amir Chaudhry" {"http://amirchaudhry.com"}
date: "2014-08-11"
--BODY--

The OCaml Platform combines the OCaml compiler toolchain with a coherent
set of tools for build, documentation, testing and IDE integration. The
project is a *collaborative* effort across the OCaml community, tied
together by the OCaml Labs group in Cambridge and with other major
contributors including OCamlPro. The requirements of the Platform are being
guided by the industrial OCaml Consortium (primarily Jane Street and
Citrix).

The OCaml Platform is the natural evolution of OPAM. Specifically, OPAM
forms the substrate on which components can be built and it naturally
facilitates a convenient development workflow. Since OPAM underpins the OCaml
Platform efforts, we will use [opam.ocaml.org][] to chart its
progress — hence
this new blog. Over the next three weeks we will have a series of posts that
cover the new features in OPAM 1.2, describe the additional components, and
highlight some of the relevant work from the community that supports these
integrative efforts.

The first item to announce is the **beta release of OPAM 1.2**.
This release provides a number of new features, which we will cover in a
separate post.
In the meantime, please do read the [release notes][opam-12]!

In the rest of this post we will give an overview of what the Platform is
(and is not), briefly cover some of the major elements and then describe an
example workflow that the Platform can enable. Later posts in this series
will go into more depth on each of the components and how they fit together,
beginning with OPAM. We welcome wider feedback so please do join the
[Platform mailing list][platform-list] to discuss or comment on these posts.


## Overall ethos

OCaml Labs has initially taken direction from major industrial users
because these groups have a great deal of experience of using the
language at scale (e.g. millions of lines of code, thousands of
directories with complicated testing and build requirements as well as
the concomitant issues in training new developers). For the Platform to
succeed, it has to be a viable product for those heavy
users of OCaml. We're of the pragmatic view that examining and solving
the problems that such organizations have will ultimately result in a
large and positive impact on the wider community through significantly
improved tooling, libraries, and documentation.
Ensuring that the Platform is viable for industrial users also
ensures that it (1) has a long-term future and that (2) has the ability
to scale right from beginner through to industrial use-cases.
A Platform that can scale in this way also has benefits for
establishing workflows and practices that help every community member.

However, each of the major users also have large codebases with distinct
coding styles and they have often built their own extensive libraries to
complement the OCaml standard library and open-source ecosystem. A
defining characteristic of OCaml programming is **modularity**, where one
component can be swapped out for another component with the same
interface. Therefore, the Platform follows the same philosophy; it does
*not* mandate
any given set of libraries, but instead focuses on providing the
tooling to assemble, build, test and document a set of packages geared
towards certain uses. Example uses thus include the Core
standard library from Jane Street, the Ocsigen client and server web
framework that compiles OCaml into JavaScript, and the Mirage OS library
operating system.

### Creating a modular Platform, based on OPAM

To restate the above, the Platform does not *mandate* the use of
specific libraries or even tools. In fact, the only pre-requisite is the
use of OPAM and OPAM repositories. These repositories can be custom OPAM
repositories, such as those maintained by Citrix for Xen packages and by
the Ocsigen team for their packages, as well as the main OPAM
repository. This allows a completely distributed workflow, which enables
any organisation to maintain internal repositories and infrastructure as
they wish, while still interacting with the main repository.

In this sense, the OCaml Platform is markedly different to other platform
efforts that developers may have experienced.  Although we do aim to
release a set of tools to aid an efficient workflow, we also allow for any
organisation or team to define their own 'platform', based on their
preferred/pre-existing tools and services. The only instance where this
tooling is pre-defined is around the main OPAM repository with respect to
testing and publishing, which has already been in place for some time.

Considered another way, the Platform is a *workflow* based around OPAM
and we are releasing a set of suggested tools and components that support
that workflow.

This workflow is outlined later and the three main areas of focus are:
- **development**, developing a package on an individual's machine;
- **publication**, the process of creating package releases to share
  with others — either within an organisation or on the open web;
- **distribution**, the dissemination of completed package releases,
  tutorials and documentation.
There are various tools used for different steps in this workflow, with a
handful of them spanning multiple areas (most obviously OPAM).

Over time, as more code is shared and re-used and the workflow becomes
more widely adopted, we expect a standard set of libraries to naturally
emerge through a consensus-based process. Indeed, we are already seeing this
with the tools we have released so far (e.g. OPAM).


## OPAM

*More detail about OPAM 1.2 will be available in a post tomorrow but in
meantime we encourage users to try the newly released beta.
Instructions are with the [release notes][opam-12].*

![Growth of OPAM packages](/images/opam-pacakge-growth.jpg)

OPAM plays a key role in the tooling for the Platform, by providing a
frontend that can control a concurrently installed set of compiler
versions and package sets. Since its public release, thousands of
packages and revisions have been added to the central repository, and
growth continues strongly in 2014 (see Figure above). An important
feature of OPAM is that it tracks multiple revisions of a single
package, thereby letting packages rely on older interfaces if they need
to. It also supports multiple package repositories, letting users blend
the global stable package set with their internal revisions, or building
completely isolated package universes.

The [OPAM 1.2 release][opam-12] has been adapted
to make OPAM easier to package up on
different operating systems, and features better support for external
solvers to deal with the growth of the package database. Most
importantly, much of the package management logic is available as an
OCaml library ([opam-lib][]), making it easy for other tools to
interface directly with OPAM.

One major use of OPAM as a library is that we now have the capability to
run tests over the complete package set to understand the ramifications
of proposed compiler changes and any breaking effects they might have.
This substantially improves the usage data available to anyone working
on compiler modifications, and has been used extensively during the
development of OCaml 4.02.



## OCaml.org

Parallel to the Platform efforts we are also working with the OCaml.org team
on reimplementing and adding functionality to the
main ocaml.org website. This involves coalescing the outputs from the
previously mentioned projects (i.e. OPAM, documentation tools and
testing results) and presenting them in a clear and navigable way via
ocaml.org.  Much work has been done in this regard, including new
general-purpose libraries like [OMD][omd], the
[visual redesign][ocaml-org-redesign] and the upcoming work on
[OCaml Planet][ocaml-planet] (see [Amir's post][planet-post] for background).


## The Platform Workflow

The best way to describe how the Platform fits together is to outline
the kind of workflow it is designed to support. The figure below
provides an overview of this workflow.

The following is presented here as a **roadmap** so please bear in mind
that the tools are in varying stages of development from pre-alpha
to currently released. 
In addition, we consider the workflow around the main OPAM repository,
hence the references to GitHub and Travis CI. As mentioned earlier,
developers are able to compose their own tools and services to achieve
the same result.


![Platform workflow](/images/platform-workflow.jpg)

### Develop

**Write Source Code**: Write your source code assisted by tools such as
the [Merlin][] IDE assistant, which gives feedback to the developer in the
form of type information and code completion, and by libraries such as
[Ctypes][], which allows you to glue together OCaml code and C code without
writing any C.

**Initiate an OPAM Package**: In essence, this involves the creation of files
and scripts to deal with configuring, building and installing the
package. Building can be done using whichever tool the developer
prefers, for example `make`, `ocamlbuild` or `jenga`.

At the moment, we are developing [Assemblage][], which is a tool
to make it easier to describe an OCaml project and use that
description to generate relevant files (e.g build files, opam files,
etc). Please note that Assemblage is *unreleased* and we will discuss it
in a later post.

**Local Install and Testing**: Test your package release by installing
it locally. OPAM 1.2 improves support for this by allowing a package to
be installed directly from a directory containing an `opam` file. It can
even create this `opam` file itself, prompting interactively for the
required data.

### Publish

**Push Code to Git**: Once the package release has been
written and tested locally, it is time to publish a release
candidate. In our case, we use GitHub as the default
online site for sharing code, but the Platform is intended to
work with any similar Git-style hosting platform.
(NB: There is a pure [OCaml Git][ocaml-git] implementation now
available in OPAM, to make manipulating such git repositories easy
from within Platform tools.)

**Code Review**: An emergent trend in OPAM packages has been for users
to request code reviews of their newly uploaded package releases. Such
code review can take place on the GitHub pull request interface, but
more specific code review tools are also available (including a
home-grown one developed at Jane Street, called [Iron][]).

**Submit Package to an OPAM Repository**: The release
candidate is then submitted to an OPAM repository. In the case of the
main OPAM repository this is done as a GitHub pull request.
This process can be automated via a command-line plugin to OPAM that stores
the user's GitHub login token and creates the pull request to the
central OPAM repository without having to use the web interface.
This tool is in development and not yet available.

**Automated Testing**: When a package release is submitted to an OPAM
repository that repository can initiate automated integration testing. 
In the case of the main OPAM repository, packages are run through the OCamlot
continuous integration system, which initially tests it on Ubuntu Linux,
using [Travis CI][travis].
The tests are run inside a Linux container or a Xen VM, with extra
system libraries specified in the package metadata. Every new package
must pass this test before being merged into the main package repository.
Unit tests can also be run inside the sandbox environment.

To ensure the ongoing health of the main repository, daily bulk build
regression tests are also run (using a combination of [Docker][docker]
and Xen). Additional bulk runs also test the package database on
'unusual' platforms that developers are unlikely to have access
to, such as ARM, PowerPC and Sparc devices, and a combination of MacOS
X, FreeBSD, OpenBSD and Linux distributions. The results are logged to a
[central Git repository][opam-bulk-logs] and failures triaged by a
combination of automated tools and the OPAM developers.

**Publish Release on an OPAM Repository:** When a package release
passes testing and review, its release can be finalised and merged into
the OPAM repository. Finalising a release is automated using the OPAM
command-line.

**Generate a site based on an OPAM Repository**:
The `opam2web` tool can then generate a static site based on a
repository, which is similar in structure to that at [opam.ocaml.org][].
For instance, Citrix maintains their own OPAM remote (just for Xen packages),
as does the Ocsigen team.

### Distribute

**Source Release:** Once a package release is published, it is available
for install using OPAM.

**Online documentation:** When a package release is published, a
canonical set of online documentation is generated using
`opam-doc`. These are gathered across all the packages and combined into
a cross-referenced HTML site. This can also be run locally such that a
developer can access cross-referenced documentation for the packages
installed in a given opam switch.

**Multi-arch Binary Releases:** While OPAM is a source-based package
manager, it is important to allow users to download a binary snapshot
for their operating system to get started quickly. The [0Install][]
binary distribution system, written in OCaml, supports the
redistribution of signed binaries and the bulk build systems can upload
binaries to 0install repositories.  Binary distribution is a significant
improvement and especially important for slower platforms such as the
Raspberry Pi.

## Summary

The OCaml Platform is the natural evolution of OPAM and represents a
workflow which does not mandate a particular set of tools or services.
To achieve an efficient workflow we are releasing a coherent set of
tools that enable developers, both novice and experienced, to better
manage the life-cycle of their OCaml software.
Over the coming weeks we will delve into
the above elements of the workflow and describe the new tools,
starting with an overview of OPAM 1.2.
We welcome wider feedback so please do join the
[Platform mailing list][platform-list] to discuss or comment on these posts.

[opam-12]: TODO
[wireframe-demo]: http://amirchaudhry.com/wireframe-demos-for-ocamlorg
[try-ocaml]: http://try.ocamlpro.com
[Merlin]: https://github.com/the-lambda-church/merlin
[Ctypes]: https://github.com/ocamllabs/ocaml-ctypes
[docker]: http://docker.io
[opam-bulk-logs]: http://github.com/ocaml/opam-bulk-logs
[0Install]: http://0install.net
[platform-list]: http://lists.ocaml.org/listinfo/platform
[omd]: https://github.com/ocaml/omd
[ocaml-planet]: https://github.com/ocaml/ocaml.org/tree/syndic
[planet-post]: http://amirchaudhry.com/writing-planet-in-pure-ocaml
[ocaml-org-redesign]: http://amirchaudhry.com/announcing-new-ocamlorg

[ocaml-git]: https://opam.ocaml.org/packages/git/git.1.2.0/
[opam-lib]: http://opam.ocaml.org/packages/opam-lib/opam-lib.1.1.1/
[Iron]: https://blogs.janestreet.com/code-review-that-isnt-boring/
[opam.ocaml.org]: https://opam.ocaml.org
[travis]: https://travis-ci.org
[Assemblage]: https://github.com/samoht/assemblage
