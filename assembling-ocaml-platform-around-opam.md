title: "Assembling the OCaml Platform around OPAM"
authors: "OCaml Platform Team" {"https://opam.ocaml.org/blog/"}
date: "2014-08-18"
--BODY--

As mentioned in the [OPAM 1.2 beta announcement][opam-12], we are starting the
journey towards an *OCaml Platform* that combines the OCaml compiler toolchain
with a coherent workflow for build, documentation, testing and IDE integration.
This is a *collaborative* effort across the OCaml community, tied together by
the [OCaml Labs][ocl-www] group in Cambridge and [OCamlPro][ocp-www] in France.

The OCaml Platform is the natural evolution of OPAM, since OPAM forms the
substrate on which tools can be built and it facilitates a consistent
development workflow. Over the next few weeks we will have a series of posts
that cover the new features in OPAM 1.2, describe the additional components,
and highlight some of the relevant work from the community that supports these
integrative efforts.

The rest of this post we will give an overview of what the Platform is (and is
not), briefly cover some of the major elements and then describe an example
workflow that the Platform can enable. Later posts in this series will go into
more depth on each of the components and how they fit together.
We welcome wider discussion so please do **join
[platform@lists.ocaml.org][platform-list]** and make your views known.


## Overall ethos

We have initially taken direction from major industrial users because these
groups have a great deal of experience of using the language at scale (e.g.
millions of lines of code, thousands of directories with complicated testing
and build requirements as well as the concomitant issues in training new
developers). For the Platform to succeed, it has to be a viable product for
those heavy users of OCaml. We're of the pragmatic view that examining and
solving the problems that such organizations have will ultimately result in a
large and positive impact on the wider community through significantly improved
tooling, libraries, and documentation.  A Platform that can scale up to larger
commercial uses this way also has benefits for establishing simpler workflows
and practices that help every community member.

However, each of the major users also have large codebases with distinct coding
styles and they have often built their own extensive libraries to complement
the OCaml compiler standard library and open-source ecosystem. A defining
characteristic of OCaml programming is **modularity**, where one component can
be swapped out for another component with the same interface. Therefore, the
Platform follows the same philosophy; it does *not* mandate any given set of
libraries, but instead focuses on providing the tooling to assemble, build,
test and document a set of packages geared towards certain uses. Example uses
thus include the [Core][] standard library from Jane Street, the [Ocsigen][]
client and server web framework, and the [Mirage OS][mir-www] library
operating system.

### Creating a modular Platform, based on OPAM

To restate the above, the Platform does not *mandate* the use of specific
libraries or even tools. In fact, the only prerequisite is the use of OPAM.
These repositories can be custom OPAM repositories, such as those maintained by
Citrix for the [XenAPI][xapi] project and by the Ocsigen team for their
packages, as well as the main OPAM repository. This allows a completely
distributed workflow, which enables any organisation to maintain internal
repositories and infrastructure as they wish, while still interacting with the
main repository.

In this sense, the OCaml Platform is *markedly different* to other platform
efforts that developers may have experienced.  Although we do aim to release a
set of tools to aid an efficient workflow, we also allow for any organisation
or team to define their own 'platform', based on their preferred/pre-existing
tools and services. The only instance where this tooling is predefined is
around the main OPAM repository with respect to testing and publishing, which
has already been in place for some time.

This is why we describe the Platform as a *workflow* based around OPAM
and we are releasing a set of suggested tools and components to support
one such workflow.

This workflow is outlined later and the three main areas of focus are:
- **development**, developing a package on an individual's machine;
- **publication**, the process of creating package releases to share with others — either internal to an organisation or on the open web;
- **distribution**, the dissemination of completed package releases, tutorials and documentation.

There are various tools used for different steps in this workflow, with a
handful of them spanning multiple areas (most obviously OPAM).

Over time, as more code is shared and reused and the workflow becomes more
widely adopted, we expect a standard set of libraries to naturally emerge
through a consensus-based process. Indeed, we are already seeing this with
some of the tools we have released so far.

## OPAM 1.2

OPAM plays a key role in the tooling for the Platform by providing a
command-line front-end that can control a concurrently installed set of
compiler versions and package sets.

It's therefore every important to make sure the OPAM installation procedure is
as seamless as possible on a variety of operating systems. The
[OPAM 1.2 release][opam-12] has been adapted to make such packaging easier,
and we have **released binary RPMs and DEBs** along with the current beta
(Windows still isn't packaged out of the box, but support is steadily
improving!). Most importantly, much of the package management logic is
available as an OCaml library ([opam-lib][]), making it easy for other tools
to interface directly with OPAM.

One major use of OPAM as a library is that we now have the capability to test
experimental compiler patches over the complete package set to understand the
ramifications of proposed language changes and any breaking effects they might
have.  This substantially improves the usage data available to anyone working
on compiler modifications, and has been [used extensively][anil-docker] during
the development of OCaml 4.02.

### Web-based view of activity on an OPAM Repository

In addition to OPAM itself, we are releasing new tools to generate a static
website for an OPAM repository, which is incrementally updated based on
activity around that repository.

The tools will be able to draw information from other services (e.g. code
review tools and testing infrastructure), to provide an up-to-date view about
the status of existing and submitted packages. These tools are based on
`opam2web`, which currently generates the site at [opam.ocaml.org][] and will
enable any organisation to create an accessible web-view into their workflow.
More detail about how to set these up will follow in a later post.

<!-- but for examples of how
this works in practice, we can examine the package sets for the
[Mirage OS libraries][mir-opam] and the [Xapi project libraries][xapi-opam],
as well as the main [OPAM site][opam.ocaml.org]. -->


## OCaml.org

Parallel to the Platform efforts we are also working with the
[OCaml.org team][ocamlorg-team] on reimplementing and
[adding functionality][wireframe-demo] to
the main OCaml.org website. This involves coalescing the outputs from the
previously mentioned projects (i.e. OPAM, documentation tools and testing
results) and presenting them in a clear and navigable way. Much work has been
done in this regard, including new general-purpose libraries (e.g. for
[Markdown in pure OCaml][omd]), the [visual redesign][ocaml-org-redesign] and
the upcoming work on [a pure OCaml Planet][planet-post].


## The Platform Workflow

The figure below provides an overview of the emerging Platform workflow.  This
is just the first iteration that we're making public to solicit feedback, and
we anticipate that changes will happen as uptake increases and it gets tested
more widely in production.  Remember also that the individual tools that we
discuss can be swapped out with others in many cases, depending on the
developer's preference.

<!-- TODO: INSERT LEO'S WORKFLOW DIAGRAM -->
<!-- https://github.com/lpw25/platform/blob/master/blog/table.html -->

### Develop

**Write Source Code**: Write your source code assisted by tools such as the
[Merlin][] IDE assistant, which gives feedback to the developer in the form of
type information and code completion, and by libraries such as [Ctypes][],
which allows you to glue together OCaml code and C code, without writing any C.

**Assemble an OPAM Package**: In essence, this involves the creation of files
and scripts to deal with configuring, building and installing the package.
Building can be done using whichever tool the developer prefers, for example
`make`, `ocamlbuild` or `jenga`.

One of the new tools that we have developed to integrate this workflow more
tightly with OPAM is [Assemblage][].  This is a DSL that describes an OCaml
project and uses that description to generate relevant files (e.g build files,
opam files, etc). Assemblage is in alpha testing at the moment, and will be
discussed in an upcoming post.

**Local Testing**: Test your package release by installing it locally. OPAM 1.2
improves support for this by allowing a package to be installed directly from a
source code directory containing an `opam` file. It can even create this `opam`
file itself, prompting interactively for the required data.

### Publish

**Push Code Remotely**: Once the package release has been written and tested
locally, it is time to publish a release candidate. In our case, we use GitHub
as the default online site for sharing code, but OPAM itself supports arbitrary
Git, Mercurial or Darcs remotes. There is a [pure OCaml Git][ocaml-git]
implementation now available in OPAM, to make manipulating git repositories
particularly easy from within Platform tools. Contributions are welcome for
Mercurial and Darcs equivalents!

**Submit Package to an OPAM Repository**: The release candidate is then
submitted to an OPAM repository. In the case of the main OPAM repository this
is done as a GitHub pull request. This process can be automated via a
command-line plugin to OPAM that stores the user's GitHub login token and
creates the pull request to the central OPAM repository without having to use
the web interface. This tool is in development and not yet available.

The web-view into the OPAM repository allows the journey of the release
candidate to be easily monitored (e.g code review, testing), with pages
automatically being updated or generated as required.

**Code Review**: An emergent trend in OPAM packages has been for users to
request code reviews of their newly uploaded package releases. One of the most
common interfaces for this is currently the [GitHub pull request][gh-pr]
interface. One drawback that we have observed is that most existing code review
tools are based around patch review, and not the review of entire codebases.
More specific code review tools are under active development within the OCaml
community, such as Jane Street's [Iron][], and we will be experimenting with
deploying these for community use once they are released into OPAM. Whichever
tool is used, the progress of a release candidate's code review can be
displayed within the web-view of the OPAM repository.

**Automated Testing**: When a package release is submitted to an OPAM
repository, that repository can initiate automated integration testing, with
progress and results made visible in the web-view of the OPAM repository.

In the case of the main OPAM repository, packages are run through the
[Travis CI][travis] system, which initially tests it on Ubuntu Linux and
MacOS X using a matrix of OCaml compiler and OPAM versions. Every new package
must pass this test before being merged into the main OPAM package repository.

To ensure the ongoing health of the main repository, daily
[bulk build][opam-bulk-logs] regression tests are also run (using a
combination of [Docker][docker] and Xen). Additional bulk runs also test the
package database on 'unusual' platforms that developers are unlikely to have
access to, such as ARM, PowerPC and Sparc devices, and a combination of
MacOS X, FreeBSD, OpenBSD and Linux distributions. The results are logged to a
[central Git repository][opam-bulk-logs] and failures triaged by a combination
of automated tools and the OPAM developers.

**Publish Release on an OPAM Repository:** When a package release passes
testing and review, its release can be finalised and merged into the OPAM
repository. Finalising a release is automated using the OPAM command-line and
the web-view can be automatically generated.


### Distribute

**Source Release:** Once a package release is published, it is available for
install using OPAM.

**Online documentation:** When a package release is published, a canonical set
of online documentation is generated using `opam-doc`. These are gathered
across all the packages and combined into a cross-referenced HTML site. This
can also be run locally such that a developer can access cross-referenced
documentation for the packages installed in a given `opam switch`.

**Multi-arch Binary Releases:** While OPAM is a source-based package manager,
it is important to allow users to download a binary image for their
operating system to get started quickly. The [0Install][] binary distribution
system, written in OCaml, supports the redistribution of signed binaries and
the bulk build systems could upload binaries to 0Install repositories.  Binary
distribution is a significant improvement and is especially important for
slower platforms such as the Raspberry Pi.

## Summary

The OCaml Platform is the natural evolution of OPAM and represents a workflow
which does not mandate a particular set of tools or services. To achieve an
efficient workflow, we are beginning the process of releasing the tools that
enable developers (both novice and experienced) to better manage the life-cycle
of their OCaml software. Over the coming weeks we will delve into the above
elements of the workflow and describe the new tools, starting with some of the
features in OPAM 1.2. We welcome wider feedback so please do join the
[Platform mailing list][platform-list] to discuss or comment on these posts.



<!-- Intro -->

[opam-12]: https://opam.ocaml.org/blog/opam-1-2-0-beta4/
[ocl-www]: http://www.cl.cam.ac.uk/projects/ocamllabs/
[ocp-www]: http://www.ocamlpro.com
[platform-list]: http://lists.ocaml.org/listinfo/platform

<!-- Ethos -->

[Core]: http://janestreet.github.io
[Ocsigen]: http://ocsigen.org
[mir-www]: http://openmirage.org
[xapi]: http://wiki.xen.org/wiki/XAPI

<!-- OPAM 1.2 -->

[opam-lib]: http://opam.ocaml.org/packages/opam-lib/opam-lib.1.1.1/
[anil-docker]: http://anil.recoil.org/2013/11/15/docker-and-opam.html
[opam.ocaml.org]: https://opam.ocaml.org
<!-- [mir-opam]: TODO
[xapi-opam]: TODO -->

<!-- OCaml.org -->

[ocamlorg-team]: http://ocaml.org/about.html
[wireframe-demo]: http://amirchaudhry.com/wireframe-demos-for-ocamlorg
[omd]: https://github.com/ocaml/omd
[ocaml-org-redesign]: http://amirchaudhry.com/announcing-new-ocamlorg
[planet-post]: http://amirchaudhry.com/writing-planet-in-pure-ocaml

<!-- Workflow -->

[Merlin]: https://github.com/the-lambda-church/merlin
[Ctypes]: https://github.com/ocamllabs/ocaml-ctypes
[Assemblage]: https://github.com/samoht/assemblage
[ocaml-git]: https://opam.ocaml.org/packages/git/git.1.2.0/
[gh-pr]: https://help.github.com/articles/using-pull-requests
[Iron]: https://blogs.janestreet.com/code-review-that-isnt-boring/
[travis]: https://travis-ci.org
[opam-bulk-logs]: http://github.com/ocaml/opam-bulk-logs
[docker]: http://docker.io
[0Install]: http://0install.net
