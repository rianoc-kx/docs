---
keywords: arithmetic mean, average, avg, avgs, kdb+, mean, moving, q, statistics, volume-weighted average price, vwap, weighted average
---

# `avg`, `avgs`, `mavg`, `wavg`




_Arithmetic mean_

## `avg` 

Syntax: `avg x`, `avg[x]`

Where `x` is a numeric list, returns its arithmetic mean. 

The mean of an atom is itself. 
Null is returned if `x` is empty, or contains both positive and negative infinity. Any null items in `x` are ignored.

```q
q)avg 1 2 3
2f
q)avg 1 0n 2 3       / null values are ignored
2f
q)avg 1.0 0w
0w
q)avg -0w 0w
0n
q)\l trade.q
q)show select ap:avg price by sym from trade
sym| ap
---| -----
a  | 10.75
```

`avg` is an aggregate function.



## `avgs` 

Syntax: `avgs x`, `avgs[x]`

Where `x` is a numeric list, returns the running averages, i.e. applies function `avg` to successive prefixes of `x`.

```q
q)avgs 1 2 3 0n 4 -0w 0w
1 1.5 2 2 2.5 -0w 0n
```

`avgs` is a uniform function.


## `mavg`

_Moving average_

Syntax: `x mavg y`, `mavg[x;y]`

Where 

-   `x` is a positive int atom (not infinite)
-   `y` is a numeric list

returns the `x`-item [simple moving averages](https://en.wikipedia.org/wiki/Moving_average#Simple_moving_average) of `y`, with any nulls after the first item replaced by zero. The first `x` items of the result are the averages of the terms so far, and thereafter the result is the moving average. The result is floating point.

```q
q)2 mavg 1 2 3 5 7 10
1 1.5 2.5 4 6 8.5
q)5 mavg 1 2 3 5 7 10
1 1.5 2 2.75 3.6 5.4
q)5 mavg 0N 2 0N 5 7 0N    / nulls after the first are replaced by 0
0n 2 2 3.5 4.666667 4.666667
```

`mavg` is a uniform function. 


## `wavg` 

_Weighted average_

Syntax: `x wavg y`, `wavg[x;y]`

Where 

-   `x` is a numeric list
-   `y` is a numeric list

returns the average of numeric list `y` weighted by numeric list `x`. The result is a float atom. The calculation is `(sum x*y) % sum x`.

```q
q)2 3 4 wavg 1 2 4
2.666667
q)2 0N 4 5 wavg 1 2 0N 8  / nulls in either argument ignored
6f
```


### Volume-weighted average price

The financial analytic known as [VWAP](https://en.wikipedia.org/wiki/Volume-weighted_average_price "Wikipedia") is a weighted average.

```q
q)select size wavg price by sym from trade
sym| price
---| -----
a  | 10.75
```

`wavg` is an aggregate function.



<i class="far fa-hand-point-right"></i>
Wikipedia:[Weighted average](https://en.wikipedia.org/wiki/Weighted_arithmetic_mean)  
Basics: [Mathematics](../basics/math.md)
