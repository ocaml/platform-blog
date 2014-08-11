title: "Announcing OPAM 1.2"
author: "Louis Gesbert" {"http://ocamlpro.com"}
date: "2014-08-12"
--BODY--

Six months after the last stable release, we are proud to announce the beta
release of OPAM 1.2. We've been very busy improving over 1.1, and 1.2 is packed
with new features, stability and usability improvements. Here is a short
overview.

## Better build

* No longer depends on ocp-build (or bytecode) or camlp4
* Uses newer versions of dependencies, esp. better Dose
* Simpler Makefile-based build. `./configure`, one rule to locally get and build
  external dependencies if needed (make lib-ext), one rule to build, one rule to
  install.

## Integrated development workflow

* "package pinning" on steroids
* One-command package creation, from a template
* Quickly and easily start hacking existing packages [opam pin --dev, opam source]

## Interface improvements

* New query commands [opam list [--depends-on|--required-by] [--recursive] [--external]; opam show --raw...]
* More expressive install queries, fixup
* Self-upgrade
* Speed

## More expressive metadata

* New opam file fields: 'bug-reports', 'flags', 'dev-repo', 'install' ; new url file field 'mirrors'. Parser now allows operator priorities
* Build dependencies
* opam-admin.top
* Better documented available variables [opam config list]

## Error handling and recovery

* Stability [fixes, exception handling, C-c, locks]
* Error messages [positions of errors in files, conflict messages (OWS), unavailable...]
* Backups, export/import
* Safer order of processing of actions and clearer reports
