---
keywords: cross, dot product, kdb+, list, product, q, vector
---

# `cross`



Syntax: `x cross y`, `cross[x;y]`

Returns the cross-product (i.e. all possible combinations) of `x` and `y`.

```q
q)1 2 3 cross 10 20
1 10
1 20
2 10
2 20
3 10
3 20
q)(cross/)(2 3;10;"abc")
2 10 "a"
2 10 "b"
2 10 "c"
3 10 "a"
3 10 "b"
3 10 "c"
```

`cross` can work on tables and dictionaries. 

```q
q)s:`IBM`MSFT`AAPL
q)v:1 2
q)([]s:s)cross([]v:v)
s    v
------
IBM  1
IBM  2
MSFT 1
MSFT 2
AAPL 1
AAPL 2
```


