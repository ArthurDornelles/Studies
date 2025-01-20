```toc
style: number
```

## Foundations of Efficiency

Writing **efficient** [[Python]] code means to code with minimal completion time and minimal resource consumption.
A <span style = "color: #cc4444">Pythonic </span> code is the way of using Python's constructs as intended.

### The Power of NumPy Arrays

#### NumPy array overview

[[NumPy]] is an alternative to [[Python]] lists:

```python
nums_list = list(range(5))
print(nums_list)
```

```CMD
[0, 1, 2, 3, 4]
```

```python
import numpy as np

nums_np = np.array(range(5))
print(nums_np)
```

```CMD
array([0, 1, 2, 3, 4])
```

NumPy has homogeneity to it's array, which means that the whole array <span style = "color :#cc4444">has the same type associated to it</span>.

#### NumPy array broadcasting

Python's built-in lists doesn't support broadcasting, which means that one cannot directly apply an operation to the whole list at once.
NumPy, in the other hand, has support broadcasting.

```python
nums = [-2, -1, 0, 1, 2]

# A Pythonic way with lists to do it
sqrd_nums = [num ** 2 for num in nums]

# Using NumPy
nums_np = np.array(nums) ** 2

print(sqrd_nums)
print(nums_np)
```

```CMD
[4, 1, 0, 1, 4]
array([4, 1, 0, 1, 4])
```

The one dimensional index of lists and NumPy are the same. But, there is a small change with 2-D indexing.

Let:
```python
nums2 = [ [1, 2, 3],
		  [4, 5, 6]]
nums2_np = np.array(nums2)
```

| lists                           | result              | arrays | result | 
| ------------------------------- | ------------------- | ------ | ------ |
| ```nums2[0][1]```               | ```2``` | ```nums2_np[0,1]```        |        ```2```|
| ```[row[0] for row in nums2]``` | ```[1, 4]``` | ```nums2_np[:,0]```        |       ```array([1, 4])``` |

#### NumPy array boolean indexing
NumPy was the base of creating the [[Pandas]] dataframes and is really similar when indexing it's values. Let:
```python
nums = [-2, -1, 0, 1, 2]
nums_np = np.array(nums)
```

One can use a boolean indexing with the broadcasting property:

```python
print(nums_np > 0)
```

```CMD
array([False, False, False, True, True])
```

```python
print(nums_np[nums_np > 0])
```

```CMD
array([1, 2])
```

Making it really <span style = "color: #cc4444">Pythonic</span> to use NumPy!

## Timing and Profiling Code

### Examining runtime

A <span style = "color: #cc4444"> pythonic </span> way to choose the **optimal** coding approach is to write the ones that run faster. A fast code is an efficient code.
One can calculate the runtime with IPython using the [[magic command]] ```%timeit```.

#### Using %timeit

Using IPython, one can time it using:
```Python
%timeit rand_nums = np.random.rand(1000)
```

8.61 $\mu s$  $\pm$ 69.1 $ns$  per loop (mean $\pm$ std. dev. of 7 runs, 10000 loops each)

One can also specify the number of runs and loops using the flags ```-r``` and ```-n``` respectively.

e.g.:  ```%timeit -r2 -n10 rand_nums = np.rand(1000)```

#### Using %timeit as a cell magic mode

You can run the ```%timeit``` in multiple lines of code with:

```Python
%%timeit
nums = []
for x in range(10):
	nums.append(x)
```

8.69 $\mu s$  $\pm$ 91.4 $ns$  per loop (mean $\pm$ std. dev. of 7 runs, 10000 loops each)

you can also use the flag ```-o``` and save it to a variable. This allows to pick properties such as ```best``` and ```worst```:
```Python
time = %timeit -r2 -n10 rand_nums = np.rand(1000)
time.best
```

```
8.619398139999247e-06
```

#### Comparing times
Let's prove that using the literal syntax - ```[]```, ```{}```, ```()``` -  to data structures are better than formal names - ```list()```, ```dict()```, ```tuple()```:
```Python
f_time = %timeit -o formal_dict = dict()
l_time = %timeit -o literal_dict = {}
diff - (f_time.average - l_time.average) * (10**9)
print(f'l_time better than f_time by {diff} ns')
```

```CMD
l_time better than f_time by 51.90819192857814 ns
```

### Code profiling for run time

Python can give you detailed statistics on frequency and duration of function calls where you can have line-by-line analyses. You can use the package ```line_profiler```.

#### Code profiling: line_profiler

Let's see the ```convert_units``` function inside the ```convert_units.py``` file.
```Python
def convert_units(heroes, heights, weights):
	new_hts = [ht * 0.39370 for ht in heights]
	new_wts = [wt * 2.20462 for wt in heights]

	hero_data = {}

	for i, hero in enumerate(heroes):
		hero_data[hero] = (new_hts[i], new_wts[i])

	return hero_data

heroes = ['Batman', 'Superman', 'Wonder Woman']

hts = np.array([188.0, 191.0, 183.0])

wts = np.array([95.0, 101.0, 74.0])

```

In the IPython kernel, you can use:

```Python
%load_ext line_profilers

%lprun -f convert_units convert_units(heroes, hts, wts)
```

```CMD
Line #  Hits  Time    Per Hit % Time  Line Contents

==============================================================

17                                    def convert_units(heroes, heights, weights):

18       1    919.0   919.0   69.3    new_hts = [ht * 0.39370 for ht in heights]

19       1    163.0   163.0   12.3    new_wts = [wt * 2.20462 for wt in heights]

20                                          

21       1    23.0    23.0    1.7     hero_data = {}

22                                          

23       3   137.0    45.7   10.3     for i, hero in enumerate(heroes):

24       3   81.0     27.0    6.1     hero_data[hero] = (new_hts[i], new_wts[i])

25                                          

26       1    4.0      4.0    0.3     return hero_data    
```

now you can check that the list comprehension takes several time of our code and optimizing it would result in the most time performance gain. You can use a [[NumPy]] array instead of the list comprehension.  
```

Line #  Hits  Time    Per Hit % Time  Line Contents
==============================================================
     5                                def convert_units_np(heroes, heights, weights):
     6
     7   1    134.0   134.0     65.4  new_hts = heights * 0.39370
     8   1    13.0    13.0      6.3   new_wts = weights * 2.20462
     9   1    3.0     3.0       1.5   hero_data = {}
    10
    11   3    18.0    6.0       8.8   for i, hero in enumerate(heroes):
    12   3    35.0    11.7      17.1  hero_data[hero] = (new_hts[i], new_wts[i])
    13
    14   1    2.0     2.0       1.0   return hero_data
```

and as we can see, there was a reduction from 919 ns to 134 ns!

### Code profiling for memory usage

As used above for time performance, you can use the ```memory_profiler``` [[Magic Command]] to have a line-by-line analysis of the memory consumption.

```Python
%load_ext memory_profiler

%mprun -f convert_units convert_units(heroes, hts, wts)
```

```
Line # Mem usage  Increment  Occ. Line Contents
=============================================================
17     60.0 MiB   60.0 MiB    1   def convert_units(heroes, heights, weights):
18     60.0 MiB   0.0 MiB     6   new_hts = [ht * 0.39370 for ht in heights]
19     60.0 MiB   0.0 MiB     6   new_wts = [wt * 2.20462 for wt in heights]
20
21     60.0 MiB   0.0 MiB     1   hero_data = {}
22
23     60.0 MiB   0.0 MiB     4   for i, hero in enumerate(heroes):
24     60.0 MiB   0.0 MiB     3      hero_data[hero] = (new_hts[i], new_wts[i])
25
26     60.0 MiB   0.0 MiB     1   return hero_data
```

the standard memory unit used is the MebiByte and values that are read as 0.0 MiB means too small instead of actually zero.

The value may differ from which python or package versions you are using.