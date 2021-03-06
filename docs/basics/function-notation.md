---
keywords: abort, control, expression, function, kdb+, lambda, multiline, notation, q, rank, signal, signed, unsigned
---

# Function notation




Function notation enables the definition of functions.
Function notation is also known as the _lambda notation_ and the defined functions as _lambdas_.

!!! note "Anonymity"

    Although the term _lambda_ originated elsewhere as a name for an anonymous function, we use it to denote any function defined using the lambda notation.

    In this usage a lambda assigned a name is still a lambda.
    For example, if `plus:{x+y}`, then `plus` is a lambda.


A lambda is defined as a pair of braces (curly brackets) enclosing an optional _signature_ (a list of up to 8 argument names) followed by a zero or more expressions separated by semicolons. 


## Signature

```q
q){[a;b] a2:a*a; b2:b*b; a2+b2+2*a*b}[20;4]  / binary function
576
```

Functions with 3 or fewer arguments may omit the signature and instead use default argument names `x`, `y` and `z`. 

A lambda with a signature is _signed_; without, _unsigned_.

```q
q){[x;y](x*x)+(y*y)+2*x*y}[20;4]  / signed lambda
576
q){(x*x)+(y*y)+2*x*y}[20;4]       / unsigned lambda
576
```


## Rank

The [rank](glossary.md#rank) of a function is the number of arguments it takes. 

The rank of a signed lambda is the number of names in its signature.

The rank of an unsigned lambda is the here highest-numbered of the three default argument names `x` (1), `y` (2) and `z` (3) used in the function definition.

```q
{[h;l;o;c].5*(h-l;c-o)}      / rank 4
{x+y*10}                     / rank 2
{x+z*10}                     / rank 3
```


## Result

The result of the lambda is the result of the last statement evaluated. If the last statement is empty, the result is the generic null, which is not displayed.

```q
q)f:{2*x;}      / last statement is empty
q)f 10          / no result shown
q)(::)~f 10     / matches generic null
1b
```


## Explicit return

To terminate evaluation successfully and return a value, use an empty assignment, which is `:` with a value to its right and no variable to its left.

```q
q)c:0
q)f:{a:6;b:7;:a*b;c::98}
q)f 0
42
q)c
0
```

<i class="far fa-hand-point-right"></i> 
[Evaluation control](control.md)


## Abort

To abort evaluation immediately, use Signal, which is `'` with a value to its right.

```q
q)c:0
q)g:{a:6;b:7;'`TheEnd;c::98}
q)g 0
{a:6;b:7;'`TheEnd;c::98}
'TheEnd
q)c
0
```

<i class="far fa-hand-point-right"></i> 
[Error handling](errors.md) 


## Name scope

Within the context of a function, name assignments with `:` are _local_ to it and end after evaluation. Assignments with `::` are _global_ (in the session root) and persist after evaluation.

```q
q)a:b:0                      / set globals a and b to 0
q)f:{a:10+3*x;b::100+a;}     / f sets local a, global b
q)f 1 2 3                    / apply f
q)a                          / global a is unchanged
0
q)b                          / global b is updated
113 116 119
```

References to names _not_ assigned locally are resolved in the session root. Local assignments are _strictly local_: invisible to other functions applied during evaluation. 

```q
q)a:42           / assigned in root
q)f:{a+x}
q)f 1            / f reads a in root
43
q){a:1000;f x}1  / f reads a in root
43
```


## Multiline definition

In scripts function definitions can straddle multiple lines.

```q
sqsum:{[a;b]   / square of sum
  a2:a*a;
  b2:b*b;
  a2+b2+2*a*b  / implicit result
  }
```


<i class="far fa-hand-point-right"></i>
[Multiline expressions](syntax.md#multiline-expressions)


## Variables and constants

A lambda definition can include up to: 

&nbsp;    | in use | current     | <V3.6 2017.09.26
----------|--------|-------------|------------------
arguments |        | 8           | 8
locals    | $m$    | 110         | 31
globals   | $n$    | 110         | 23
constants |        | $240-(m+n)$ | $95-(m+n)$

<i class="far fa-hand-point-right"></i>
[Parse errors](errors.md#parse-errors)

