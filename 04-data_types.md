---
layout: page
title: Programming with Python
subtitle: Data types and formats
minutes: 30
---

The format of individual columns and rows will impact analysis performed on a
dataset that is read into Python. For example, you can't perform mathematical
calculations on a string (character formatted data). This might seem obvious, but sometimes numeric values are read into python as strings. In this
situation, when you then try to perform calculations on the string-formatted
numeric data, you get an error.

In this lesson we will review ways to explore and better understand the
structure and format of our data.

> ## Learning Objectives {.objectives}

> * Learn about character and numeric data types.
> * Learn how to explore the structure of your data.
> * Understand NaN values and different ways to deal with them.


# Types of Data

How information is stored in a
DataFrame or a python object affects what we can do with it. There are two main types of data that we're explore in this lesson: numeric and character types.

# Numeric Data Types

Numeric data types include integers and floats. A **floating point** (known as a
float) number has decimal points even if that decimal point value is 0. For
example: 1.13, 2.0 1234.345. If we have a column that contains both integers and
floating point numbers, Pandas will assign the entire column to the float data
type so the decimal points are not lost.

An **integer** will never have a decimal point. Thus 1.13 would be stored as 1.
1234.345 is stored as 1234. You will often see the data type `Int64` in python
which stands for 64 bit integer. The 64 simply refers to the memory allocated to
store data in each cell which effectively relates to how many digits in can
store in each "cell". Allocating space ahead of time allows computers to
optimize storage and processing efficiency.

## Character Data Types

Strings, known as Objects in Pandas, are values that contain numbers and / or
characters. For example, a string might be a word, a sentence, or several
sentences. A Pandas object might also be a plot name like 'plot1'. A string can
also contain or consist of numbers. For instance, '1234' could be stored as a
string. As could '10.23'. However **numbers that are formatted as strings can not be used
for mathematical operations**!

> ## Pandas vs Python {.callout}
> Pandas and Python use slightly different names for data types:
>
> | Pandas Type | Native Python Type | Description |
> |-------------|--------------------|-------------|
> | object | string | The most general dtype. Will be assigned to your column if column has mixed types (numbers and strings). |
> | int64  | int | Numeric characters. 64 refers to the memory allocated to hold this character. |
> | float64 | float | Numeric characters with decimals. If a column contains numbers and NaNs(see below), pandas will default to float64, in case your missing value has a decimal. |
> | datetime64, timedelta[ns] | N/A (but see the [datetime] module in Python's standard library) | Values meant to hold time data. Look into these for time series experiments. |
>
> [datetime]: http://doc.python.org/2/library/datetime.html


## Checking the format of our data

Now that we're armed with a basic understanding of numeric and character data
types, let's explore the format of our survey data. We'll be working with the 
same `surveys.csv` dataset that we've used in previous lessons.

~~~python
# note that pd.read_csv is used because we imported pandas as pd
surveys_df = pd.read_csv("surveys.csv")
~~~

Remember that we can check the type of an object like this:

~~~python
type(surveys_df)
~~~

~~~{.output}
pandas.core.frame.DataFrame
~~~

Next, let's look at the structure of our surveys data. In pandas, we can check
the type of one column in a DataFrame using the syntax
`dataFrameName[column_name].dtype`:

~~~python
surveys_df['sex'].dtype
~~~
~~~{.output}
dtype('O')
~~~

A type 'O' just stands for "object", which in Pandas' world is a string
(characters).

~~~python
surveys_df['record_id'].dtype
~~~
~~~{.output}
dtype('int64')
~~~

The type `int64` tells us that python is storing each value within this column
as a 64 bit integer. We can use the `dat.dtypes` command to view the data type
for each column in a DataFrame (all at once).

~~~python
surveys_df.dtypes
~~~
~~~{.output}
record_id      int64
month          int64
day            int64
year           int64
plot           int64
species       object
sex           object
wgt          float64
dtype: object
~~~

Note that most of the columns in our survey data are of type `int64`. This means
that they are 64 bit integers. But the wgt column is a floating point value,
which means it contains decimals. The species and sex columns are objects, which
means they contain strings.


## Working With Integers and Floats

We've learned that computers store numbers in one of two ways: as integers or
as floating-point numbers (or floats). Integers are the numbers we usually count
with. Floats have fractional parts (decimal places).  

Let's next consider how
the data type can impact mathematical operations on our data. Addition,
subtraction and multiplication work on floats and integers as we'd expect:

~~~python
print 5+5
~~~
~~~{.output}
10
~~~
~~~python
print 24-4
~~~
~~~{.output}
20
~~~


However, division works differently. If we divide one integer by another integer,  the result is the quotient without the remainder. In the example below, 9 goes into 5 as an integer 0 times:

~~~python
print 5/9
~~~
~~~{.output}
0
~~~
~~~python
# 3 goes into 10, 3 times
print 10/3
~~~
~~~{.output}
3
~~~

If either part of the division is a float, python
returns a floating-point result:

~~~python
print '10.0/3 is:', 10.0/3
~~~
~~~{.output}
10.0/3 is: 3.33333333333
~~~

Python does this for historical reasons: integer operations were much
faster on early computers and this behavior is actually useful in a lot of
situations. It's still confusing though, so Python 3  produces a floating-point
answer when dividing integers. We're still using Python 2.7 in
this class though, so if we want 5/9 to give us the right answer, we have to
write it as 5.0/9, 5/9.0, 5.0/9.0.

Another way to create a floating-point answer is to explicitly tell the computer
that you desire one. This is achieved by **casting** one of the numbers as a float:

~~~python
print 'float(10)/3 is:', float(10)/3
~~~

> ## Casting and the order of operations {.challenge}
> 
> What happens if you type `float(10/3)`?

We can also convert a floating point number to an integer. Notice that Python by
default rounds down when it converts from floating point to integer.

~~~python
a = 7.33
# convert a to integer
int(a)
~~~
~~~{.output}
7
~~~
~~~python
b = 7.89
int(b)
~~~
~~~{.output}
7
~~~

# Working with our survey data

We can use casting to modify the format of values within our data. For instance, we could convert the `record_id` field to floating point
values:

~~~python
# convert the record_id field from an integer to a float
surveys_df['record_id'] = surveys_df['record_id'].astype('float64')
surveys_df['record_id'].dtype
~~~
~~~{.output}
dtype('float64')
~~~

What happens if we try to convert weight values to integers?

~~~python
surveys_df['wgt'].astype('int')
~~~

Notice that this throws a value error: `ValueError: Cannot convert NA to
integer`. If we look at the `wgt` column in the surveys data we notice that
there are NaN (**N**ot **a** **N**umber) values. *NaN* values are undefined
values that cannot be represented mathematically. Pandas, for example, will read
an empty cell in a CSV or Excel sheet as a NaN. NaNs have some desirable
properties: if we were to average the `wgt` column without replacing our NaNs,
Python would know to skip over those cells.

~~~python
surveys_df['wgt'].mean()
42.672428212991356
~~~

## Missing Data Values - NaN

Dealing with missing data values is always a challenge. It's sometimes hard to
know why values are missing - was it because of a data entry error? Or data that
someone was unable to collect? Should the value be 0? We need to know how
missing values are represented in the dataset in order to make good decisions.
If we're lucky, we have some metadata that will tell us more about how null
values were handled.

In some disciplines, like Remote Sensing, missing data values are
often defined as -9999. Having a bunch of -9999 values in your data could really
alter numeric calculations. In spreadsheets, cells are often left empty where no
data are available. Python will replace those missing values with
NaN by default. However, it is good practice to  intentionally mark
cells that have no data with a no data value. That way there are no questions
in the future when you (or someone else) explores your data.

### Where Are the NaN's?

Let's explore the NaN values in our data a bit further. We can use the tools we
learned so far to figure out how many rows contain NaN values for
weight. We can also create a new subset from our data that only contains rows
with weight values > 0 (ie. select meaningful weight values):

~~~python
len(surveys_df[pd.isnull(surveys_df.wgt)])
# how many rows have wgt values?
len(surveys_df[surveys_df.wgt> 0])
~~~

We can replace all NaN values with zeroes using the `.fillna()` method (after
making a copy of the data so we don't lose our work):

~~~python
df1 = surveys_df
# fill all NaN values with 0
df1['wgt'] = df1['wgt'].fillna(0)
~~~

However NaN and 0 yield different analysis results. The mean value when NaN
values are replaced with 0 is different from when NaN values are simply thrown
out or ignored.

~~~python
df1['wgt'].mean()
38.751976145601844
~~~

We can fill NaN values with any value that we chose. The code below fills all
NaN values with a mean for all wgt values.

~~~python
 df1['wgt'] = df['wgt'].fillna(df['wgt'].mean())
~~~

We could also chose to create a subset of our data, only keeping rows that do
not contain NaN values.

The point is to make conscious decisions about how to manage missing data. This
is where we think about how our data will be used and how these values will
impact the scientific conclusions made from the data.

Python gives us all of the tools that we need to account for these issues. We
just need to be cautious about how the decisions that we make impact our
results.