## Requires

Require statements consist of the `require` keyword followed by a file name
(between quotation marks)
```
require <string>
```

It can be used for several files
```
require <string> ... <string>
```

And given a base path
```
from <ident> require <string> ... <string>
```

See [[the require section|Jasmin-language-guide#requires]] in the language
guide for information on what this statement means.


## Parameters

The syntax for parameter declaration consists of the `param` keyword, the type
and name of the parameter, and its value
```
param <type> <ident> = <expr>;
```

See [[the parameter section|Jasmin-language-guide#parameters]] in the language
guide for information on what this statement means.


## Global variables

TODO: Add global variable syntax.


## Function declaration

TODO: Add function declaration syntax.


## Instructions

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

### Assignments

```
<instr_assign> ::=
  | <lval> = <expr>;
```

For binary operators of the shape `<+> : a -> b -> a`, the following syntax is
also valid
```
<lval> <+>= <expr>;
```
where the first argument of the operator is taken to be the left hand side of
the assignment.


### Intrinsics

```
<instr_intrinsic> ::=
  | <lval>, ..., <lval> = #<ident>(<expr>, ..., <expr>);
```


### Conditionals

```
<instr_conditional> ::=
  | if ( <expr> ) { <code> }
  | if ( <expr> ) { <code> } else { <code> }
  | if ( <expr> ) { <code> } else <instr_conditional>  // else-if syntax.
```

### While loops

```
<instr_while> ::=
  | while ( <expr> ) { <code> }  // Usual while loop.
  | while { <code> } ( <expr> )  // do-while loop.
  | while { <code> } ( <expr> ) { <code> }  // Both.
```


### For loops

```
<instr_for> ::=
  | for <ident> = <expr> to <expr> { <code> }
  | for <ident> = <expr> downto <expr> { <code> }
```

### Function calls

```
<instr_call> ::=
  | <ident>(<expr>, ..., <expr>);
  | <lval>, ..., <lval> = <ident>(<expr>, ..., <expr>);
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

### Unary operators

```
<op1> ::=
  | !  // Boolean and bitwise negation.
  | -  // Arithmetic negation.
  | (<cast>) // Cast.

<cast> ::=
  | <wsize_size><sign>
  | int
```


### Binary operators

```
<op2> ::=
  | +  // Addition.
  | -  // Subtraction.
  | *  // Multiplication.
  | /  // Division.
  | %  // Modulo.
  | &  // Bitwise AND.
  | |  // Bitwise OR.
  | ^  // Bitwise XOR (exclusive OR).

  | ==  // Equality test.
  | !=  // Inequality test.
  | <  // Unsgined less than test.
  | <s  // Signed less than test.
  | >  // Unsigned greater than test.
  | >s  // Signed greater than test.
  | <=  // Unsigned less than or equal test.
  | <=s  // Signed less than or equal test.
  | >=  // Unsigned greater than or equal test.
  | >=s  // Signed greater than or equal test.

  | <<  // Left rotation.
  | >>  // Right rotation.
  | >>s  // Arithmetic right rotation.
  | <<r  // Left rotation.
  | >>r  // Right rotation.

  | &&  // Boolean AND.
  | ||  // Boolean OR.
```


TODO: Where to put these?

```
<wsize_size> ::=
  | 8
  | 16
  | 32
  | 64
  | 128
  | 256

<wsize> ::= u<wsize_size>
```

```
<sign> ::=
  | s
  | u
```
