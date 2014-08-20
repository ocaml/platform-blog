title: "Turn your editor into a full fledged OCaml IDE"
authors: "Frederic Bour" {"https://github.com/def-lkb"}
date: "2014-08-20"
--BODY--

This post is short presentation of a couple of tools you can use with your
editor to have a smoother experience while developing in OCaml.

At the time of writing, an interface to these tools is only available
for Emacs and Vim.  However, using the tools from other editors is definitely
possible and some efforts exist for
[Acme](https://github.com/raphael-proust/merlin-acme) and
[Sublime Text 3](https://github.com/def-lkb/sublime-text-merlin).

# Overview

One of these tools, [ocp-indent](http://www.typerex.org/ocp-indent.html),
handles the task of indenting your OCaml files.  It is an OCaml executable that
can be used from the command line or directly from your editor.

The other is [merlin](http://the-lambda-church.github.io/merlin/) and runs some
"static analysis" on your file.  This translates into error reporting, source
browsing, auto-completion and more.

## Ocp-indent for indentation

Most editors provide some kind of indentation "out of the box".
However recently a good chunk of the OCaml community has moved to using
ocp-indent for fine-tuned indentation:

- it follows language evolution closely, nicely handling recent features,
- it will indent the same even if your co-worker has a different editor,
- it is more flexible, for instance by enabling project-specific style;

Indeed the indentation behaviour of ocp-indent can be configured through several
options, directing how it will behave when encountering different constructions
of the OCaml language.  These options can be set in a configuration file or
directly as parameters when invoked from the command line.  An example
configuration file is available
[here](https://github.com/OCamlPro/ocp-indent/blob/master/.ocp-indent).

Finally, ocp-indent can also recognize some common syntax extensions of the
OCaml ecosystem and will keep a meaningful indentation in presence of these,
while your editor probably will not.

## Merlin for analysis

Merlin will enhance your experience editing OCaml code by providing interactive
feedback about your code.

Under the hood, it maintains a "code model" of the file you are editing.  For
other files in your project, it will use the output produced by the compiler;
rebuild regularly after edition to keep it synchronized.

From this code model, it provides a lot of useful features: 

- scope/context-aware completion, like IntelliSense;
- querying the type of any expression in the file;
- quick reporting of type and syntax errors, for short editing cycle;
- jumping to definition;
- list usages of identifiers in current buffer.

# Quick start

Assuming opam is already installed on your system, you just need to invoke

    $ opam install ocp-indent merlin

to install these two tools.

**Emacs.** You will have to add `opam/share` to `'load-path` and load plugin
specific file.  This can be done
by adding the following lines to your `.emacs`:

```lisp
(setq opam-share (substring (shell-command-to-string "opam config var share 2> /dev/null") 0 -1))
(add-to-list 'load-path (concat opam-share "/emacs/site-lisp"))

(require 'ocp-indent)
(require 'merlin)
```

For more information about merlin setup, you can look at
[the dedicated wiki](https://github.com/the-lambda-church/merlin/wiki).

**Vim & ocp-indent.**  We recommend using the
[ocp-indent-vim](https://github.com/def-lkb/ocp-indent-vim) plugin instead of
the default one. It provides interactive indentation "as you type", while the
official mode only provides an indenting function to call manually but
no passive indentation.

This mode does require vim to be compiled with Python support, while the
official one doesn't.

Installing is as simple as cloning
[ocp-indent-vim](https://github.com/def-lkb/ocp-indent-vim) and adding the
directory to your runtime-path.

Assuming your clone is in `~/my-clone-of/ocp-indent-vim`, add this to `.vimrc`:

```viml
set rtp+=~/my-clone-of/ocp-indent-vim
```

**Vim & merlin.**  A comprehensive guide to the installation procedure for
merlin is available on [the dedicated
wiki](https://github.com/the-lambda-church/merlin/wiki).  Once again if you
just want to get started, the following lines are enough.

Loading merlin in vim boils down to adding plugin directory in the
runtime-path. However as merlin depends on your current opam switch, a more
flexible way is to find the current switch and use it as base directory.

This code does exactly that: find current opam share directory, then add
merlin plugin directory. Add it to your `.vimrc`:

```viml
let g:opamshare = substitute(system('opam config var share'),'\n$','','''')
execute "set rtp+=" . g:opamshare . "/merlin/vim"
```

## Integrating with your project

To maintain synchronization with the compiler, merlin needs some information
about the structure of your project: build and source directories, package
dependencies, syntax extensions. 
You can see merlin's .merlin [here](https://github.com/the-lambda-church/merlin/blob/master/.merlin).

You have to put it at the root directory of your project, and the .merlin will be loaded next time you open an OCaml file in the editor.

To benefit of code navigation across files you'll also need to generate "cmt" files. Pass the `-bin-annot` flag to the compiler. 
