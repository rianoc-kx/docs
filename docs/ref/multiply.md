# `*` Multiply


Syntax: `x*y`, `*[x;y]` (atomic) 

Where `x` and `y` are conformable numerics or temporals, returns their product.
```q
q)3 4 5*2.2
6.6 8.8 11
q)1.1*`a`b`c!5 10 20
a| 5.5
b| 11
c| 22
q)t:([]price:10 20 30;qty:200 150 17)
q)t*\:1.15 1 /raise all prices 15%
price qty
---------
11.5  200
23    150
34.5  17
q)update price:price*1+.15*qty<50 from t /raise prices 15% where stock<50
price qty
---------
10    200
20    150
34.5  17
q)
```


## Range and domains
```
*| b g x h i j e f c s p m d z n u v t
-| -----------------------------------
b| i . i i i j e f . . p m d z n u v t
g| . . . . . . . . . . . . . . . . . .
x| i . i i i j e f . . p m d z n u v t
h| i . i i i j e f . . p m d z n u v t
i| i . i i i j e f . . p m d z n u v t
j| j . j j j j e f . . p m d z n u v t
e| e . e e e e e f . . p m d z n u v t
f| f . f f f f f f f . f f z z f f f f
c| . . . . . . . f . . p m d z n u v t
s| . . . . . . . . . . . . . . . . . .
p| p . p p p p p f p . . . . . . . . .
m| m . m m m m m f m . . . . . . . . .
d| d . d d d d d z d . . . . . . . . .
z| z . z z z z z z z . . . . . . . . .
n| n . n n n n n f n . . . . . . . . .
u| u . u u u u u f u . . . . . . . . .
v| v . v v v v v f v . . . . . . . . .
t| t . t t t t t f t . . . . . . . . .
```
Range: `ijefpmdznuvt`

<i class="far fa-hand-point-right"></i> [Arithmetic functions](/basics/arithmetic), [Datatypes](/basics/datatypes), [`differ`](/ref/differ), [`.Q.addmonths`](dotq/#qaddmonths)

