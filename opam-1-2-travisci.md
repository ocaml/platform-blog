title: "OPAM 1.2 and Travis CI"
authors: "Thomas Gazagnaire" {"http://gazagnaire.org"}
date: "2014-10-31"
--BODY--

The [new pinning feature][pin] in OPAM 1.2 enables new interesting workflow
for my projects. I will briefly describe one of them here: simplifying
continuous testing with Travis CI.

## Creating an opam file

As explained in the [previous post][pin], adding an `opam` file to a
project is now a very good idea. It's very easy with OPAM 1.2:

```
$ opam pin add <my-project-name> .
[... follow the instructions ...]
```

Once this is done, you can continue to edit the new `opam` and check
that is is consistent using `opam lint`.

## Installing the Travis CI scripts

Travis CI is a free service to enable continuous testing on your
Github projects. It uses Ubuntu containers and run (sometimes slowly)
the tests for at most 50 minutes.

To use Travis CI with your OCaml project, you can follow the instructions on
https://github.com/samoht/ocaml-travisci-skeleton. Basically, this involves:

- adding
  [2](https://github.com/samoht/ocaml-travisci-skeleton/blob/master/.travis-ci.sh)
  [files](https://github.com/samoht/ocaml-travisci-skeleton/blob/master/.travis.yml)
  at the root of to your project; and

- signing in at https://travis-ci.org/ (using your Github account) and
  enabling the tests for your project (click on the '+' button on the
  left pane).

And that's it, your project now has continuous integration, using OPAM 1.2
pinning feature and Travis CI scripts.

[pin]: https://opam.ocaml.org/blog/opam-1-2-pin/