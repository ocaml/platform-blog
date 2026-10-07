title: "opam 2.0 tips, compiler edition"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
]
date: "2019-04-10"
--BODY--

This blog post answers to the question _"How can I install / use ocaml compiler xxx with opam?"_, where _xxx_ can be a system install, a local directory, an upgrade, etc.

## System OCaml compiler upgrade

If you upgrade your system OCaml compiler, all switches that rely on `ocaml-system` as a compiler will be out of sync, as the installed version of `ocaml-system` is no more the version of your `usr/bin/ocamlc`.

In opam 2.1.0, this upgrade will be done automatically with [`opam upgrade`](https://github.com/ocaml/opam/pull/3816), but for the moment, you need to do it manually. We need to upgrade version of `ocaml-system`. As those packages are compiler packages, the are locked (_base packages_).

```bash
$ ocamlc -vnum
4.02.3

# update ocaml system
$ ocaml -vnum
4.07.1

$ opam upgrade #fails
[WARNING] File /usr/bin/ocamlc, which package ocaml-system.4.02.3 depends upon, was changed on your system. ocaml-system has been marked as removed, and will be reinstalled if necessary.
[WARNING] Upgrade is not possible because of conflicts or packages that are no longer available:
The following dependencies are the cause:
  - ocaml → ocaml-base-compiler = 4.02.3
      conflict with the base packages of this switch
  - ocaml → ocaml-system = 4.02.3
      unmet availability conditions: sys-ocaml-version = "4.02.3"
  - ocaml → ocaml-variants >= 4.02.3
      conflict with the base packages of this switch

You may run "opam upgrade --fixup" to let opam fix the current state.

The former state can be restored with:
    opam switch import "/tmp/rrr/default/.opam-switch/backup/state-20181219102938.export"

$ opam list --base #base is still on 4.02.3
[WARNING] File /usr/bin/ocamlc, which package ocaml-system.4.02.3 depends upon, was changed on your system. ocaml-system has been marked as removed, and will be reinstalled if necessary.
# Packages matching: base
# Name        # Installed # Synopsis
base-bigarray base
base-threads  base
base-unix     base
ocaml         4.02.3      The OCaml compiler (virtual package)
ocaml-config  1           OCaml Switch Configuration
ocaml-system  --          The OCaml compiler (system version, from outside of opam)

# install correct ocaml packages
$ opam install ocaml.4.07.1 ocaml-system.4.07.1 --unlock-base
[WARNING] File /usr/bin/ocamlc, which package ocaml-system.4.02.3 depends upon, was changed on your system. ocaml-system has been marked as removed, and will be reinstalled if necessary.
The following actions will be performed:
  ∗ install   ocaml-system 4.07.1
  ↻ recompile ocaml-config 1                [uses ocaml-system]
  ↗ upgrade   ocaml        4.02.3 to 4.07.1
===== ∗ 1   ↻ 1   ↗ 1 =====

<><> Gathering sources ><><><><><><><><><><><><><><><><><><><><><><><><><><><><>

<><> Processing actions <><><><><><><><><><><><><><><><><><><><><><><><><><><><>
⊘ removed   ocaml.4.02.3
⊘ removed   ocaml-config.1
∗ installed ocaml-system.4.07.1
∗ installed ocaml-config.1
∗ installed ocaml.4.07.1
Done.

# set them as base packages
$ opam sw set-base ocaml.4.07.1 ocaml-system.4.07.1 ocaml-config --yes

$ opam list --base
# Packages matching: base
# Name       # Installed # Synopsis
ocaml        4.07.1      The OCaml compiler (virtual package)
ocaml-system 4.07.1      The OCaml compiler (system version, from outside of opam)

$ opam upgrade
Already up-to-date.
Nothing to do.
```

## Local compiler switch

You can create a local switch with your locally modified compiler. OCaml compiler will have [opam files integrated](https://github.com/ocaml/ocaml/pull/2207), that will permit to pin locally the compiler.
Once you have these opam files in your compiler directory, you can create the new switch with

```bash
$ opam switch create . --empty
$ opam install .
```

## How to create a switch with an unreleased version

As noted Damien Doligez in [ocaml 4.08.0 beta2 annoucement](https://inbox.ocaml.org/caml-list/50013A0A-B5B3-47B7-BADF-32A9A94D9C15@inria.fr), a new repository `[ocaml-beta-repository](https://github.com/ocaml/ocaml-beta-repository)` is set for unreleased OCaml compiler packages.

You can add it in your switch (you can also use the `--all-switches` option to add it to all your switches)

```bash
$ opam repo add ocaml-unreleased git+https://github.com/ocaml/ocaml-beta-repository
[ocaml-unreleased] Initialised
[NOTE] Repository ocaml-unreleased has been added to the selections of switch dim only.
       Run `opam repository add ocaml-unreleased --all-switches|--set-default' to use it in all existing switches, or in newly created switches, respectively.
```

On switch creation, only default switch is looked up to retrieve available compilers. To have opam take into account the new repo also, you need to specify it using `--repositories` argument

```bash
$ opam switch create ocaml-variants.4.08.0+beta2 --repositories=default,ocaml-unreleased
```

It is also possible to define directly the new repository at switch creation time

```bash
$ opam switch create ocaml-variants.4.08.0+beta2 --repositories=default,ocaml-unreleased=git+https://github.com/ocaml/ocaml-beta-repository.git
```

### If I want to use a system install of an unreleased OCaml version?

In this case, the process is the same than when you want to create a new switch with ocaml-system, just you need to be sure to use the `ocaml-beta-repository` on the switch creation, and that your ocaml version is a non patched one (e.g. 4.08.0)

```bash
$ opam switch create unreleased ocaml-system.4.08.0 --repositoies=default,ocaml-unreleased
```

### But my system installed compiler is a patched version...

Switches that uses system OCaml compiler need installation of `ocaml-system` package, but it is available in the main [opam repository](https://github.com/ocaml/opam-repository) only for released version.

If you want try a patched or trunk compiler, it needs the package `ocaml-system` of the current installed version, and OCaml version is not as simple as `4.08.0`. You need to install the package `ocaml-system.4.08.0+xxx`.

In order to do that, we'll have to changes those packages and create new ones.

Let's take as example a local install of `ocaml.4.08.0+dev2-2019-01-18`.

```bash
$ ocamlc -vnum
4.08.0+dev2-2019-01-18
```

We'll need to create an empty switch and then create the new custom `ocaml-system` and `ocaml` packages by pinning and editing them

```bash
$ opam switch create ocaml-local --empty
```

Then, pin `ocaml-system` to the needed version, using the generic [`ocaml-system`](https://github.com/AltGr/ocaml-system) project (don't forget to add previously `ocaml-unreleased` repo if it is an unreleased version).


```bash
$ opam pin ocaml-system.4.08.0+dev2-2019-01-18 git+https://github.com/AltGr/ocaml-system
[ocaml-system.4.08.0+dev2-2019-01-18] synchronised (git+https://github.com/AltGr/ocaml-system)
ocaml-system is now pinned to git+https://github.com/AltGr/ocaml-system (version 4.08.0+dev2-2019-01-18)

The following actions will be performed:
  ∗ install base-bigarray base
  ∗ install base-threads  base
  ∗ install base-unix     base
  ∗ install ocaml-system  4.08.0+dev2-2019-01-18*
  ∗ install ocaml-config  1
  ∗ install ocaml         4.08.0
===== ∗ 6 =====
Do you want to continue? [Y/n] y

<><> Processing actions <><><><><><><><><><><><><><><><><><><><><><><><><><><><>
∗ installed base-bigarray.base
∗ installed base-threads.base
∗ installed base-unix.base
▼ retrieved ocaml-system.4.08.0+dev2-2019-01-18  (git+https://github.com/AltGr/ocaml-system)
∗ installed ocaml-system.4.08.0+dev2-2019-01-18
∗ installed ocaml-config.1
∗ installed ocaml.4.08.0
Done.

```

If you installed the compiler in a custom prefix, don't forget to export your `PATH` or prefix install commands with the updated `PATH`. For linux user, if this path is in a unusual location, adds it also to `OPAM_USER_PATH_RO` to be automatically handled by sandbox script.

One last step, for switch consistency, add those packages to the switch base packages, i.e., packages that can not be changed (it is set by default on each non-empty switch creation)

```base
$ opam list --base
# Packages matching: base
# No matches found

$ opam list
# Packages matching: installed
# Name        # Installed            # Synopsis
base-bigarray base
base-threads  base
base-unix     base
ocaml         4.08.0                 The OCaml compiler (virtual package)
ocaml-config  1                      OCaml Switch Configuration
ocaml-system  4.08.0+dev2-2019-01-18 pinned to version 4.08.0+dev2-2019-01-18 at git+https://github.com/AltGr/ocaml-system

#add them to base
$ opam list -s | xargs opam switch set-base

$ opam list --base
# Packages matching: base
# Name        # Installed            # Synopsis
base-bigarray base
base-threads  base
base-unix     base
ocaml         4.08.0                 The OCaml compiler (virtual package)
ocaml-config  1                      OCaml Switch Configuration
ocaml-system  4.08.0+dev2-2019-01-18 pinned to version 4.08.0+dev2-2019-01-18 at git+https://github.com/AltGr/ocaml-system

```

And everything is set up to install your favorite packages!











=========================================

Switches that uses system OCaml compiler need installation of `ocaml-system` package, but it is available in the main [opam repository](https://github.com/ocaml/opam-repository) only for released version. New repository `[ocaml-beta-repository](https://github.com/ocaml/ocaml-beta-repository)` contains opam packages of unreleased version of OCaml.

Then, you have to add this repository in your opam switch (you can also use the `--all-switches` option to add it to all your switches)

```bash
$ opam repo add ocaml-unreleased git+https://github.com/ocaml/ocaml-beta-repository
[ocaml-unreleased] Initialised
[NOTE] Repository ocaml-unreleased has been added to the selections of switch dim only.
       Run `opam repository add ocaml-unreleased --all-switches|--set-default' to use it in all existing switches, or in newly created switches, respectively.
```

You can then see what packages are proposed on this repository

```bash
$ opam list --all --repo ocaml-unreleased
# Packages matching: any & from-repository(ocaml-unreleased)
# Name         # Installed # Synopsis
ocaml          4.02.3      The OCaml compiler (virtual package)
ocaml-sytem    4.02.3      The OCaml compiler (system version, from outside of opam)
ocaml-variants --          current trunk
```

And check the new available versions, here `4.08.0` & `4.09.0`

```bash
$ opam show ocaml-system -f all-versions
3.07 [...] 4.06.0  4.06.1  4.07.0  4.07.1  4.08.0  4.09.0
```

In that case, you just need to tell opam to rely on system OCaml for the new switch, with

```bash
$ opam switch create unreleased ocaml-system.4.08.0

<><> Gathering sources ><><><><><><><><><><><><><><><><><><><><><><><><><><><><>

<><> Processing actions <><><><><><><><><><><><><><><><><><><><><><><><><><><><>
∗ installed base-bigarray.base
∗ installed base-threads.base
∗ installed base-unix.base
∗ installed ocaml-system.4.08.0
∗ installed ocaml-config.1
∗ installed ocaml.4.08.0
Done.
```

