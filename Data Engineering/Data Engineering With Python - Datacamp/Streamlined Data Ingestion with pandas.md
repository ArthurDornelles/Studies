## Introduction to Flat Files
[[Pandas]]  is a major library for analytics used for creating bidimensional data frames. It's easy to connect to databases, APIs, files and other data sources.

#### Loading CSVs
Let's say that we have a [[csv]] file __called us_tax_data_2016.csv__, with a sample :
```
STATEFIPS,STATE,zipcode,agi_stub,...,N11901,A11901,N11902,A11902
1,AL,0,1,...,64320,51444,7111580,1831661
```
We can import it as:
```python
import pandas as pd
tax_data = pd.read_csv("us_tax_data_2016.csv")
tax_data.head(4)
```
| |STATEFIPS| STATE | zipcode| agi_stub| ... | N11901| A11901 | N11902 | A11902 |
| -- |---|---|---|---|---|---|---|---|---|
| 0 | 1 |AL| 0| 1| ... | 63420 | 5144 | 711580 | 1831661|
| 1 |1|AL|0|2|...|74090| 110889|416090|1173463|
| 2 |1|AL|0|3|...|64000| 143060|195130|543284|
| 3 |1|AL|0|4|...|45020| 128920|117410|381329|

[ 4 rows x 147 columns ]
___
### Loading Other Flat Files
We can specify a different delimiter with __sep__, e.g. a sample of __called us_tax_data_2016.tsv__:

```
STATEFIPS   STATE   zipcode   agi_stub   ...   N11901   A11901   N11902   A11902
1   AL   0   1   ...   64320   51444   7111580   1831661
```
That we can import using:
```python
import pandas as pd
tax_data = pd.read_csv("us_tax_data_2016.tsv", sep="\t")
tax_data.head(3)
```
| |STATEFIPS| STATE | zipcode| agi_stub| ... | N11901| A11901 | N11902 | A11902 |
| -- |--:|---|--:|--:|---|--:|--:|--:|--:|
| 0 | 1 |AL| 0| 1| ... | 63420 | 5144 | 711580 | 1831661|
| 1 |1|AL|0|2|...|74090| 110889|416090|1173463|
| 2 |1|AL|0|3|...|64000| 143060|195130|543284|
<span> [ 3 rows x 147 columns ] </span>
___
## Modifying flat file imports

```python
tax data = pd.read_csv("us_tax_data_2016.csv")

print(tax_data.shape)
```
 | (179796, 147)

### Limiting Columns
You can choose columns to load directly with the key word argument __usecols__, you can use both column names or number:
```python
col_names = ['STATEFIPS', 'STATE', 'zipcode','agi_stub','N1']
col_nums = [0, 1, 2, 3, 4]
# Choose columns to load by name
tax_data_v1 = pd.read_csv('us_tax_data_2016.csv',
						usecols=col_names)
# Choose columns to load by number
tax_data_v2 = pd.read_csv('us_tax_data_2016.csv',
						 usecols=col_nums)
print(tax_data_v1.equals(tax_data_v2))
```
| True

### Limiting Rows
Similarly, whe can limit the number of rows loaded with __nrows__ keyword argument:
```python
tax_data_first1000 = pd.read_csv('us_tax_data_2016.csv', nrows=1000)
print(tax_data_first1000.shape)
```
| (1000, 147)

### Assigning Column Names
For example reasons, one can opt to assign a name for the columns from import. Remember that **the list of columns must have a name for every column in the data frame**.
```python
col_names = list(tax_data_first1000)
tax_data_next500 = pd.read_csv('us_tax_data_2016.csv',
								nrows=500,
								skiprows=1000,
								header=None,
								names=col_names)
print(tax_data_next500.head(1))
```
| |STATEFIPS| STATE | zipcode| agi_stub| ... | N11901| A11901 | N11902 | A11902 |
| -- |--:|---|--:|--:|---|--:|--:|--:|--:|
| 0 | 1 |AL| 35565| 4| ... | 50 | 222 | 210 | 794|
[1 rows x 147 columns]

---
## Handling errors and missing data

Most of the data engineering job is to look out for errors and data that is missing. [[Pandas]] offers many alternatives to handle various scenarios. As [[Python]] is not a static typed language, pandas automatically infers column data types.

### Specifying Data Types

A good practice is to keep your mind open to any possible automatic data types changes. One can look pandas data types using __dtypes__
```python
print(tax_data.dtypes)
```

| STATEFIPS | int64 |
| --- | --:|
| STATE | object |
| zipcode | int64 |
| agi_stub | int64 |
| N1 | int64 |
|  | ... |
| N11902 | int64 |
| A11902 | int64 |
Length: 147, dtype: object

In import, one can insert a dictionary with column names and types using __dtype__:
```python
tax_data = pd.read_csv('us_tax_data_2016.csv', dtype={'zipcode':str})
print(tax_data.dtypes)
```

| STATEFIPS | int64 |
| --- | --:|
| STATE | object |
| zipcode | object |
| agi_stub | int64 |
| N1 | int64 |
|  | ... |
| N11902 | int64 |
| A11902 | int64 |
Length: 147, dtype: object

### Customizing Missing Data Values

[[Pandas]] automatically interprets some values as missing or NA. You can define your own NA values with __na_values__ - you can pass a dictionary of columns with values, a single value or a list.
