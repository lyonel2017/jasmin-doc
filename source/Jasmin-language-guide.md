Jasmin is a low-level language: it is essentially assembly with variables and
structured control flow.
It should be thought of as a tool to help you write assembly: programmers use
Jasmin to produce specific assembly code in a convenient, safe and secure
manner.
This means that there are special considerations for each architecture.
You can read these for the [[x86-64|x86-64]] and the [[ARMv7-M|ARMv7-M]] target
architectures.


A Jasmin program is list of:

- requires;
- parameters;
- global variables; and
- function definitions.


# Requires

```
require <string>
```
```
require <string> ... <string>
```
```
from <ident> require <string> ... <string>
```

A program may be split into several files using a `require` clause.
Its simplest form is as follows
```
require "path/file.jinc"
```
where relative paths are resolved relative to the current file.
The required file is treated (mostly) as if its contents were put in place of
the `require` clause.
TODO: Why mostly?

Files can also be searched in named locations, for instance
```
from PATH require "file.jinc"
```
where the file `file.jinc` is located in a path named `PATH`.

There are two ways to give names to paths in the file-system:

1. On the command-line, using the argument `-I PATH:actual/path/`; and
1. Using the `JASMINPATH` environment variable, as in
`$ export JASMINPATH=PATH=actual/path/`.

If several paths must be named, the `-I` argument can be used multiple times,
```
-I path1:actual/path1/ -I path2:actual/path2/
```
and in the environment variable value, several `ID=path` pairs can be separated
by a colon
```
$ export JASMINPATH=path1=actual/path1/:path2=actual/path2/
```

Both `require` clauses can take several arguments, as in
```
require "path/file1.jinc" "path/file2.jinc" "path/file3.jinc"
```
and
```
from PATH require "file1.jinc" "file2.jinc" "file3.jinc"
```

See [[the require section|Syntax-reference#requires]] for the full syntax.


# Parameters

```
param <type> <ident> = <expr>;
```

A parameter is a named value.
They can be used in code and also within types (e.g., as the size of an array).
As such, they provide a limited form of genericity.
Parameters are removed by the compiler right after parsing, and therefore, they
have no formal semantics.

A parameter is introduced by the `param` keyword, followed by a type, a name,
and the value.
For instance
```
param int cROUNDS = 2;
```

See [[the parameters section|Syntax-reference#parameters]] for the full syntax.


# Global variables

Global variables are named runtime values.
Unlike parameters, said values are not available at compile-time (i.e., they
cannot be used as part of a type), but are available at run-time.
Specifically, global variables are stored in the code segment and are accessed
to using PC-relative addressing mode.

There is no keyword to introduce a global variable at the top-level: a
declaration consists only of its type, name, and value.
For instance,
```
u64[4] a = { 0, 1, 2, 3 };
```
declares an array `a` of four 64-bit values (0, 1, 2, and 3).

In this example
```
u128 pattern = (16u8)[ 12, 15, 14, 13, 8, 11, 10, 9, 4, 7, 6, 5, 0, 3, 2, 1 ];
```
the value is a 128-bit machine-word, described as a vector of 16 8-bit
machine-words.

See [[the global variables section|Syntax-reference#global-variables]] for the
full syntax.


# Function declarations

Functions in Jasmin are labeled pieces of code.
They are declared using the `fn` keyword, have a signature (the types of their
input and output) and a body.
This is an example function:
```
export
fn add(reg u64 x, reg u64 y) -> reg u64 {
  reg u64 r;
  r = x;
  r += y;
  return r;
}
```

See [[the functions section|Syntax-reference#funcitons]] for the full syntax.

Function definitions can be prefixed with either the `inline` or `export`
keywords to denote, respectively, that the function will be inlined by the
compiler or that it will respect a specified calling convention.
Functions that are not marked with `inline` or `export` are local functions:
they will be preserved by the compiler but are not visible from the outside
(i.e., they cannot be called from non-Jasmin code).
TODO: Add link to locality syntax.

Function definitions can also be prefixed by annotations.
Annotations are key-value pairs that customize the behaviour of the compiler,
for instance to store the return address of a function in a register or in the
stack.
For instance,
```
#[returnaddress=stack]
fn copy_to_memory(reg u64 x, reg u64 p) {
    [p + 8] = x;
}
```
is a local function that copies the contents of a register `x` to an address
(stored in a register `p`) with an offset of 8, and takes its return address
from the stack.
TODO: Add link to annotation syntax.

Variables in Jasmin have both a type (`int`, `bool`, `u8`, `u16`, `u32`, ...)
and a storage qualifier (`inline`, `reg`, `stack`).
Variables qualified as `inline` are removed in compile time, for instance the
counter of a for loop that is unrolled.
Variables qualified as `reg` must go into architecture registers or flags,
and those qualified as `stack` must live in the stack.
In Jasmin there is no variable spilling: the programmer must do it explicitly.
TODO: Add link to variable and type syntax.

The body of a function consists of variable declarations, Jasmin code, and a
return statement.


# Jasmin code

Jasmin code comprises the following constructs:

```
<code> ::=
  | <empty>
  | <instr> <code>

<instr> ::=
  | <instr_assign>
  | <instr_intrinsic>
  | <instr_conditional>
  | <instr_while>
  | <instr_for>
  | <instr_call>
```

## Variable declaration

Syntax for variable declaration is as follows:
```
<var_list> ::=
  | <var>
  | <var>, <var_list>

<var_decl> ::=
  | <type> <var_list>;
```
For instance,
```
u64 var;
stack u64[N] arr;
inline int i;
```

## Storage locations

There are five locations where values can be stored:

### Variables
```
reg u64 var;
var = foo();
```

### Array accesses
```
stack u64[10] arr;
arr[0] = 5;
```

### Memory accesses
```
fn foo(reg u64 ptr) {
  [ptr] = (64u)0;
  [ptr + 8 * INDEX] = (64u)-1;
}
```

### Subarrays
```
stack u64[4*N] arr;
arr[2*N : N] = foo();
```

### Underscore
```
reg u64 var;
_ = foo();
_, var = bar();
```

TODO: Add link to command syntax.


## Assignments

```
<instr_assign> ::=
  | <lval> = <expr>;
```

The basic syntax for assignments is the usual equal sign
```
x = y;
```
and prefixing the equal sign with a binary operator `x += y;` is simply
syntactic sugar for `x = x + y;`.

Assignments can be made conditional by adding an `if` clause as a suffix
```
x = y if b;
```
This means that the assignment is only performed if the condition `b` evaluates
to `true`.
TODO: Add link to left value and expression syntax.

### Subarrays
To facilitate array handling, subarray notation was introduced. Consider the following example,
```
a[i:N] = add_array(a[i:N], b[j:N]);
```
with,
```
inline fn add_array(stack u64[N] a b) -> stack u64[N] {
  inline int i;
  for i = 0 to N {
    reg u64 ai bi;
    ai = a[i];
    bi = b[i];
    ai += bi;
    a[i] = ai;
  }
  return a;
}
```
Subarrays consists on two parts:
- index: where the access starts
- length: amount of elements to access

### Load and store
Suppose a register `reg u64 ptr` which value is a memory address. Then, for loading values from memory the notation is as follows:
```
reg u64 var;
var = [ptr + offset];
// or for better understanding
var = (64u)[ptr + offset];
```
`offset` is measured in bytes.

Notation for storing values is the same with a slight difference when specifying the type,
```
[ptr + offset] = var;
// or for better understanding
(u64)[ptr + offset] = var;
```

## Intrinsics

```
<instr_intrinsic> ::=
  | <lval>, ..., <lval> = #<ident>(<expr>, ..., <expr>);
```

Intrinsics are architecture-specific instructions.
The basic syntax is as follows
```
of, cf, x = #ROL(y, 4);
```
where to the left of the equals sign is a list of variables and to the right
the architecture-specific instruction is prefixed by a `#` and used as a
function name.

The list of available architecture-specific instructions can be seen using
`$ jasminc -help-intrinsics`.


## Conditionals

```
<instr_conditional> ::=
  | if ( <expr> ) { <code> }
  | if ( <expr> ) { <code> } else { <code> }
  | if ( <expr> ) { <code> } else <instr_conditional>  // else-if syntax.
```

Conditionals take an expression and two pieces of code, and execute one piece
or the other depending on whether the expression evaluates to `true` or
`false` (conditions cannot be complex expressions).
The basic syntax for conditionals is as follows
```
if (x != y) {
    x += 1;
} else {
    x += 2;
}
```
where the `else` branch can be omitted.

Conditionals can be chained as follows
```
if (x < y) {
    x += 1
} else if (x == y) {
    x += 2;
} else {
    x += 3;
}
```
where again last `else` branch can be omitted.
This is syntactic sugar for nested conditionals.


## For loops

```
<instr_for> ::=
  | for <ident> = <expr> to <expr> { <code> }
  | for <ident> = <expr> downto <expr> { <code> }
```

For loops execute a piece of code some fixed number of times.
These iterations depend on an `inline int` counter, that takes values in a given
range.
The basic syntax for for loops is
```
for i = 0 to 13 {
    x += x;
    i += 2;
}
```
where the counter starts taking the value of the first expression, and increases
until it reaches the second one.
For example, the loop `for i = 0 to 5 { ... }` executes the code in the body of
the loop five times, as `i` takes the values 0, 1, 2, 3, and 4 in each iteration
respectively.

The iteration can be made decreasing as well, as follows
```
for i = 13 downto 0 {
    x += x;
    i -= 2;
}
```


## While loops

```
<instr_while> ::=
  | while ( <expr> ) { <code> }  // Usual while loop.
  | while { <code> } ( <expr> )  // do-while loop.
  | while { <code> } ( <expr> ) { <code> }  // Both.
```

While loops execute a piece of code until a condition is met.
The basic syntax for while loops is
```
while (x <= y) {
    y -= 1;
}
```
where the condition `x <= y` is evaluated before each iteration, and the body
is executed only if it evaluated to `true`.

Do-while loops first execute the body and only then evaluate the condition, and
are written as follows
```
while {
    y -= 1;
} (x <= y)
```

In Jasmin, we can do both: execute some code before evaluating the condition,
and also after
```
while {
    y -= 1;
} (x <= y) {
    y -= x;
}
```


## Function calls

```
<instr_call> ::=
  | <ident>(<expr>, ..., <expr>);
  | <lval>, ..., <lval> = <ident>(<expr>, ..., <expr>);
```

The syntax for function calls is as follows
```
z = add_then_shift_left(x, y, 2);
```
where to the left of the equals sign we have a list of variables corresponding
to the return type of the function.
The following is valid syntax for a function that does not return any values:
```
do_side_effect_computation(x, y);
```


## Expressions
```
<expr> ::=
  | <int>  // Integer constant.
  | <bool>  // Boolean constant.
  | <var>  // Variable.
  | (<wsize>)[<var> + <expr>] // Memory access.
  | (<wsize>)[<var> - <expr>] // Memory access.
  | <var>[<expr>]  // Array access.
  | <var>[<wsize> <expr>]  // Array access.
  | <var>.[<wsize> <expr>]  // Unscaled array access.
  | <var>[<expr> : <expr>]  // Subarray.
  | <op1> <expr>  // Unary operation.
  | <expr> <op2> <expr>  // Binary operation.
  | <expr> ? <expr> : <expr>  // Conditional.
  | (<int><sign><int>)[<expr>, ..., <expr>] // Packing.
  | (<expr>)
```

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
  - primitive instructions (`#copy_32(t)`).

### Unary operators

```
<op1> ::=
  | !        // Boolean and bitwise negation.
  | -        // Arithmetic negation.
  | (<cast>) // Cast.

<cast> ::=
  | <wsize_size><sign>
  | int

```

Unary operators are, by decreasing precedence:

  - zero- or sign-extension (`(16s)e`);
  - boolean / bitwise negation (`! b`);
  - opposite (`- e`).

### Binary operators

```
<op2> ::=
  | +    // Addition.
  | -    // Subtraction.
  | *    // Multiplication.
  | /    // Integer division.
  | %    // Modulo.
  | &    // Bitwise AND.
  | |    // Bitwise OR.
  | ^    // Bitwise XOR (exclusive OR).

  | ==   // Equality test.
  | !=   // Inequality test.
  | <    // Unsgined less than test.
  | <s   // Signed less than test.
  | >    // Unsigned greater than test.
  | >s   // Signed greater than test.
  | <=   // Unsigned less than or equal test.
  | <=s  // Signed less than or equal test.
  | >=   // Unsigned greater than or equal test.
  | >=s  // Signed greater than or equal test.

  | <<   // Left shift.
  | >>   // Right shift.
  | >>s  // Arithmetic right shift.
  | <<r  // Left rotation.
  | >>r  // Right rotation.

  | &&   // Boolean AND.
  | ||   // Boolean OR.
```

- `+`: Addition.
- `<<`: Left shift.
- `>>`: Right shift.
- `>>s`: Arithmetic right shift. In ARM-M4, an arithmetic right shift by 0 is
  compiled to a `MOV`.
- `<<r`: Left rotation.
- `>>r`: Right rotation. In ARM-M4, a right rotation by 0 is compiled to a
  `MOV`.

Binary operators are, by decreasing precedence:

  - multiplication (`x * y`), integer division (`x / y`), and remainder (`x % y`);
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
