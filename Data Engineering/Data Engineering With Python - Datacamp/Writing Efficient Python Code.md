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