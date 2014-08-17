This repo holds the blog posts on http://opam.ocaml.org/blog

Files should be markdown (omd) with a preamble using the following format:

    ```
    title: "Introducing the OCaml Platform"
    authors: [ "Amir Chaudhry" {"http://amirchaudhry.com/"} ]
    date: "2014-08-09 16:29 UTC+0200"
    --BODY--
    ```

Publication is done by the scripts at https://github.com/ocaml/opam.ocaml.org (a private repo).
Manually, as user `opam` on the server, you can simply run
`~/local/bin/update-opam-repo.sh` to update.

Log at http://opam.ocaml.org/lastlog.txt
