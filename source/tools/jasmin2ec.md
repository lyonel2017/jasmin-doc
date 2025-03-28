# Easycrypt extraction

The Jasmin compiler is formally proven in Coq, this means that we have a formal proof ensuring that it preserves the semantics of safe programs. But how can we prove that a source Jasmin program satisfies properties that will be preserved by the compiler?
To that end, the compiler allows to extract Jasmin program to an equivalent EasyCrypt model. This extraction is correct on [safe programs](safety_checker).

## Command line for extraction

The command to extract EasyCrypt programs is the following: 

```
jasmin2ec -o extracted.ec source.jazz
```

By default, `jasmin2ec` will extract all the functions in a file, however a
(set of) specific function(s) can be extract using the `--function`
command-line parameter one of multiple times (this also extracts the functions
called the function to be extracted).

When no output file is specified, the extracted model is printed (but auxiliary
theories are not saved, so the model might not run through EasyCrypt
successfully).

The `--output-array` parameter allows to specify a separate directory to store
the generated auxiliary EasyCrypt theories. By default this is the same
directory as the directory of the output file.

By specifying `--model=CT`, the extracted EasyCrypt model allows to verify that
the Jasmin program satisfies the cryptographic constant time property, as an alternative to `jasmin-ct`.
For more explanation on how to verify that a program is constant time, see the [Constant-time verification](ct) page.

For other parameters, see `jasmin2ec --help`.

## Configure EasyCrypt to verify Jasmin programs

EasyCrypt models of Jasmin programs may refer to modules such as `JModel` or `JWord`
These are part of the Jasmin library for EasyCrypt (to be found in the source code of Jasmin).

To instruct `easycrypt` how to find these modules,
set the `JASMINPATH=Jasmin=/path/to/jasmin/easycrypt/` environment variable
(with the correct path of the directory containing the modules).

You may also write a configuration file for EasyCrypt (usually in
`~/.config/easycrypt/easycrypt.conf`) or an `easycrypt.projct` (in the
directory where you run EasyCrypt) with the following contents (with the
correct path depending on your local installation of Jasmin):
~~~
[general]
idirs=Jasmin:~/.nix-profile/lib/jasmin/easycrypt/
~~~

> Note: modules such as `JBArray8` are generated by `jasmin2ec`, they are not part of the library.

