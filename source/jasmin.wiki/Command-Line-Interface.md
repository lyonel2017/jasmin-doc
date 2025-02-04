# Compile a Jasmin library to assembly

  - Output to the terminal

        jasminc -pasm source.jazz

  - Output to a file `target.s`

        jasminc -o target.s source.jazz

See also: [[Compilation to assembly]]

# Verify the safety of a Jasmin library

    jasminc -checksafety source.jazz

See also: [[Safety checker]]

# Generate an EasyCrypt model for functional verification of a Jasmin function

    jasminc -ec f -oec model.ec source.jazz

See also: [[Extraction to EasyCrypt]]

# Generate an EasyCrypt model for constant-time verification of a Jasmin function

    jasminc -CT -ec f -oec model.ec source.jazz

See also: [[Extraction for checking cryptographic constant time|Extraction to EasyCrypt#extraction-for-checking-cryptographic-constant-time]]
