title: "Merlin 2.0 release"
authors: [ "Frederic Bour" {"https://github.com/def-lkb"}
           "Thomas Refis" {"https://github.com/trefis"} ]
date: "2014-11-01"
--BODY--

After months of development, we are pleased to announce the stable release of
Merlin 2.0. Supported OCaml versions range from 4.00 to 4.02.1.

### Overview

Merlin is a tool focused on helping you code in OCaml, by providing features
such as:
* automatic completion of identifiers, using scope and type informations,
* interactively typing definitions and expressions during edition,
* jumping to the definition of any identifier,
* quickly reporting errors in the editor.

### What's new

This release provides great improvements in robustness and quality of analysis.
Files that changed on disk are now automatically reloaded. 
Parsing process is finer grained to provide more accurate recovery and error
messages.
Integration with Jane Street Core and js\_of\_ocaml has also improved.

Vim & Emacs are still the main targeted editors. 
Preliminary support for Sublime Text is also available, see
[Sublime-text-merlin](https://github.com/def-lkb/sublime-text-merlin).
Help is welcome to improve and extend supported editing environments.

Windows support also received some fixes.  Merlin is now distributed in
[WODI](http://wodi.forge.ocamlcore.org/).  Integration in
[OCaml-on-windows](http://protz.github.io/ocaml-installer/) is planned.

### Installation

This new version of merlin is already available on opam with `opam install
merlin`, and can also be built from the source which are available at
[the-lambda-church/merlin](http://github.com/the-lambda-church/merlin).

### Changelog

This is a major release which we worked on for several months, rewriting many
parts of the codebase. An exhaustive list of changes is therefore impossible to
give, but here are some key points (from an user perspective):

* support for OCaml 4.02.{0,1}
* more precise recovery in presence of syntax errors
* more user-friendly messages for syntax errors
* locate now works on MLI files
* automatic reloading of .merlin files (when they are update or created), it
  is no longer necessary to restart merlin
* introduced a small refactoring command: rename, who renames all occurences
  of an identifier. See [here](http://yawdp.com/~host/merlin_rename.webm).

This release also contains contributions from: Yotam Barnoy, Jacques-Pascal
Deplaix, Geoff Gole, Rudi Grinberg, Steve Purcell and Jan Rehders.

We also thank Gabriel Scherer and Jane Street for their continued support.
