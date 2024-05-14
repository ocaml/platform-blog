title: "opam 2.2.0~beta2 - Windows OCaml"
authors: [
  "Raja Boujbel - OCamlPro" {"mailto:raja.boujbel(à)ocamlpro.com"}
  "Kate Deplaix - Ahrefs" {"mailto:kit-ty-kate(à)outlook.com"}
  "David Allsopp - Tarides" {"mailto:david(à)tarides.com"}
]
date: "2024-05-13"
--BODY--

_Feedback on this post is welcomed on [Discuss](??)!_

Almost exactly 8 years ago, in a small village to the north of Cambridge, a Dell Precision 5510 laptop running an experimental build of opam just recently re-versioned from 1.3~dev7 to 2.0~alpha, crunched its way through the creation of 80 compiler switches and verified that the native Windows builds of OCaml for 3.07-4.03 all passed a battery of compilation tests. Time to upstream it.

They say good things come to those that wait. Many PRs have gone into [ocaml/ocaml](https://github.com/ocaml/ocaml), [ocaml/opam](https://github.com/ocaml/opam) and [ocaml/opam-repository](https://github.com/ocaml/opam-repository) gradually laying the way for the actual compiler packages. Even the ability to bootstrap FlexDLL itself in [ocaml/ocaml#388](https://github.com/ocaml/ocaml/pull/388) in January 2016 already mentions opam as a motivation! Quite a few PRs since then ([#462](https://github.com/ocaml/ocaml/pull/462), [#678](https://github.com/ocaml/ocaml/pull/678), [#1535](https://github.com/ocaml/ocaml/pull/1535), [#1035](https://github.com/ocaml/ocaml/pull/10135), [#10926](https://github.com/ocaml/ocaml/pull/10926), ...)

Although this is hopefully the end of a very long journey from prototype to upstreamable candidate, it is the start of a new journey to widen opam-repository's Windows support further and this post covers both how you can try this out, what's still to be done and how you can help!

# Trying it out

## Getting opam 2.2.0~beta2

**Please beware that the new compiler packages require opam 2.2.0~beta2 or later. They do not - and never will - work with the opam 2.0.10 binary in OCaml for Windows.**

The easiest way to try out the new compiler packages is to use `winget` to install opam 2.2.0~beta2. [winget](https://learn.microsoft.com/en-us/windows/package-manager/winget/) is installed by default on Windows 11 and recent editions of Windows 10, but if you don't have it already, the easiest way to get it is to install the somewhat confusingly named [App Installer](https://apps.microsoft.com/detail/9nblggh4nns1) from Microsoft, available from the Windows Store app. A shout-out to [@jonahbeckford](https://github.com/jonahbeckford) at [Diskuv](https://diskuv.com/) for maintaining the winget package for opam - thanks!

You will need Git in order to try this out, and we _strongly recommend_ using [Git for Windows](https://gitforwindows.org/) for this. If you don't already have it, installers are available on the website, or you can install it via winget with `winget install Git.Git`

You can then issue `winget install opam`. **You must then launch a fresh Command Prompt / PowerShell session**.

## Initialising opam

If you are already using opam on Windows (yay!), you may wish to try this with a separate "opam root". You can do this in the current session by issuing:

```
set OPAMROOT=%LOCALAPPDATA%\opam-testing
```

or, for PowerShell:

```
$env:OPAMROOT = "$env:LOCALAPPDATA\opam-testing"
```

Remember that this only affects your current session.

At this point, you're good to run:

```
opam init git+https://github.com/dra27/opam-repository.git#windows-initial
```

`opam init` will present you with a lot of information! Unless you've already enabled it, opam will recommend turning on [Developer Mode](https://learn.microsoft.com/en-gb/windows/apps/get-started/enable-your-device-for-development). This helps at the moment, for example, with installing packages like ocamlbuild where the tarball contains symlinks. This will be fixed, but in the meantime, if you're able to enable Developer Mode, more things are likely to work (including several of the examples later in this post).

opam will then ask which Git you would like to use. Assuming you have dutifully installed Git for Windows, you may be surprised at being asked this! This should be fixed in beta3 (see [ocaml/opam#5835](https://github.com/ocaml/opam/issues/5835)) but in the meantime, from:

```
Which Git should opam use?
> 1. Use default Cygwin Git
  2. Use found git in C:\Program Files\Git\cmd
  3. Enter the location of installed Git
  4. Abort initialisation to install recommended Git.
```

select option 2!

opam packages - especially OCaml itself, need a Unix-like environment in order to be built. We support this being provided either by [Cygwin](https://cygwin.com) or by [MSYS2](https://www.msys2.org). opam will therefore then ask about _Unix support infrastructure_:

```
How should opam handle Cygwin?
> 1. Automatically create an internal Cygwin installation that will be managed by opam (recommended)
  2. Enter the location of an existing Cygwin installation
  3. Abort initialisation
```

Unless you _really know what you are doing_ we strongly recommend selecting the first option and allowing opam to manage an _internal_ installation of Cygwin. We'll follow-up with details about how to configure native Windows opam with external Cygwin and MSYS2 installations another time (especially as this is likely to be improved slightly with beta3).

Assuming you selected option 1, you should then see Cygwin's installer running in unattended mode. You should then get to:

```
<><> Fetching repository information ><><><><><><><><><><><><><><><><><><><>  🐫
[default] Initialised
```

**Please be aware that opam may sit here for a VERY long time: it's not crashed!** (see [ocaml/opam#5741](https://github.com/ocaml/opam/issues/5741))

Having patiently waited, you should finally be presented with the usual shell configuration question(s).

opam will then start building OCaml! You'll see Cygwin's setup popping up again as it adds GCC to the internal installation, then opam will spend a little time in _Processing actions_.

Finally, opam will prompt you to run the Windows equivalent of `eval $(opam env)` which is either:

```
for /f "tokens=*" %i in ('opam env') do @%i
```

or, for PowerShell:

```
(& opam env) -split '\r?\n' | ForEach-Object { Invoke-Expression $_ }
```

and you should then be able to run `ocaml` and see: 🎆🥳🎉

```
OCaml version 5.1.1
Enter #help;; for help.

#
```

Note that once [ocaml/opam-repository#25861](https://github.com/ocaml/opam-repository/pull/25861) has been merged, the URL will no longer be required (and you'll be able to run `opam repo set-url default https://opam.ocaml.org` to switch back to opam-repository on any existing installation).

## Microsoft Visual Studio

So far, so good. What about the Microsoft Visual Studio (MSVC) version of OCaml? If you've already installed [Visual Studio](https://visualstudio.microsoft.com/), then as long as you have installed the MSVC x64/x86 build tools, Windows 11 SDK and C++/CLI support for build tools individual components, you should be good to go. Visual Studio Community is freely available [from Microsoft](https://visualstudio.microsoft.com/downloads/). You can also install Visual Studio Build Tools with:

```
winget install Microsoft.VisualStudio.2022.BuildTools --override "--add Microsoft.VisualStudio.Workload.VCTools --includeRecommended --passive"
```

which installs all the required components.

Once you've done that, from whichever terminal you were **already** using opam in (_you do not need to use Visual Studio Tools command prompts to use MSVC with opam_), you can now run:

```
opam switch create 4.14.2-msvc32 arch-x86_32 system-msvc ocaml.4.14.2
```

At the end of the process, opam will of course invite you to run the appropriate environment-updating rune. If you run `ocaml` this time then you should be presented with a 4.14.2 toplevel but also note that you can run `cl` - i.e. the Microsoft C compiler and linker have been automatically set-up in the environment by opam.

## Selecting other ports

The packaging allows all four ports to be selected at switch creation just by adding an `arch-` package and a `system-` package to the switch. You can see that at `opam init`, opam simply added `arch-x86_64` and `system-mingw` as the default packages. You can therefore add a mingw32 build of OCaml 4.14.2 with:

```
opam switch create 4.14.2-mingw32 arch-x86_32 system-mingw ocaml.4.14.2
```

and opam will automatically add the 32-bit GCC compiler to the internal Cygwin installation and build a 32-bit OCaml 4.14.2 using it.

# Installing other packages

The focus of this work has been on getting the OCaml compiler packages themselves upgraded, which has required a considerable number of iterations and new features in opam itself to create an experience that we think is consistent with the Unix experience of opam. While the framework is now laid out, there's still a lot more work to do, but here are some pretty cool things which are already correct and working.

## Compiling opam itself

If you're using the 4.14.2 mingw32 switch created earlier, you can issue:

```
opam install opam-devel.2.2.0~beta2
```

but here's a good example of where we can use help. Help can be opening issues either on [ocaml/opam](https://github.com/ocaml/opam/issues) or [ocaml/opam-repository](https://github.com/ocaml/opam-repository/issues) or even pull requests fixing things, but issues are _very_ useful!

`opam-devel` at present installs correctly in a 4.14.2 mingw-w64 switch, but:
- Windows opam doesn't build in OCaml 5 yet owing to C API changes which should be fixed before release.
- The C++ packaging in conf-c++ is incorrect for the Visual Studio ports, so you can't install this package using MSVC (opam does in fact build with MSVC, especially as it's how @dra27 develops opam!)
- ocamlbuild's tarball contains symlinks and so requires Developer Mode.

Those are things we already know, but there are going to be a bunch of things we don't know! Of course, knowing is only the first stage; they do then need fixing, too 🙂

## Writing something new!

If you don't already have it, you can install Visual Studio Code with `winget install Microsoft.VisualStudioCode` (remember to start a fresh terminal, set `OPAMROOT` again, if you did that before, and run the appropriate `eval $(opam env)` rune which `opam switch` will remind you about)! Make sure you've installed the [`ocamllabs.ocaml-platform`](https://marketplace.visualstudio.com/items?itemName=ocamllabs.ocaml-platform) extension in Visual Studio Code.

Switch back to the 5.1.1 switch opam initially created with `opam switch default` and issue `opam install ocaml-lsp-server` and create that all-important "Hello, world" program with either

```
echo print_endline "Hello, world"> hello.ml
```

or, for PowerShell:

```
echo 'print_endline "Hello, world"' > hello.ml
```

Now run `code hello.ml`. Hovering over either the `print_endline` or the string should reveal tooltips with their types. Apart from the fact it works, note that the instructions are _exactly_ as for Unix as given on [the OCaml website](https://ocaml.org/docs/configuring-your-editor) 🥳

## External Dependencies ("depexts")

OK, now we move into areas which are much more a work-in-progress. [opam-repository-mingw](https://github.com/ocaml-opam/opam-repository-mingw) which underpins the "OCaml for Windows" system that has been being used as a very long-lived stop-gap while [ocaml/opam-repository](https://github.com/ocaml/opam-repository) lacked official Windows support is based on opam 2.0.10.

One of the biggest differences between opam 2.0 and opam 2.1 is that the external dependency support in opam 2.0 is a plugin where in opam 2.1 it is integrated. When external dependencies are first required in a switch in OCaml for Windows, therefore, the plugin already knows which version of OCaml is installed which, critically, means it can select between the 32- and 64-bit versions of the packages. In opam 2.1, external dependency analysis also takes place at switch creation, potentially before the compiler has been built.

Figuring out how to implement this with opam 2.1's integrated depext has been a very large part of the behind-the-scenes work for opam 2.2, and there are still many more packaging changes needed in opam-repository before everything "just works".

Here is a worked example trying to install the `ssl` package in OCaml 5.1.1.

Let's start off with `opam install ssl`:

```
C:\>opam install ssl
The following actions will be performed:
=== install 4 packages
  ∗ conf-libssl       4      [required by ssl]
  ∗ conf-pkg-config   3      [required by conf-libssl]
  ∗ dune-configurator 3.15.2 [required by ssl]
  ∗ ssl               0.7.0

Proceed with ∗ 4 installations? [y/n] y

The following system packages will first need to be installed:
    pkgconf

<><> Handling external dependencies <><><><><><><><><><><><><><><><><><><><>  🐫
```

so far, so good - the `pkg-config` package has been upgraded as part of the work already done in opam-repository and opam will install the required dependencies to the internal installation. Things then stop going well:

```
<><> Processing actions <><><><><><><><><><><><><><><><><><><><><><><><><><>  🐫
∗ installed conf-pkg-config.3

#=== ERROR while compiling conf-libssl.4 ======================================#
"pkg-config": command not found.


<><> Error report <><><><><><><><><><><><><><><><><><><><><><><><><><><><><>  🐫
┌─ The following actions failed
│ ⬇ fetch dune-configurator 3.15.2
│ ⬇ fetch ssl               0.7.0
│ λ build conf-libssl       4
```

`conf-libssl` has failed to install. This is because it has not yet been updated to work with the new depext layout. The commit for this is on @dra27's fork of opam-repository [in branch windows-initial-conf-libssl](https://github.com/dra27/opam-repository/commit/windows-initial-conf-libssl). It:
- Disables all the old versions for Windows. Note the formula `available: os != "win32" | os-distribution = "cygwinports"` which keeps the package available for either non-Windows users _or_ for users of OCaml for Windows (where `os-distribution` has the value `cygwinports`)
- Alters `conf-libssl.4` to:
  - Call the correct `pkg-config` command (which is `pkgconf --personality=`)
  - Depend on one of two new packages `conf-mingw-w64-openssl-i686` or `conf-mingw-w64-openssl-x86_64`
- Adds two new packages containing the depext package names for Cygwin and MSYS2 for the openssl package

So we can use that in our switch with:

```
opam repo add conf-libssl --rank=1 git+https://github.com/dra27/opam-repository.git#windows-initial-conf-libssl
```

We can re-try `conf-libssl` with `opam install conf-libssl` and this time the installation succeeds.

So now we can try install ssl again:

```
opam install ssl

...

#=== ERROR while compiling ssl.0.7.0 ==========================================#
# context     2.2.0~beta2 | win32/x86_64 | ocaml.5.1.1 | git+https://github.com/dra27/opam-repository.git#windows-initial-conf-libssl
# path        ~\AppData\Local\opam\default\.opam-switch\build\ssl.0.7.0
# command     ~\AppData\Local\opam\default\bin\dune.exe build -p ssl -j 15 @install
# exit-code   1
# env-file    ~\AppData\Local\opam\log\ssl-5164-514317.env
# output-file ~\AppData\Local\opam\log\ssl-5164-514317.out
### output ###
# ssl_stubs.c:1321:3: warning: implicit declaration of function 'ASN1_TIME_to_tm'; did you mean 'ASN1_TIME_set'? [-Wimplicit-function-declaration]
# [...]
#       |   ^~~~~~~~~~~~~~~
#       |   ASN1_TIME_set
# ssl_stubs.c:1321:19: warning: implicit declaration of function 'X509_get0_notBefore'; did you mean 'X509_get_notBefore'? [-Wimplicit-function-declaration]
#  1321 |   ASN1_TIME_to_tm(X509_get0_notBefore(cert), &t);
#       |                   ^~~~~~~~~~~~~~~~~~~
#       |                   X509_get_notBefore
# ssl_stubs.c: In function 'ocaml_ssl_get_expiration_date':
# ssl_stubs.c:1333:19: warning: implicit declaration of function 'X509_get0_notAfter'; did you mean 'X509_get_notAfter'? [-Wimplicit-function-declaration]
#  1333 |   ASN1_TIME_to_tm(X509_get0_notAfter(cert), &t);
#       |                   ^~~~~~~~~~~~~~~~~~
#       |                   X509_get_notAfter



<><> Error report <><><><><><><><><><><><><><><><><><><><><><><><><><><><><>  🐫
┌─ The following actions failed
│ λ build ssl 0.7.0
└─
```

so we're still not there!

At this point, it's worth being sure that the target is achievable - was this package previously unavailable in opam-repository-mingw. The latest version there is ssl.0.5.13, so let's try explicitly installing that:

```
opam install ssl.0.5.13
```

Aha! That works. So what's wrong with ssl.0.7.0? It turns out the issue here is Cygwin - for whatever reason, its [mingw64-x86_64-openssl](https://cygwin.com/packages/summary/mingw64-x86_64-openssl.html) package is held at 1.0.2 and the recent ssl packages expect a newer version of libssl. We can cheekily work around this using the setup program for Cygwin in the opam root (it's in `%OPAMROOT%\.cygwin` or `%LOCALAPPDATA%\opam\.cygwin` ) to do a manual installation of the test release.

The takeaway here is that there are various things which can be done and various other things to upstream - please do try things out; please do report issues!

# Where's OCaml 4.12 and earlier?

At the moment, only OCaml 4.13 and onwards are available. The reason for this is that 4.13.0 is the first release of OCaml including [ocaml/ocaml#10135](https://github.com/ocaml/ocaml/pull/10135), part of which is a necessary stability fix for the compilation of FlexDLL.

Extending this support back to OCaml 4.08 is a high priority, but is not likely to happen until the autumn. There is no timeline at present for dealing with OCaml 4.07 and older, although it is worth remembering that @dra27 has form at restoring old versions of OCaml (cf. [ocaml/opam-repository#18240](https://github.com/ocaml/opam-repository/pull/18240) and [metastack/ocaml-legacy](https://github.com/metastack/ocaml-legacy) 🤓).
