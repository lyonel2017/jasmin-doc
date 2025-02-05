# Wish List

## Arrays as argument of export functions

  - Particularly useful for stateful computations split in several functions (init/update/finalize)
  - Can be implemented similarly as for internal functions
  - Can be emulated with plain memory accesses, but without the convenience of arrays

## Position-independent code

  - Can a Jasmin program be compiled as a shared object and then dynamically linked? Maybe. Bug reports welcome.

## Arrays iterators (floating arrays?)

  - Currently, to traverse an array in a `while` loop, a base pointer (the array) and an index must be kept in two registers.
  - It would save a register to be able to directly increment the base pointer (and compare it with a pointer to the end of the slice).
  - Packing two or four such base pointers in a `u128` or `u256` variable could make it possible to emit `VPGATHERxx` instructions.