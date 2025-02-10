# Easycrypt extraction

The Jasmin compiler is formally proven in Coq, this means that we have a formal proof ensuring that it preserves the semantics of safe programs. But how can we prove that a source Jasmin program satisfies properties that will be preserved by the compiler?
To that end, the compiler allows to extract Jasmin program to an equivalent EasyCrypt model. This extraction is correct on safe programs. 
## Command line for extraction
The command to extract EasyCrypt programs is the following: 

          jasminc -ec funname -oec ecFileName jasminFileName

* _funname_ is the name of the Jasmin function to extract, notice that if _funname_ uses other functions they will be extracted as well. It is possible to provide different functions to extract by adding more "-ec" arguments.
* _ecFileName_ is the EasyCrypt file that will be generated.
* _jasminFileName_ is the name of the Jasmin source file containing _funname_


## Extraction for checking cryptographic constant time.
It is possible to extract a EasyCrypt model allowing that a Jasmin program satisfies the cryptographic constant time property.

For this we need to add a -CT argument to the command line.

          jasminc -CT -ec funname -oec ecFileName jasminFileName

For more explanation on how to verify that a program is constant time, see the [Constant-time verification](ct) page.

## Configure EasyCrypt to verify Jasmin programs

EasyCrypt models of Jasmin programs may refer to some `JModel` or `JWord` modules.
These are part of the Jasmin library for EasyCrypt (to be found in the source code of Jasmin).

To instruct `easycrypt` how to find these modules,
either use the `-I Jasmin:/path/to/jasmin/easycrypt/` command-line arguments
(with the correct path of the directory containing the modules)
write a configuration file for EasyCrypt (usually in `~/.config/easycrypt/easycrypt.conf`) with the following contents
(with the correct path depending on your local installation of Jasmin):

~~~
[general]
idirs=Jasmin:~/.nix-profile/lib/jasmin/easycrypt/
~~~
