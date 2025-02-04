# Compuilation Passes

Here are the different compilation passes performed by the Jasmin compiler.

 - [[Array-copy]] expands `#copy` operations into explicit `for` loops
 - [[Array-init]] inserts array-init instructions at the beginning of functions
 - [[Function inlining]] inlines calls to `inline` functions and some other
 - [[Removal of unused functions]] removes `inline` functions that are never called
 - [[Loop unrolling]]
 - [[Constant propagation]]
 - [[Dead-code elimination]]
 - [[Renaming]] renames variables to remove copies added in the previous passes (inlining, unrolling)
 - [[Array-init]] removes array-init instructions
 - [[Expansion of register arrays]] makes each cell of reg arrays an independent variable
 - [[Remove global]] turn `global` local variables into proper global values
 - [[Make ref arguments]] inserts `ptr` intermediate variables for function arguments and returned values
 - [[Instruction selection]] aka lowering
 - [[Propagate inline]] propagation of expressions made of flags
 - [[Stack allocation]]
 - [[Remove unused returned values]] changes the signature of functions to return less values
 - [[Register allocation]] solves NP-hard problems in polynomial time, might fail unexpectedly
 - [[Merge varmaps]] check that compilation can proceed
 - [[Linearization]] explicit code for handling the stack and function arguments, labels & gotos
 - [[Tunelling]]
 - [[ASM generation]]
