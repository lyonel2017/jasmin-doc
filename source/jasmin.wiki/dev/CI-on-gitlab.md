# Continuous Integration on gitlab.com

The repository at <https://gitlab.com/jasmin-lang/jasmin> is a mirror of the
main repository hosted on <https://github.com>. Its purpose is to enable the
continuous integration pipeline described in the `.gitlab-ci.yml` configuration
file.

## CI Targets

| Target name        | Body          | Goal  |
| ------------------ | ------------- | ----- |
| coq | compiles all the Coq files, calls the extraction and puts the resulting `.ml{,i}` files in subdirectory `CIL/` in `compiler/`      | checks that all the `.v` files compile and that extraction works |
| ocaml     | compiles the Jasmin compiler based on what was done by target `coq`      | checks that we can indeed produce the executable `jasminc` |
| check | calls the compiler produced by `ocaml` on the tests contained in directories `tests/` and `examples/`     | checks that we have no regression in Jasmin from the user point of view |
| cryptolib | calls the compiler produced by `ocaml` on the programs implemented in cryptolib | same purpose as `check`, but on larger and real programs |
| eclib | calls EasyCrypt on the files in sub-directory `eclib/` | checks that we write valid EasyCrypt code in the files in `eclib/` |
| opam | installs the dependencies of Jasmin using opam, compiles Jasmin in this environment and produces an artefact | checks that the opam file is correct |
| tarball | creates a tarball using `make dist` and pushes it as an artefact | produces a nice tarball to distribute, and checks that the distribution script works |
| push-compiler-code | pushes the OCaml code produced by `tarball` to another repository | this allows to have a repo with OCaml-only code, no Coq, that can be helpful both to give as pointer to the users and to debug some bugs |

## Dependency cache on cachix.org

Some of the build dependencies are distributed as source code.  Some pre-built
binaries are available from `cache.nixos.org`. Other dependencies are built
during the first step of the pipeline and the results are stored on an online
public cache described at <https://jasmin.cachix.org>.

A private signing key is required to write to this cache. It is stored as a
protected and masked variable, restricted to the `cachix` environment.  If
needed, the cache can be emptied or destroyed.  If the secret key needs to be
rotated, just create a fresh cache and update the value of the variable to the
new key.  (Using `gitlab.com` web interface, navigate to “Settings” → “CI / CD”
→ “Variables”.)

The corresponding public keys are written in the `.gitlab-ci.yml` file.

## The OCaml-only `jasmin-compiler` git repository

At the end of successful pipelines for protected branches, the OCaml source code
(included the part extracted from Coq files) is committed to the
<https://gitlab.com/jasmin-lang/jasmin-compiler/> git repository.

There is a SSH key-pair allowing the CI machine to write to that repository. The
private key is held in a protected variable of the `jasmin-lang/jasmin`
repository on `gitlab.com`; said variable is restricted to the `deployment`
environment. The public key is set as a “deploy key” with write access for the
`jasmin-lang/jasmin-compiler` repository.

This key-pair has no other use (so it can be easily rotated).
