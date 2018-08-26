# . Apply



_Apply a function to a list of arguments. 
Trap errors.
Get items at depth in a list._

> Everything begins with a dot.  
> — _Vassily Kandinsky_



rank | syntax               | function semantics                  | list semantics
-----|----------------------|-------------------------------------|---------------
2    | `m . mx`,  `.[m;mx]` | apply `m` to list `mx` of arguments | get item/s at depth from `m`
2    | `u @ ux`,  `@[u;ux]` | apply unary `u` to argument `ux`    | get items `ux` from `u`
3    | `.[g;gx;e]`          | try `g . gx`; catch with `e`        |
3    | `@[f;fx;e]`          | try `f@fx`; catch with `e`          |

Where

-   `e` is an expression, typically a function
-   `f` is a unary function and `fx` in its domain 
-   `g` is a function of rank $n$ and `gx` an atom or list of count $n$ with items in the domains of `g`
-   `m` is a map of rank $n$ (or a handle to one) and `mx` a list of count $n$ with items in the domains of `m`
-   `u` is a unary map (or a handle to one) and `ux` in its domain

<i class="far fa-hand-point-right"></i> [Maps](/basics/maps) for why functions, lists and dictionaries are all maps.


## Apply, Index

`m . mx` evaluates map `m` on the $n$ arguments listed in `mx`.
```q
q)+ . 2 3           / +[2;3] Apply
5
q)add
0 1 2 3
1 2 3 4
2 3 4 5
3 4 5 6
q)add . 2 3         / add[2;3] Index
5
q).[+;2 3]
5
q).[add;2 3]
5
```
If `m` has rank $n$, then `mx` has $n$ items and `m` is evaluated as:
```q
m[mx[0]; mx[1]; …; mx[-1+count mx]]
```
If `m` has rank 2 then `mx` has 2 items and `m` is applied to the first argument `mx[0]` and the second argument `mx[1]`.
```q
m[mx[0];mx[1]]
```
If `m` has 1 argument then `mx` has 1 item and `m` is applied to the argument `mx[0]`. 
```q
m[mx[0]]
```


## Nullaries

Nullaries (functions of rank 0) are handled differently. The pattern above suggests that the empty list `()` would be the argument list to nullary `m`, but `m . ()` is a case of Index, where empty `mx` always selects `m`. 

Apply for nullary `m` is denoted by `m . enlist[::]`, i.e. the right argument is the enlisted nil. 
For example:
```q
q)a: 2 3
q)b: 10 20
q){a + b} . enlist[::]
12 23
```


## Index

`d . i` returns an item from list `d` as specified by successive items in list `i`.
The result is found in `d` at depth `count i` as follows. 

The list `i` is a list of successive indexes into `d`. `i[0]` must be in the domain of `d@`. It selects an item of `d`, which is then indexed by `i[1]`, and so on.
```q
( (d@i[0]) @ i[1] ) @ i[2] …
```
```q
q)d
(1 2 3;4 5 6 7)
(8 9;10;11 12)
(13 14;15 16 17 18;19 20)
q)d . enlist 1      / select item 1, i.e. d@1
8 9
10
11 12
q)d . 1 2           / select item 2 of item 1
11 12
q)d . 1 2 0         / select item 0 of item 2 of item 1
11
```


### Index At

The selections at each level are individual applications of Index At: first, item `d@i[0]` is selected, then `(d@i[0])@i[1]`, then `((d@i[0])@ i[1])@ i[2]`, and so on. 

These expressions can be rewritten using [Over](reduce) applied to Index At; the first is `d@/i[0]`, the second is `d@/i[0 1]`, and the third is `d@/i[0 1 2]`. 

In general, for a vector `i` of any count, `d . i` is identical to `d@/i`. 
```q
q)((d @ 1) @ 2) @ 0         / selection in terms of a series of @s
11
q)d @/ 1 2 0                / selection in terms of @-Over
11
```


### Cross sections

Index is cross-sectional when the items of `i` are lists. That is, items-at-depth in `d` are indexed for paths made up of all combinations of atoms of `i[0]` and atoms of `i[1]` and atoms of `i[2]`, and so on to the last item of `i`. 

The simplest case of cross-sectional index occurs when the items of `i` are vectors. For example, `d .(2 0;0 1)` selects items 0 and 1 from both items 2 and 0:
```q
q)d . (2 0; 0 1)
13 14 15 16 17 18
1 2 3 4 5 6 7
q)count each d . (2 0; 0 1)
2 2
```
Note that items appear in the result in the same order as the indexes appear in `i`.

The first item of `i` selects two items of `d`, as in `d@i[0]`. The second item of `i` selects two items from each of the two items just selected, as in `(d@i[0])@'i[1]`. Had there been a third vector item in `i`, say of count 5, then that item would select five items from each of the four items-at-depth 1 just selected, as in `((d@i[0])@'i[1])@''i[2]`, and so on. 

When the items of `i` are vectors the result is rectangular to at least depth `count i`, depending on the regularity of `d`, and the `k`th item of its shape vector is `(count i)[k]` for every `k` less than `count i`. That is, the first `count i` items of the shape of the result are `count each i`.


More general cross-sectional indexing occurs when the items of `i` are rectangular lists, not just vectors, but the situation is much like the simpler case of vector items. 

<!-- In particular, the shape of the result is ,/^:'i. FIXME -->


### Nils in i

Nils in `i` mean “select all”: if `i[0]` is nil, then continue on with `d` and the rest of `i`, i.e. `1_i`; if `i[1]` is nil, then for every selection made through `i[0]`, continue on with that selection and the rest of `i`, i.e. `2_i`; and so on. For example, `d .(::;0)` means that the 0th item of every item of `d` is selected.
```q
q)d
(1 2 3;4 5 6 7)
(8 9;10;11 12)
(13 14;15 16 17 18;19 20)
q)d . (::;0)
1 2 3
8 9
13 14
```
Another example, this time with `i[1]` equal to nil:
```q
q)d . (0 2;::;1 0)
(2 1;5 4)
(14 13;16 15;20 19)
```
Note that `d .(::;0)` is the same as `d .(0 1 2;0)`, but in the last example, there is no value that can be substituted for nil in `(0 2;;1 0)` to get the same result, because when item 0 of `d` is selected, nil acts like `0 1`, but when item 2 of `d` is selected, it acts like `0 1 2`.


### The general case of a non-negative integer list i

In the general case, when the items of `i` are non-negative integer atoms or lists, or nil, the structure of the result can be thought of as cascading structures of the items of `i`. That is, with nils aside, the result is structurally like `i[0]`, except that wherever there is an atom in `i[0]`, the result is structurally like `i[1]`, except that wherever there is an atom in `i[1]`, the result is structurally like `i[2]`, and so on. 

The general case of Index can be defined recursively in terms of [**Index At**](index-at) by partitioning the list `i` into its first item and the rest:
```q
Index:{[d;F;R] 
  $[ F~::; Index[d; first R; 1 _ R];
     0 =count R; d @ F;
     0>type F; Index[d @ F; first R; 1 _ R]
     Index[d;; R]'F ]}
```
That is, `d . i` is `Index[d;first i;1_i]`. 

To work through the definition, start with `F` as the first item of `i` and `R` as the remainder. At each step in the recursion: 

-   if `F` is nil then select all of `d` and continue on, with the first item of the remainder `R` as the new `F` and the remainder of `R` as the new remainder; 
-   otherwise, if the remainder is the empty vector apply Index At (the right argument `F` is now the last item of `i`), and we are done; 
-   otherwise, if `F` is an atom, apply Index At to select that item of `d` and continue on in the same way as when `F` is nil; 
-   otherwise, apply Index with fixed arguments `d` and `R`, but independently to the items of the list `F`.


### Dictionaries and symbolic indexing

If `i` is a symbol atom then `d` must be a dictionary or handle of a directory on the K-tree, and `d . i` selects the value of the entry named in `i`. For example, if:
```q
dir:`a`b!(2 3 4;"abcdefg")
```
then `` `dir . enlist`b`` is `"abcdefg"` and `` `dir . (`b;1 3 5)`` is `"bdf"`.

If `i` is a list whose items are non-negative integer atoms and symbol atoms, then just like the non-negative integer vector case, `d . i` is a single item at depth `count i` in `d`. The difference is that wherever a symbol appears in `i`, say as the kth item, the selection up to the kth item must produce a dictionary or a handle of a directory. Selection by the kth item is the value of an entry in that dictionary or directory, and further selections go on from there. For example:
```q
q)(1;`a`b!(2 3 4;10 20 30 40)) . (1; `b; 2)
30
```
As we have seen above for the general case, every atom in the `k`th item of `i` must be a valid index of all items at depth `k` selected by `d . k # i`. Moreover, symbols can only select from dictionaries and directories, and integers cannot. 
Consequently, if the kth item of i contains a symbol atom, then all items selected by `d . k # i` must be dictionaries or handles of directories, and therefore all atoms in the `k`th item of `i` must be symbols.

It follows that each item of `i` must be made up entirely of non-negative integer atoms, or entirely of symbol atoms, and if the `k`th item of `i` is made up of symbols, then all items at depth `k` in `d` selected by the first `k` items of `i` must be dictionaries.

Note that if `d` is either a dictionary or handle to a directory then `d . enlist key d` is a list of values of all the entries.


## Apply At, Index At

`@` is [syntactic sugar](https://en.wikipedia.org/wiki/Syntactic_sugar "Wikipedia") for the case where `m` is a unary and `mx` a 1-item list. 
`u@ux` is always equivalent to `u . enlist ux`.

!!! note "Brackets are syntactic sugar"

    The brackets of an argument list are also syntactic sugar. Nothing can be expressed with brackets that cannot also be expressed using `.`.

You can use the derived function `@\:` to apply a list of unary maps to the same argument. 

```q
q){`o`h`l`c!(first;max;min;last)@\:x}1 2 3 4 22  / open, high, low, close
o| 1
h| 22
l| 1
c| 22
```


## Trap

In the ternary, if evaluation of the function fails, the expression is evaluated. 
(Compare try/catch in some other languages.)
```q
q).[+;"ab";`ouch]
`ouch
```
If the expression is a function, it is evaluated on the text of the signalled error.
```q
q).[+;"ab";{"Wrong ",x}]
"Wrong type"
```
For a successful evaluation, the ternary returns the same result as the binary.
```q
q).[+;2 3;{"Wrong ",x}]
5
```


### Trap At

`@[f;fx;e]` is equivalent to `.[f;enlist fx;e]`. 

Use Trap At as a simpler form of Trap, for unary maps.


### Limit of the trap

Trap catches only errors signalled in the applications of `f` or `g`. Errors in the evaluation of `fx` or `gg` themselves are not caught.
```a
q)@[2+;"42";`err]
`err
q)@[2+;"42"+3;`err]
'type
  [0]  @[2+;"42"+3;`err]
                ^
```


### When e is not a function

If `e` is a function it will be evaluated _only_ if `f` or `g` fails. It will however be _parsed_ before any of the other expressions are evaluated.
```q
q)@[2+;"42";{)}]
')
  [0]  @[2+;"42";{)}]
                  ^
```
If `e` is any _other_ kind of expression it will _always_ be evaluated – and _first_, in the usual right-to-left sequence. In this respect Trap and Trap At are unlike try/catch in other languages. 
```q
q)@[string;42;a:100] / expression not a function
"42"
q)a // but a was assigned anyway
100
q)@[string;42;{b::99}] / expression is a function
"42"
q)b // not evaluated
'b
  [0]  b
       ^
```
For most purposes, you will want `e` to be a function.


## Errors signalled

error  | cause
-------|-----------------------------------------------------------------------
domain | the symbol `d` is not a handle
index  | an atom in `mx` or `ux` is not an index to an item-at-depth in `d`
rank   | the count of `mx` is greater than the rank of `m`
type   | `m` or `u` is a symbol atom, but not a handle to a map
type   | an atom of `mx` or `ux` is not an integer, symbol or nil

