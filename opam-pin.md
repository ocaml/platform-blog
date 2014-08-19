title: "OPAM 1.2: Repository Pinning"
authors: "Louis Gesbert" {"http://ocamlpro.com"}
date: "2014-08-19"
--BODY--

<img style="float:left; padding: 5px" src="camel-pin.jpg" width="200px"></img>

Most package managers support some _pin_ functionality to ensure that a given
package remain at a particular version without being upgraded.
The stable OPAM 1.1 already supports this by allowing any existing package to be
pinned to a _target_, which could be a specific released version, a local filesystem
path, or a remote version-controlled repository.

The OPAM 1.1 pinning workflow only let you pin packages that _already exist_ in your OPAM
repositories though. To declare a new package, you had to go through creating a
local repository, register it in OPAM, and add your package definition there.
While clear enough, that workflow required the user to know about the repository
format and the configuration of an internal repository in OPAM before getting to
writing a simple package. Besides, you were on your own for writing the package
definition, and the edit-test loop wasn't as friendly as it could have.

A natural, simpler workflow emerged from allowing users to _pin_ new package
names that don't yet exist in the repository:

* choose a name for your new package package
* `opam pin add` in the development source tree
* the package is created on-the-fly and registered locally.

To make it even easier, OPAM can now interactively help you in writing the
package definition, and you can test your updates with a single command.
This blog post explains this new OPAM 1.2 functionality in more detail,
and you may also want to check out the new [Packaging tutorial][doc-packaging]
relying on this workflow.


### From source to package

#### "opam pin add"

The command `opam pin add <name> <target>` will pin package `<name>` to
`<target>`. Now what we're interested in is pinning to a project's source
directory. For this post I found a tiny tool that I wrote some time ago and
never got released: [ocp-reloc][]

```
cd src/ocp-reloc
opam pin add ocp-reloc .
```

Now if `ocp-reloc` was an existing package, the metadata would be fetched from
the repositories. Here it doesn't exist, and OPAM will instead ask for
on-the-fly creation :

```
Package ocp-reloc does not exist, create as a NEW package ? [Y/n] y
ocp-reloc is now path-pinned to ~/src/ocp-reloc
```

> NOTE: if you are using __beta4__, you may get a _version-control_-pin instead,
> because we added auto-detection of version-controlled repos. It turned out to
> be confusing, because your changes wouldn't be reflected until you commit, so
> this has been reverted in favor of a warning. Use `--kind path` to make sure
> you get a _path_-pin.


#### Template

Now your package still needs some kind of definition for OPAM to acknowledge it;
that's where templates kick in, the above triggering an editor with a pre-filled
`opam` file that you will just have to complete. This not only saves time in
looking up the documentation, it also helps getting consistent package
definitions, reduces errors, and promotes filling in optional but recommended
fields (homepage, etc.).

```
opam-version: "1.2"
name: "ocp-reloc"
version: "0.1"
maintainer: "Louis Gesbert <louis.gesbert@ocamlpro.com>"
authors: "Louis Gesbert <louis.gesbert@ocamlpro.com>"
homepage: ""
bug-reports: ""
license: ""
build: [
  ["./configure" "--prefix=%{prefix}%"]
  [make]
]
install: [make "install"]
remove: ["ocamlfind" "remove" "ocp-reloc"]
depends: "ocamlfind" {build}
```

After filling this with some details, most importantly the dependencies and
build instructions, I can just save and exit; in case of syntax errors, I can
edit again right away :

```
[ERROR] File "/home/lg/.opam/4.01.0/overlay/ocp-reloc/opam", line 13, character 35-36: '.' is not a valid token.
Errors in /home/lg/.opam/4.01.0/overlay/ocp-reloc/opam, retry editing ? [Y/n]
```

#### Installation

And then you probably want to try your package right away, so OPAM's default
action is to try and install (unless you specified `-n`) :

```
ocp-reloc needs to be installed.
The following actions will be performed:
 - install   cmdliner.0.9.5                        [required by ocp-reloc]
 - install   ocp-reloc.0.1*
=== 1 to install ===
Do you want to continue ? [Y/n]
```

I usually don't get it working the first time around, but `opam pin edit
ocp-reloc` and `opam install ocp-reloc -v` can be used to edit and retry until
it does.

#### Updates

How do you keep working on your project, now that you are installing through
OPAM ? This is as simple as :

```
opam upgrade ocp-reloc
```

Which will pick up changes and reinstall, if any.

While we were above concerned with the metadata locally used by your OPAM
installation, you'll probably want to share this among developers even if
you're not releasing anything yet. Actually, by now you would already have been
prompted by OPAM to save the `opam` file back to your source directory. Adding
it to your source will come in handy.


### From package to package

#### Picking up your development package

Now if another developer wants to pick up `ocp-reloc`, they can directly use
your existing metadata by issuing:

```
git clone git@github.com:OCamlPro/ocp-reloc.git
opam pin add ocp-reloc/
```

Even specifying the package name is optional since this is documented in
`ocp-reloc/opam`. They can start hacking, and if needed use `opam pin edit` to
amend the opam file too. No need for a repository, no need to share more than a
versionned `opam` file within your project.

#### Now what about already existing packages ?

We have been focusing until now on an unreleased package, but these
functionalities are yet of great help to handle existing packages, wether you
need to quickly hack into them or are just curious.

```
opam source omd --pin
cd omd.0.9.7
...patch...
opam upgrade omd
```

This will also take care of recompiling any installed packages dependent on
`omd` using your patched version, so that you notice any issues right away. Once
the OPAM repository has been updated with the new field `dev-repo:`, you will
also be able to directly pin to the upstream with `opam source --dev-repo
--pin`.

And if the upstream contains an `opam` file, as above, it will be picked up
rather than the one from the OPAM repository as soon as you pin : the idea is to
have one _development_ `opam` file that is versionned along your source, can get
broken and evolve quickly ; and a _release_ `opam` file that is on the OPAM
repository, and can be updated independently without making a new release.

How to get from the former to the latter will be the object of another post !


[doc-packaging]: https://opam.ocaml.org/doc/1.2/Packaging.html "OPAM 1.2 doc preview, packaging guide"
[ocp-reloc]: https://github.com/OCamlPro/ocp-reloc "ocp-reloc repo on Github"
