

```python
import pandas as pd
import functions_i_use_a_lot as f

from importlib import reload # reload function so you don't have to restart kernel 
reload(f) # reload f
```




    <module 'functions_i_use_a_lot' from '/Users/rcarrasco/notes/dsft/section11/dsc-1-11-13-adjusted-r-squared-lab-online-ds-ft-100118/functions_i_use_a_lot.py'>




```python
# tab for autocomplete
make_a_variable = np.random.random()
```


```python
make_a_variable
```




    0.7015202056566043




```python
# Shift + Tab
# Shift + Tab x 2
# Shitf + Tab x 4
pd.DataFrame()
```


```python
def my_func(x=None, y=None):
    """
    Parameters/Input
    x : int/float
    y : string
    
    return 'x + y' as string
    """
    pass
```


```python
my_func()
```


```python
f.sqrt(5)
```




    2.23606797749979




```python
f.squared(5)
```




    25




```python
f.cubed(5)
```




    125




```python
# enumerate
fib = [0, 1]
while len(fib) < 1000:
    a = fib[-2]
    b = fib[-1]
    c = a + b
    fib.append(c)
```


```python
for index, fib_ in enumerate(fib):
    print(index, fib_)
    if index>10:
        break
```

    0 0
    1 1
    2 1
    3 2
    4 3
    5 5
    6 8
    7 13
    8 21
    9 34
    10 55
    11 89



```python
x = list(range(10, 20))
y = list(range(20, 30))
z = list(range(50, 60))
w = list(range(100, 110))
```


```python
for index, i in enumerate(x):
    print(i + y[index])
```

    30
    32
    34
    36
    38
    40
    42
    44
    46
    48



```python
# zip 
for i, j, k, l in zip(x, y, z, w):
    print(i, j, k, l, i + j + k + l)
```

    10 20 50 100 180
    11 21 51 101 184
    12 22 52 102 188
    13 23 53 103 192
    14 24 54 104 196
    15 25 55 105 200
    16 26 56 106 204
    17 27 57 107 208
    18 28 58 108 212
    19 29 59 109 216



```python
# print statements/ strings in general
# print "y is greater than x"
```


```python
for i, j in zip(x, y):
    print("{} is greater than {}".format(j, i))
```

    20 is greater than 10
    21 is greater than 11
    22 is greater than 12
    23 is greater than 13
    24 is greater than 14
    25 is greater than 15
    26 is greater than 16
    27 is greater than 17
    28 is greater than 18
    29 is greater than 19



```python
# list comprehension 
# lst = [list_stuff (if/else) (for loop) (if)]
lst = [make_pair(i, j)  if i%2==0 else (i, i) for i, j in zip(x, y)]
lst
```




    [(10, 20),
     (11, 11),
     (12, 22),
     (13, 13),
     (14, 24),
     (15, 15),
     (16, 26),
     (17, 17),
     (18, 28),
     (19, 19)]




```python
sq_n_add = lambda n, k: n**2 + k # lambda functions
```


```python
sq_n_add(8, 10)
```




    74




```python
lst = [sq_n_add(i, j) for i, j in zip(x, y)]
```


```python
lst
```




    [120, 142, 166, 192, 220, 250, 282, 316, 352, 390]




```python
make_lst = lambda n: list(range(n))
```


```python
make_lst(5)
```




    [0, 1, 2, 3, 4]




```python
# inline if/else
p = False
```


```python
p1 = 8 if p else 10
p1
```




    10




```python
%%time
func = lambda i, j: 10 if (i>10 and i!=j) else 0
```

    CPU times: user 9 µs, sys: 2 µs, total: 11 µs
    Wall time: 52 µs



```python
func(15, 5)
```




    10


