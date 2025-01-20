# Table of Contents
```toc
```


## Importing Data from Flat Files

###  Introduction to Flat Files
[[Pandas]]  is a major library for analytics used for creating bidimensional <span style="color: #cc4444">data frames </span>. It's easy to connect to databases, APIs, files and other data sources.

#### Loading CSVs
Let's say that we have a [[CSV]] file __called us_tax_data_2016.csv__, with a sample :
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

```
| True
```
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

Most of the data engineering job is to look out for errors and data that is missing. [[Pandas]] offers many alternatives to handle various scenarios. As [[Python]] is not a static typed language,<span style = "color: #cc4444"> pandas automatically infers column data types </span>.

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

```cmd
| b'Skipping line 3: expected 147 fields, saw 148\n'
1
```

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
```
[5 rows x 98 columns]
```
#### Loading Select Columns and Rows
Similarly as seen previously in the loading of [[CSV]] files, the __read_excel()__ function has the parameters __nrows__ to limit the number of rows imported, __skiprows__ to ignore the __n__ first rows and __usecols__ to select the columns used. But as a [[Excel]] orients their columns using letters sequence, the parameter __usecols__ accept a similar syntax that we would see in the spreadsheet.
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

```cmd
| 2016 <class 'pandas.core.frame.DataFrame'>
| 2017 <class 'pandas.core.frame.DataFrame'>
```

### Modifying imports: true/false data
It's quite common to read spreadsheets that uses bool values as "Yes" or "No" and similars. One can set a parameter in __read_excel__ with __true_values__ and __false_values__ to set a list of values to be signed to True or False respectively.

#### Setting Custom True/False Values
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

## Importing Data From Databases
### Introduction to Databases
A database is a <span style = "color: #cc4444" >organized collection of data </span>which is stored and accessed electronically.
#### Relational Databases
A [[relational database]] organizes the data in entities, or <span style = "color: #cc4444"> tables </span>, that are composed by <span style = "color: #cc4444"> columns </span> and <span style = "color:#cc4444"> rows</span>. 
- Each column represent a attribute of that entity and each row represents an instance of that entity.
- Tables can be linked to each other using unique keys. They can have different kinds of relations and each column has their own data type.
- [[SQL]] (Structured Query Language) is a query system to interact with structured databases and can have multiple dialects.

#### Creating a Database Engine

[[Sqlalchemy]] is library in python that allows us to <span style = "color: #cc4444"> connect to databases </span>. First, we can the function __create_engine()__ to make an engine that handles database connections. It requires a database URL to connect to, e.g.: SQLite URL format: ```sqlite:///filename.db```

#### Querying Databases
```pd.read_sql(query, engine)``` loads a data frame with [[Pandas]] and [[Sqlalchemy]]  ```engine``` and a ```query```.

#### Getting Data from a Database

```python
import pandas as pd
from sqlalchemy import create_engine

# Create database engine to manage connections
engine = create_engine("sqlite:///date.db")

# Load entire weather table by table name
weather = pd.read_sql("weather", engine)

# Load entine weather table with SQL
weather2 = pd.read_sql("SELECT * FROM weather", engine)

print(weather.equals(weather2))
```

```
| True
```

### Refining import with SQL queries

#### SQL and pandas
Together with the <span style = "color: #cc4444">SELECT </span> and <span style = "color: #cc4444">FROM </span> clauses, you can use the WHERE to filter the records by a combination of boolean conditions like:
```python
import pandas as pd
from sqlalchemy import create_engine

# Create database engine
engine = create_engine("sqlite:///data.db")
# Wrtie query to get records from Brooklyn
query = """ SELECT *
				FROM hdp311calls
			WHERE complaint_type = 'WATER LEAK'
				OR complaint_type = 'PLUMBING';"""
# Query the database
brooklyn_calls = pd.read_sql(query, engine)
print(brooklyn_calls.complaint_type.unique())
```
```| ['WATER LEAK', 'PLUMBING']```
### More Complex SQL queries
#### Distinct
You can get use the <span style = "color: #cc4444"> DISTINCT </span> with SELECT to get unique values for one or more columns:
```sql
SELECT DISTINCT [column names] FROM [table];
```
#### Aggregate Functions
The aggregate functions let's you get descriptive statistics from tables. The first four <span style = "color: #cc4444">AVG</span>, <span style = "color: #cc4444">SUM</span>, <span style = "color: #cc4444">MAX</span> and <span style = "color: #cc4444">MIN</span> takes only one column name.
e.g.: getting the average of a numeric column:
```sql
SELECT AVG(tmax) FROM weather;
```
The <span style = "color: #cc4444">COUNT</span> aggregate function returns the number of rows that meet the query condition. One can combine the ```count``` with ```distinct``` to get the number of unique values in a columns:
```sql
# Counting all rows
SELECT COUNT(*) FROM [table_name];
# Counting unique values of the column
SELECT COUNT(DISTINCT [column_names]) FROM [table_name];
```
#### GROUP BY
You can summarize data by categories using <span style = "color: #cc4444">GROUP BY</span> statement. One can also use aggregate functions to get statistics from each category.
```sql
/* Get counts of plumbing calls by borough */
SELECT borough, 
		COUNT(*)
	FROM hpd311calls
	WHERE complaint_type = 'PLUMBING'
	GROUP BY borough;
```
### Loading Multiple Tables with Joins
As a characteristic of a [[relational database]], you can join tables by their relation and work with multiple tables.
#### Joining Tables
Use the dot notation (```table.column```) when working with multiple tables, use the relations available between the tables to join them. <span style = "color: #cc4444">Make sure join keys are the same data type</span>!
You can use all aggregating, filtering and grouping that we have seen so far:
```sql
SELECT hpd311calls.borough,
		COUNT(*),
		boro_census.total_population,
		boro_census.housing_units
	FROM hpd311calls
		JOIN boro_census
		ON hpd311calls.borough = boro_census.borough
	GROUP BY hpd311calls.borough;;
```
|     | borough       | count | total_population | housing_units |
| --- |:------------- | -----:| ----------------:| -------------:|
| 0   | BRONX         | 29874 |          1455856 |        524488 |
| 1   | BROOKLYN      | 31722 |          2635121 |       1028383 |
| 2   | MANHATTAN     | 20196 |          1653877 |        872645 |
| 3   | QUEENS        | 11384 |          2339280 |        850422 | 
| 4   | STATEN ISLAND |  1322 |           475948 |        179179 |
## Import JSON Data and Working with APIs

### Introduction to JSON

Javascript Object Notation, or [[JSON]], is a common web data format, not tabular and organized into collections of objects. 

#### Reading JSON Data

```read_json()``` takes a string path to JSON or JSON data as a string, you can use ```dtype``` to specify the data types and  ```orient``` to flag uncommon JSON data layouts.

Since JSON is a non tabular data format, pandas can automatically handle only common orientations.

### Record Orientation
The most common JSON orientation is a list of dictionaries, being each dictionary a register, each key a column and their respective values:
```JSON
[
	{
		"age_adjusted_death_rate":"7.6",
		"death_rate":"6.2",
		"deaths":"32",
		"leading_cause":"Accidents Except Drug Posioning (V01-X39, X43, X45-X59, Y85-Y86)",
		"race_ethnicity": "Asian and Pacific Islander",
		"sex": "F",
		"year": "2007"
	},
	{
	"age_adjusted_death_rate": "8.1",
	"death_rate": "8.3",
	...
```
#### Column Orientation
A more space-efficient than record-oriented JSON:
```JSON
{
	"age_adjusted_death_rate": {
		"0": "7.6",
		"1": "8.1",
		"2": "7.1",
		"3": ".",
		"4": ".",
		"5": "7.3",
		...

```
#### Specifying Orientation
The **split** oriented data:
```JSON
{
	"columns": [
		"age_adjusted_death_rate",
		"death_rate",
		"deaths",
		"leading_cause",
		"race_ethnicity",
		"sex",
		"year"
	],
	"index": [...],
	"data": [
		[
			"7.6",
		...
```
Let's say that the above JSON is inside a file called __nyc_death_causes.json__.
```python
import pandas as pd
death_causes = pd.read_json("nyc_death_causes.json",
						   orient="split")
print(death_causes.head())
```
|     | age_adjusted_death_rate | death_rate | deaths | leading_cause            | race_ethnicity             | sex | year |
| --- | ----------------------- | ---------- | ------ | ------------------------ | -------------------------- | --- | ---- |
| 0   | 7.6                     | 6.2        | 32     | Accidents Except Drug... | Asian and Pacific Islander | F   | 2007 |
| 1   | 8.1                     | 8.3        | 87     | Accidents Except Drug... | Black Non-Hispanic         | F   | 2007 |
| 2   | 7.1                     | 6.1        | 71     | Accidents Except Drug... | Hispanic                   | F   | 2007 |
| 3   | .                       | .          | .      | Accidents Except Drug... | Not Stated/Unknown         | F   | 2007 | 
| 4   | .                       | .          | .      | Accidents Except Drug... | Other Race/ Ethnicity      | F   | 2007 |
``` [5 rows x 7 columns]```

### Introduction to APIs
An Application Programming Interface or [[API]] defines how an application communicates with other programs.

#### Requests

A request is a way to send and get data from websites, you can use the library [[Requests]] in [[Python]] to have a http request.

```requests.get(url_string)``` to get data from a URL. The ```params``` keyword takes a dictionary of parameters and values to customize API requests while ```headers``` takes a dictionary that can provide user authentication to the API.
After the request, the user can receive the ```response``` object that contains data and metadata - one can parse it with JSON with ```response.json()```.    

#### Making Requests

```python
import requests
import pandas as pd

api_url = "https://api.yelp.com/v3/business/search"
# Set up parameter dictionary according to documentation
params = {
			"term": "bookstore",
			"location": "San Francisco"
}

# Set up header dictionary w/API key according to documentation
headers = {"Authorization": "Bearer {}".format(api_key)}

# Call the API
response = requests.get(api_url,
					   params=params,
					   headers=headers)
```

#### Parsing Responses 

```python
# Isolate the JSON data from the response object
data = response.json()
print(data)
```
``` 
{'business':[{'id':'_rbF2ooLcMRA7Kh8neIr4g', 'alias': 'city-lights-bookstore-san-francisco',...
```
```python
# Load business data to a data frame
bookstores = pd.DataFrame(data['businesses'])
print(bookstores.head(2))
```

|     | alias                                | ... | url                                               |
| --- | ------------------------------------ | --- | ------------------------------------------------- |
| 0   | city-lights-bookstore-san-francisco  | ... | https://www.yelp.com/biz/city-lights-bookstore... |
| 1   | alexander-book-company-san-francisco | ... | https://www.yelp.com/biz/alexander-book-comp...   | 
```[2 rows x 16 columns]```

### Working with nested JSONs

#### NESTED JSONs

[[JSON]]s contain object with attribute-value pairs and it is said that a JSON is nested when the value itself is an object.

```JSON
{
	"total": 8228,
	"businesses": [
	{
		"rating" : 4,
		"price": "$",
		"is_closed": false,
		"categories":[
		{
			"alias": "coffee",
			"title": "Coffee & Tea"
		}
		],
		"review_count": 1738,
		"name": "Four Barrel Coffee",
		"url":"https://www.yelp.com/biz/four-barrel-coffee-san-francisco",
		"coordinates": {
			"latitude": 37.7670169511878,
			"longitude": -122.42184275
		},
		"image_url": "https://s3media2.fl.yelpcdn.com/bphoto/MmgtASP3l/o.jpg",
		"location": {
			"city": "San Francisco",
			"country": "US",
			"address2": "",
			"address3": "",
			"state": "CA",
			"address1": "375 Valencia St",
			"zip_code": "94103"
		},
	...
```

```python
# Print columns containing nested data
print(bookstores[['categories','coordinates','location']].head(3))
```
|     | categories                                       |
| --- | ------------------------------------------------ |
| 0   | [{'alias': 'bookstores', 'title': 'Bookstores'}] |
| 1   | [{'alias': 'bookstores', 'title': 'Bookstores'}] |
| 2   | [{'alias': 'bookstores', 'title': 'Bookstores'}] |                                                   |

|     | coordinates                                                 |
| --- | ----------------------------------------------------------- |
| 0   | [{'latitude': '37.7975997924805', 'longitude': '-1..... '}] |
| 1   | [{'latitude': '37.7975992345564', 'longitude': '-1..... '}] |
| 2   | [{'latitude': '37.7qw3456377345', 'longitude': '-1..... '}] |
 
#### Loading NESTED JSON Data
```pandas.io.json``` submodule has tools for reading and writing JSON. Needs its own ```import``` statement.

```python
import pandas as pd
import requests
from pandas import json_normalize

# Set up headers, parameters, and API endpoint
api_url = "https://api.yelp.com/v3/businesses/search"
headers = {"Authorization": "Bearer {}".format(api_key)}
params = {"term": "bookstore",
		 "location": "San Francisco"}

# Make the API call and extract the JSON data
response = requests.get(api_url,
					   headers=headers,
					   params=params)
data = response.json()
```

```python
# Flatten data and load to data frame, with _ separators
bookstores = json_normalize(data['businesses'], sep="_")
print(list(bookstores))
```
```CMD
['alias',
'categories',
'coordinates_latitude',
'coordinates_longitude',
...
'location_address1',
'location_address2',
'location_address3',
'location_city',
...
'url']
```
#### Deeply Nested Data
```python
print(bookstores.categories.head())
```
```CMD
0 [{'alias': 'bookstores', 'title': 'Bookstores'}]
1 [{'alias': 'bookstores', 'title': 'Bookstores'..
2 [{'alias': 'bookstores', 'title': 'Bookstores'}]
3 [{'alias': 'bookstores', 'title': 'Bookstores'}]
4 [{'alias': 'bookstores', 'title': 'Bookstores'...
Name: categories, dtype: object
```

One can use ```json_normalize()``` with:
- ```record_path```: string/list of string attributes to nested data
- ```meta```: list of other attributes to load to data frame
- ```meta_prefix```: string to prefix to meta column names
```python
# Flatten categories data, bring in business details
df = json_normalize(data['businesses'],
				   sep="_",
				   record_path="categories",
				   meta=['name',
						'alias',
						'rating',
						['coordinates','latitude']],
					meta_prefix='biz_')
print(df.head(2))
```
|     | alias      | title      | biz_name               | biz_alias                            | biz_rating | biz_coordinates_latitude | 
| --- | ---------- | ---------- | ---------------------- | ------------------------------------ | ---------- | ------------------------ | 
| 0   | bookstores | Bookstores | City Lights Bookstore  | city-lights-bookstore-san-francisco  | 4.5        | 37.797600                | 
| 1   | bookstores | Bookstores | Alexander Book Company | alexander-book-company-san-francisco | 4.5        | 37.788585                |

### Combining multiple datasets

#### Appending
You can add rows from one data frame to another with the ```append()``` function, e.g.: ```df1.append(df2)``` and setting the parameter ```ignore_index``` to ```True``` in order to renumber the rows.
```python
# Get first 20 bookstore results
params = {'term':'bookstore',
		 'location':'San Francisco'}
first_results = requests.get(api_url,
							headers=headers,
							params=params).json()
first_20_bookstores = json_normalize(first_results['businesses'],
									sep="_")
print(first_20_bookstores.shape)
```
```(20,24)```
```python
# Get next 20 bookstore results
params['offset'] = 20
next_results = requests.get(api_url,
							headers=headers,
							params=params).json()
next_20_bookstores = json_normalize(next_results['businesses'],
								   sep="_")

# Put bookstore datasets together, renumber rows
bookstores = first_20_bookstores.append(next_20_bookstores,
										ignore_index=True)
print(bookstores.name)
```
```CMD
0 City Lights Bookstore
1 Alexander Book Company
2 Boderlands Books
... ...
38 Eastwind Books & Arts
39 My Favorite
Name: name, dtype: object
```
#### Merging
You can use the ```merge()``` function that works as the JOIN function from [[SQL]]. Use the parameter ```on``` if names of the columns are the same in both data frames, use ```left_on``` and ```right_on``` if key names differ. <span style = "color: #cc4444">The key columns should be the same data type</span>. You can also use the ```how``` parameter to set the type of the join - ```left```, ```right```, ```inner```, etc.
```python
call_counts.head()
```
|     | created_date | call_counts |
| --- | ------------ | ----------- |
| 0   | 01/01/2018   | 4597        |
| 1   | 01/02/2018   | 4362        |
| 2   | 01/03/2018   | 3045        |
| 3   | 01/04/2018   | 3374        |
| 4   | 01/05/2018   | 4333        |
```python
weather.head()
```
|     | date       | tmax | tmin |
| --- | ---------- | ---- | ---- |
| 0   | 12/01/2017 | 52   | 42   |
| 1   | 12/02/2017 | 48   | 39   |
| 2   | 12/03/2017 | 48   | 42   |
| 3   | 12/04/2017 | 51   | 40   |
| 4   | 12/05/2017 | 61   | 50   | 
```python
merged = call_counts.merge(weather,
						  left_on = "created_date",
						  right_on = "date")
print(merged.head())
```

|     | created_date | call_counts | date       | tmax | tmin |
| --- | ------------ | ----------- | ---------- | ---- | ---- |
| 0   | 01/01/2018   | 4597        | 01/01/2018 | 19   | 7    |
| 1   | 01/02/2018   | 4362        | 01/02/2018 | 26   | 13   |
| 2   | 01/03/2018   | 3045        | 01/03/2018 | 30   | 16   |
| 3   | 01/04/2018   | 3374        | 01/04/2018 | 29   | 19   |
| 4   | 01/05/2018   | 4333        | 01/05/2018 | 19   | 9    | 

The default ```merge()``` behavior is to return only values that are in both datasets.