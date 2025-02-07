# Expressions

At source level, Jasmin program contain expressions whose concrete syntax is described below.

Expressions are made of:

  - constants (`true`, `false`,  `42`, `0xabcd`);
  - packs (`(4u2)[0, 3, 2, 1]`);
  - variables (`x`);
  - parenthesized subexpressions (`(e)`);
  - memory loads (`(u16)[p + 2 * i]`);
  - array accesses (`x[i]`);
  - unary operators (`- e`);
  - binary operators (`e - f`);
  - conditional expressions (`c ? th : el`);
  - function calls (`f(x, y)`);
  - primitive instructions `(#copy_32(t)`).

## Operators

Unary operators are, by decreasing precedence:

  - zero- or sign-extension (`(16s)e`);
  - boolean / bitwise negation (`! b`);
  - opposite (`- e`).

Binary operators are, by decreasing precedence:

  - multiplication (`x * y`), division (`x / y`), and remainder (`x % y`);
  - addition (`x + y`) and subtraction (`x - y`);
  - shifts (`x << y`; `x >>s y`) and rotations (`x <<r y`; `x >>r y`);
  - bitwise and (`x & y`);
  - bitwise exclusive or (`x ^ y`);
  - bitwise or (`x | y`);
  - comparisons (`x < y`; `x >= y`);
  - equality test (`x == y`; `x != y`);
  - boolean conjunction (`b && c`);
  - boolean disjunction (`b || c`).

Note that most operators accept as suffix a size annotation. For instance `+32u` is the 32-bit (unsigned) addition and `+8u16` is the parallel 16-bit (unsigned) addition for vectors of 8 elements (i.e., 128 bit). Said annotation can be limited to a sign annotation, for instance `>>u` is the _logical_ right shift whereas `>>s` is the _arithmetic_ right shift.
