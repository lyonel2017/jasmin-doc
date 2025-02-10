# Code

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

Variables in Jasmin have both a type (`int`, `bool`, `u8`, `u16`, `u32`, ...)
and a storage qualifier (`inline`, `reg`, `stack`).
Variables qualified as `inline` are removed in compile time, for instance the
counter of a for loop that is unrolled.
Variables qualified as `reg` must go into architecture registers or flags,
and those qualified as `stack` must live in the stack.
In Jasmin there is no variable spilling: the programmer must do it explicitly.
TODO: Add link to variable and type syntax.

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

## Left-value
Left-values appear on the left-hand side of assignments. A left-value describes the location where a value is stored.

### Variables
```
reg u64 var;
var = foo();
```
```
stack u64 var;
var = foo();
```
> Note that there are different types of variables. The difference between `reg`, `stack` is explained in another [section]().

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
Subarrays consists on two elements:
- index: where the access starts
- length: amount of elements to access

> Note: In Jasmin it is possible to disable the implicit access (non-scaled acccess) by adding a dot before the opening square brackets.
> For further information, please check [Jasmin wiki](https://github.com/jasmin-lang/jasmin/wiki/Arrays)

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
