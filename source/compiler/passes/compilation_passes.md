# Compilation Passes

Here are the different compilation passes performed by the Jasmin compiler.

 - [Array-copy](array_copy) expands `#copy` operations into explicit `for` loops
 - [Array-init](array_init) inserts array-init instructions at the beginning of functions
 - [Function inlining](function_inlining) inlines calls to `inline` functions and some other
 - [Removal of unused functions](removal_unused_functions)
   removes `inline` functions that are never called
 - [Loop unrolling](loop_unrolling)
 - [Constant propagation](constant_propagation)
 - [Dead-code elimination](deadcode_elimination)
 - [Renaming](renaming) renames variables to remove copies added
   in the previous passes (inlining, unrolling)
 - [Array-init](array_init_rm) removes array-init instructions
 - [Expansion of register arrays](expansion_reg_arrays)
   makes each cell of reg arrays an independent variable
 - [Remove global](remove_global) turn `global` local variables into proper global values
 - [Make ref arguments](make_ref_arguments)
   inserts `ptr` intermediate variables for function arguments and returned values
 - [Instruction selection](inst_select) aka lowering
 - [Propagate inline](propagate_inline) propagation of expressions made of flags
 - [Stack allocation](stack_alloc)
 - [Remove unused returned values](rm_unused_ret_value)
   changes the signature of functions to return less values
 - [Register allocation](reg_alloc)
   solves NP-hard problems in polynomial time, might fail unexpectedly
 - [Merge varmaps](merge_varmaps) check that compilation can proceed
 - [Linearization](linearization)
   explicit code for handling the stack and function arguments, labels & gotos
 - [Tunelling](tunelling)
 - [ASM generation](asm_gen)
