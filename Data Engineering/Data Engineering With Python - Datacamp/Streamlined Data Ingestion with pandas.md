# Table of Contents
```toc
```


## Importing Data from Flat Files

###  Introduction to Flat Files
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
#### Loading Other Flat Files
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
### Modifying flat file imports 

```python
tax data = pd.read_csv("us_tax_data_2016.csv")

print(tax_data.shape)
```
 | (179796, 147)

#### Limiting Columns
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

#### Limiting Rows
Similarly, whe can limit the number of rows loaded with __nrows__ keyword argument:
```python
tax_data_first1000 = pd.read_csv('us_tax_data_2016.csv', nrows=1000)
print(tax_data_first1000.shape)
```
| (1000, 147)

#### Assigning Column Names
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
### Handling errors and missing data

Most of the data engineering job is to look out for errors and data that is missing. [[Pandas]] offers many alternatives to handle various scenarios. As [[Python]] is not a static typed language, pandas automatically infers column data types.

#### Specifying Data Types

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

#### Customizing Missing Data Values

[[Pandas]] automatically interprets some values as missing or NA. You can define your own NA values with __na_values__ - you can pass a dictionary of columns with values, a single value or a list.
```python
tax_data = pd.read_csv('us_tax_data_2016.csv',
					  na_values={'zipcode':0})
print(tax_data[tax_data.zipcode.isna()]) # filtering only rows that has na in zipcode
```
| |STATEFIPS| STATE | zipcode| agi_stub| ... | N11901| A11901 | N11902 | A11902 |
| -- |--:|---|--:|--:|---|--:|--:|--:|--:|
| 0 | 1 |AL| NaN| 1| ... | 63420 | 5144 | 711580 | 1831661|
| 1 |1|AL|NaN|2|...|74090| 110889|416090|1173463|
| 2 |1|AL|NaN|3|...|64000| 143060|195130|543284|
| ... | ...| ... | ... | ... | ... | ...| ... |...| ...|
| 179034 |56|WY|NaN|5|...|13230| 73326|22250|99589|
| 179035 |56 |WY|NaN|6|...|3530| 128149|2250|125557|
[306 rows x 147 columns]

#### Lines with Errors
If a file is corrupted in some way, the direct import can raise an error. We can use __error_bad_lines__ __=__ __False__ to skip uparseable records and set __warn_bad_lines__ __=__ __True__ to see messages when records are skiped
```python
tax_data = pd.read_csv('us_tax_data_2016_corrupt.csv',
					  error_bad_lines=False,
					  warn_bad_lines=True)
```
| b'Skipping line 3: expected 147 fields, saw 148\n'
1

## Importing Data From Excel Files

A spreadsheet, a.k.a [[Excel]] files is a data stored tabular form, with cells arranged in rows and columns. It can have multiple pages in a single workbook, it can have formating and formulas in it's cells.

### Loading Spreadsheets
[[Pandas]] has a specific loading function for [[Excel]] files, the __read_excel()__:
```python
import pandas as pd

# Read the Excel file
survey_data = pd.read_excel('fcc_survey.xlsx')

# View the first 5 lines of data
print(survey_data.head())
```

| |Age| AttendedBootcamp | ... | SchoolMajor | StudentDebtOwe |
| -- |---|---|---|--:|--:|
| 0 | 28.0 |0.0| ... | NaN | 5144 |
| 1 |22|0.0|...|NaN| NaN|
|2| 19.0 |0.0|...|NaN | 7000|
| 3 |26.0|0.0|...|Cinematography And Film| 143060|
| 4 |20.0|0.0|...|NaN| NaN|
[5 rows x 98 columns]
#### Loading Select Columns and Rows
Similarly as seen previously in the loading of [[csv]] files, the __read_excel()__ function has the parameters __nrows__ to limit the number of rows imported, __skiprows__ to ignore the __n__ first rows and __usecols__ to select the columns used. But as a [[Excel]] orients their columns using letters sequence, the parameter __usecols__ accept a similar syntax that we would see in the spreadsheet.
```python
# Read columns W-AB and AR of file, skipping metadata header
survey_data = pd.read_excel('fcc_survey_with_headers.xlsx',
						   skiprows=2,
						   usecols="W:AB, AR")

print(survey_data.head())
```
| |CommuteTime| CountryCitizen | ... | EmploymentFieldOther | EmploymentStatus | Income|
| -- |--:|---|---|---|---| --: |
| 0 | 35.0 |United States of America|...|NaN|Employed for wages| 32000.0 |
| 1 | 90.0 |United States of America|...|NaN|Employed for wages| 15000.0 |
| 2 | 45.0 |United States of America|...|NaN|Employed for wages| 48000.0 |
| 3 | 45.0 |United States of America|...|NaN|Employed for wages| 43000.0 |
| 4 | 10.0 |United States of America|...|NaN|Employed for wages|  6000.0 |
[5 rows x 7 columns]

#### Getting Data From Multiple Worksheets

By default, __read_excel()__ loads the first sheet in a workbook. One can use the parameter __sheet_name__ to load a specific spreadsheet or a list by name or by zero-indexed position number. Any other arguments passed to __read_excel()__ apply to all sheets read.
Let's say that we have a workbook with two spreadsheets (2016 and 2017).
```python
# Get the second sheet by position index
survey_data_sheet2 = pd.read_excel('fcc_survey.xlsx',
								  sheet_name=1)
# Get the second sheet by name
surveyu_data_2017 = pd.read_excel('fcc_survey.xlsx',
								 sheet_name='2017')
print(survey_data_sheet2.equals(survey_data_2017))
```
```cmd
|  True
```
#### Loading All Sheets
If you pass the value __None__ to __read_excel()__ it reads all sheets in a workbook and store it in a dictionary.
```python
survey_responses = pd.read_excel('fcc_survey.xlsx', sheet_name=None)

print(type(survey_responses))
```

```
| <class 'collections.OrderedDict'>
```

```python
for key, value in survey_responses.items():
	print(key, type(value))
```
| 2016 <class 'pandas.core.frame.DataFrame'>
| 2017 <class 'pandas.core.frame.DataFrame'>

### Modifying imports: true/false data
It's quite common to read spreadsheets that uses bool values as "Yes" or "No" and similars. One can set a parameter in __read_excel__ with __true_values__ and __false_values__ to set a list of values to be signed to True or False respectively.
```python
# Load data with Bollean dtypes and custom T/F values
bool_data = pd.read_excel("fcc_survey_booleans.xlsx",
						 dtype={"AttendedBootcamp": bool,
								"AttendedBootCampYesNo": bool,
								"AttendedBootCampTF": bool,
								"BootcampLoan": bool,
								"LoanYesNo": bool,
								"LoanTF": bool },
								true_values=["Yes"],
								false_values=["No"])
```
### Modifying imports: parsing dates
#### Date and Time Data
Date and time data are famous to give headaches to every data engineer, specially because there are a lot of ways to express them, add this with multiple timezones and you can have a chaotic program.
#### Pandas and Datetimes
[[Pandas]] interpret datetime columns as objects by default, one can specify that columns have datetimes with the __parse_dates__ argument. We can give to it a list of column names or numbers to parse, a list containing lists of columns to combine and parse or even a dictionary where keys are new column names and values are lists of columns to parse together.
Let's see the __fcc_survey.xlsx__:

| |Part1StartTime| Part1EndTime | Part2StartDate | Part2StartTime | Part2EndTime |
| -- |---|---|---|--:| --- |
| 0 | 2016-03-29 21:23:13 | 2016-03-29 21:24:53 | 2016-03-29 | 21:24:57 | 03292016 21:27:25 |

#### Parsing Dates

```python
# List columns of dates to parse
date_cols = ['Part1StartTime', 'Part1EndTime']

# Load file, parsing standard datetime columns
survey_df = pd.read_excel('fcc_survey.xlsx',
						 parse_dates=date_cols)

print(survey_df.dtypes)
```
|||
| :-- | --:|
| Part1StartTime | datetime64[ns] |
| Part1EndTime | datetime64[ns] |
| Part2StartDate | object |
| Part2StartTime | object|
| Part2EndTime | object |

| dtype: object

```python
# List columns of dates to parse
date_cols = ['Part1StartTime',
			'Part1EndTime',
			[['Part2StartDate', 'Part2StartTime']]]
# Load file, parsing standard and split datetime columns
survey_df = pd.read_excel('fcc_survey.xlsx',
						  parse_dates=date_cols)
print(survey_df.head(1))
```
| |Part1StartTime| Part1EndTime | Part2StartDate_Part2StartTime | Part2EndTime |
| -- |---|---|---| --- |
| 0 | 2016-03-29 21:23:13 | 2016-03-29 21:24:53 | 2016-03-29 21:24:57 | 03292016 21:27:25 |
| [1 rows x 98 columns]

But a better solution would be:

```python
# List columns of dates to parse
date_cols = {'Part1Start':'Part1StartTime',
			'Part1End':'Part1EndTime',
			'Part2Start':['Part2StartDate',
							'Part2StartTime']}
# Load file, parsing standard and split datetime columns
survey_df = pd.read_excel('fcc_survey.xlsx',
						 parse_dates=date_cols)

print(survey_df.Part2Start.head(1))
```
```cmd
| 0 2016-03-29 21:24:57
| Name: Part2Start, dtype: datetime64[ns]
```

#### Non-Standard Dates
__parse_dates__ doesn't work with non-standard datetime formats. One can use __pd.to_datetime()__a after loading data if __parse_dates__ won't work; it accepts two inputs: the data frame to be converted and the __format__ string.

#### Datetime Formatting
A standard way to describe the datetime string formatting with codes and characters. Refer to [strftime.org](www.strftime.org) for the full list.

| Code | Meaning | Example |
| :-- | :-- | --:|
| %Y | Year (4-digit) | 1999 |
| %m | Month (zero-padded) | 03 |
| %d | Day (zero-padded) | 01 |
| %H | Hour (24-hour clock) | 21 |
| %M | Minute (zero-padded) | 09 |
| %S | Second (zero-padded) | 05 |

#### Parsing Non-Standard Dates
```python
format_string = "%m%d%Y %H:%M:%S"
survey_df['Part2EndTime'] = pd.to_datetime(survey_df['Part2EndTime'],
										   format=format_string)
print(survey_df.Part2EndTime.head(1))
```

```cmd
| 0 2016-03-29 21:27:25
| Name: Part2EndTime, dtype: datetime64[ns]
```