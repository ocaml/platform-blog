title: "OPAM 1.2 and Travis CI"
authors: "Thomas Gazagnaire" {"http://gazagnaire.org"}
date: "2014-10-31"
--BODY--

The [new pinning feature][pin] of OPAM 1.2 enables new interesting
workflows for your OCaml projects. I will briefly describe one of them
here: simplifying continuous testing with [Travis CI][travis] and
[Github][github].

## Creating an opam file

As explained in the [previous post][pin], adding an `opam` file at the
root of your project is now a very good idea. It's very easy with OPAM
1.2:

```
$ opam pin add <my-project-name> .
[... follow the instructions ...]
```

Once this is done, you can continue to edit the new `opam` and check
that is is consistent using `opam lint`.

If you want to run tests, do not forget to add `{test}` dependencies
and a `build-test` field. For instance, if you use `oasis` and
`ounit`, you can use something like that:

```
build: [
  ["./configure" "--prefix=%{prefix}%" "--%{ounit:enable}%-tests"]
  [make]
]
build-test: [make "test"]
depends: [
  ...
  "ounit" {test}
]
```

Without the `build-test` field, the continuous integration scripts
will just test the compilation of your project for various OCaml
compilers (which is already quite a good thing).

## Installing the Travis CI scripts

Travis CI is a free service to enable continuous testing on your
Github projects. It uses Ubuntu containers and run (sometimes slowly)
the tests for at most 50 minutes.

To use Travis CI with your OCaml project, you can follow the instructions on
https://github.com/samoht/ocaml-travisci-skeleton. Basically, this involves:

- adding
  [.travis.yml](https://github.com/samoht/ocaml-travisci-skeleton/blob/master/.travis.yml)
  at the root of your project. You can tweak this file to test your
  project with different versions of OCaml. By default, it will use
  the latest stable version (today: 4.02.1). Add a line:

    ````
  - OCAML_VERSION=<VERSION>
    ````

  at the end of the file for every OCaml version that you want to
  test. The supported values for `<VERSION>` are `3.12`, `4.00`,
  `4.01` and `4.02.

- signing in at https://travis-ci.org/ using your Github account and
  enabling the tests for your project (click on the '+' button on the
  left pane).

And that's it, your project now has continuous integration, using OPAM 1.2
pinning feature and Travis CI scripts.

[pin]: https://opam.ocaml.org/blog/opam-1-2-pin/
[travis]: https://travis-ci.org/
[github]: https://github.com/