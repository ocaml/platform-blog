title: "UTop: a much improved interface to the OCaml toplevel"
authors: "Jérémie Dimino" {"https://github.com/diml"}
date: "2014-08-21"
--BODY--

OCaml comes with an interactive toplevel. If you type `ocaml` in a
shell you will get a prompt where you can type OCaml code that is
compiled and executed on the fly.

    $ ocaml
        OCaml version 4.02.0+dev12-2014-07-30

    # 1 + 1;;
    - : int = 2

You can load libraries and your own modules in the toplevel. This is
great for playing with your code. Though you might quickly notice that
the user experience is not really good as there is no editing support:
you cannot conveniently edit what you type and you cannot access the
previously typed phrases.

This can be improved by using tools such as
[ledit](http://pauillac.inria.fr/~ddr/ledit/) or
[rlwrap](http://freecode.com/projects/rlwrap) which adds line editing
support for any program: `rlwrap ocaml` . This is better but still
doesn't provides fancy features such as context sensitive completion.

That's why [UTop](https://github.com/diml/utop) was started. UTop is a
shiny frontend to the OCaml interactive toplevel, which tries to focus
on the user experience. It features:

- line editing
- real-time completion
- syntax highlighting

And many other things that makes life easier for users that have been
added over time.

What does UTop stands for?
--------------------------

UTop stands for `Universal Toplevel`. Universal because it can be used
in a terminal or in Emacs. I originally planned to add a windowed
version using GTK but never finished it.

The UTop prompt
---------------

The utop prompt looks much more blinky than the one of the default
toplevel. This is typically what you see when you start utop:

    ─( 16:36:52 )─< command 0 >───────────────────────{ counter: 0 }─
    utop #
    ┌───┬────────────┬─────┬───────────┬──────────────┬───────┬─────┐
    │Arg│Arith_status│Array│ArrayLabels│Assert_failure│Big_int│Bigar│
    └───┴────────────┴─────┴───────────┴──────────────┴───────┴─────┘

It displays:

- the time
- the command number
- the macro counter (for Emacs style macros)

The box at the bottom is for completion, which is described in the
next section.

If colors seems too bright you can type `#utop_prompt_fancy_light`,
which is better for light backgrounds. This can be set permanently by
adding this line to `~/.ocamlinit` or by adding `profile: light` to
`~/.utoprc`.

The prompt can be customized by the user, by setting the reference
`UTop.prompt`:

    utop # UTop.prompt;;
    - : LTerm_text.t React.signal ref = {contents = <abstr>}

`LTerm_text.t` is for styled text while `React.signal` means that it
is a reactive signal, from the
[react](http://erratique.ch/software/react) library. This make it very
easy to create a prompt where the time is updated every second for
instance.

Real-time completion
--------------------

This is the main feature that motivated writing UTop. UTop makes use
of the compiler internals to find possible completions on:

- function names
- function argument names
- constructor names
- record fields
- method names

Instead of the classic way of displaying a list of words when the user
press TAB, I chose to dynamically display the different
possibilities as the user types. This idea comes from the dmenu tool
from the [dwm](http://dwm.suckless.org/) window manager.

The possible completions are displayed in the completion bar below the
prompt. It is possible to navigate in the list by using the meta key
(`Alt` by default most of the time) and the left and right arrows. A
word can be selected by pressing the meta key and `TAB`. Also pressing
just `TAB` will insert the longest common prefix of all possibilities.

Syntax highlighting
-------------------

UTop can do basic syntax highlighting. This is disabled by default but
can be enabled by writing a `~/.utoprc` file. You can copy one from
the repository, either for
[dark background](https://github.com/diml/utop/blob/master/utoprc-dark)
or
[light background](https://github.com/diml/utop/blob/master/utoprc-light).

Emacs integration
-----------------

As said earlier UTop can be run in Emacs. Instruction to set this up
can be found in the [UTop's readme](https://github.com/diml/utop). The
default toplevel can too but UTop is better is these regards:

1. it provides context-sensitive completion
2. it behaves like a real shell, i.e. you cannot delete the prompt

They are several Emacs libraries for writing shell-like modes but I
wrote my own because with all of the ones I found it is possible to
insert or remove characters from the prompt, which really annoyed
me. Even with the mode used by the Emacs Shell mode it is
possible. AFAIK at the time I wrote it the UTop mode was the only one
where it was really impossible to edit the something in the _frozen_
part of the buffer.

Other features
--------------

This is a non-exhaustive list of features that have been added over
time to enhance the user experience. Some of them might be
controversial, I tried to choose what was the most asked for most of
the time:

- when using the [lwt](http://ocsigen.org/lwt/) or
  [async](https://github.com/janestreet/async) libraries, UTop will
  automatically wait for ['a Lwt.t] or ['a Deferred.t] values and
  return the ['a] instead
- made `-short-paths` the default. This option allow to display
  shorter types when using packed libraries such as
  [core](https://github.com/janestreet/core)
- hide identifiers starting with `_` to the user. This is for hidding
  the churn generated by syntax extensions. This can be disabled with
  `UTop.set_hide_reserved` or with the command line argument
  `-show-reserved`.
- automatically load `camlp4` when the user request a syntax
  extension. In the default toplevel one has to type `#camlp4` first.
- hide verbose messages from the findlib library manager
- add a `typeof` directive to show the type of modules, values, ...
- automatically load files from `$OCAML_TOPLEVEL_PATH/autoload` at
  startup
- allow to specify libraries to be loaded on the command line

Libraries that were developed on the side of UTop
-------------------------------------------------

For the need of UTop I wrote
[lambda-term](https://github.com/diml/lambda-term), which is kind of
an equivalent of ncurses+readline, but written in OCaml. It was
written because I disliked the ncurses API and I wanted something more
fancy than readline, especially for completion. In the end I claim
that it is much more fun to write terminal application in OCaml using
lambda-term than it is using ncurses.

The pure editing part is managed by the
[zed](https://github.com/diml/zed) library, which is independent from
the user interface.


UTop development
----------------

I don't have much time to spend on UTop these days, I'm mostly just
fixing bugs. Many thanks to Peter Zotov who recently joined the
project. Contributions are more than welcome and if you are interested
in enhancing UTop do not hesitate to interact with the UTop developers
through GitHub.
