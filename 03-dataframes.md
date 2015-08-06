---
layout: page
title: Programming with Python
subtitle: Indexing, slicing and subsetting DataFrames with pandas
minutes: 30
---

> ## Learning Objectives {.objectives}
>
> *  Learn about 0-based indexing in Python.
> *  Learn about numeric vs. label based indexes.
> *  Learn how to select subsets of data from a DataFrame using Slicing and
>    Indexing methods.
> *  Understand what a boolean object is and how it can be used to "mask" or
>    identify particular sets of values within another object.

We will continue to use the surveys dataset that we worked with in the last
exercise. You can reopen it like this:

~~~python
# first make sure pandas is loaded
import pandas as pd
# read in the survey csv
surveys_df = pd.read_csv("surveys.csv")
~~~

# Indexing & slicing in Python

We often want to work with subsets of a **DataFrame** object instead of the whole thing. There are
different ways to break the data up into pieces, either by using labels (column headings),
numeric ranges or specific x,y index locations.


## Selecting data using labels (column headings)

We use square brackets `[]` to select a subset of a Python object. For example,
we can select all of the data from the column `species` in the `surveys_df`
DataFrame by requesting it by name:

~~~python
surveys_df['species']
~~~

Using the column heading as an attribute of the Python object gives the same output:

~~~{.python}
surveys_df.species
~~~

We can also create a new DataFrame to contain only the subset of data we are interested in:

~~~python
# create an object named surveys_species that only contains the species column
surveys_species = surveys_df['species']
~~~

We can also use a list of column names as an index to extract all of those columns from the DataFrame. The columns of this subset of data will be in the same order as the list of column names. This is very useful when we need to reorganize our data!

~~~python
# select the species and plot columns from the DataFrame
surveys_df[['species', 'plot']]
# what happens when you flip the order?
surveys_df[['plot', 'species']]
~~~

> ## Unknown labels {.callout}
> If a column name is not contained in the DataFrame, an exception
> (error) will be raised.
>
> ~~~{.python}
> # what happens if you ask for a column that doesn't exist?
> surveys_df['speciess']
> ~~~

## Extracting range based subsets: Slicing


> ## 0-based indexing {.callout}
> Remember that Python uses 0-based indexing. This means that the first element in an object is located at position
> 0. This is different from other tools like R and Matlab that index elements
> within objects starting at 1.
> 
> ~~~python
> # Create a list of numbers:
> a = [1,2,3,4,5]
> ~~~

Just as we did with lists in the previous lesson, we use the square brackets `[]` to extract a subset of a DataFrame:

~~~python
# select the first, second and third rows from the surveys variable
surveys_df[0:3]
# select the first 5 rows (rows 0,1,2,3,4)
surveys_df[:5]
# select the last element in the list
surveys_df[-1:]
~~~

We can also use slicing to re-assign different values to some subset of the DataFrame. Like lists, DataFrames are mutable objects, meaning that they can be changed in place. Before continuing, let's make a 
copy of our DataFrame to play with so we don't accidentally modify the original imported data.

> ## Referencing objects vs copying objects in Python {.callout}
> We might think that we would create a fresh copy of the `surveys_df` objects when we 
> type `surveys_copy = surveys_df`, but this is not cloning our DataFrame into a new object with a new name. 
> It instead created a new variable `surveys_copy` that refers to the **same** object the variable `surveys_df` refers to. This means that there is only one object that can be reached by two different names. If we were
> to modify that object using one variable name (for example, `surveys_copy[0:3]=0`), it would change for the
> other variable name as well (because we would have modified the one and only object!).

~~~python
# copy the surveys dataframe so we don't modify the original DataFrame
surveys_copy = surveys_df.copy()

# set the first three rows of data in the DataFrame to 0
surveys_copy[0:3] = 0
~~~

## Slicing subsets of rows and columns in Python

We can select specific ranges of our data in both the row and column directions
using either label or integer-based indexing. We have to use one of two methods,
depending on what we use as indices:

- `loc`: indexing via *labels*
- `iloc`: indexing via *integers*

To select a subset of rows AND columns from our DataFrame, we can use the `iloc`
method. For example, we can select month, day and year (columns 2, 3 and 4 if we
start counting at 1), like this:

~~~python
surveys_df.iloc[0:3, 1:4]
~~~

~~~{.output}
   month  day  year
0      7   16  1977
1      7   16  1977
2      7   16  1977
~~~

Notice that we asked for a slice from 0:3 and this yielded 3 rows of data. When you
ask for 0:3, you are actually telling python to start at index 0 and select rows
0, 1, 2 **up to but not including 3**.


Let's explore some other ways to index and select subsets of data:

~~~python
# select all columns for rows of index values 0 and 10
surveys_df.loc[[0,10], :]
# select only three columns of the first row
surveys_df.loc[0, ['species', 'plot', 'wgt']]
~~~

>## Integers for both loc and iloc {.callout}
> When using `loc`, both labels and integers
> *can* be used, but the integers will refer to the index label of the row and not its position (index labels are not necessarily continuous!).


We can also select a specific data value according to the specific row and
column location within the data frame using the `iloc` method:

~~~python
# dat.iloc[row,column]
surveys_df.iloc[2,6]
~~~
~~~{.output}
'F'
~~~

> ## Test your understanding of indices {.challenge}
> 
> 1. What do each of these commands do?
> ~~~python
> surveys_df[0:3]
> surveys_df[:5]
> surveys_df[-1:]
> ~~~
>
> 2. What is the difference between these two commands?
> ~~~{.python}
> surveys_df.iloc[0:4, 1:4]
> surveys_df.loc[0:4, 1:4]
> ~~~

## Subsetting Data Using Criteria

We can also select a subset of our data using some criteria. For example, we can
select all rows that have a year value of 2002:

~~~python
surveys_df[surveys_df.year == 2002]
~~~
~~~{.output}
       record_id  month  day  year  plot species  sex  wgt
33320      33321      1   12  2002     1      DM    M   44
33321      33322      1   12  2002     1      DO    M   58
33322      33323      1   12  2002     1      PB    M   45
33323      33324      1   12  2002     1      AB  NaN  NaN
33324      33325      1   12  2002     1      DO    M   29
33325      33326      1   12  2002     2      OT    F   26
33326      33327      1   12  2002     2      OT    M   24

...          ...    ...  ...   ...   ...     ...  ...  ...
35541      35542     12   31  2002    15      PB    F   29
35542      35543     12   31  2002    15      PB    F   34
35543      35544     12   31  2002    15      US  NaN  NaN
35544      35545     12   31  2002    15      AH  NaN  NaN
35545      35546     12   31  2002    15      AH  NaN  NaN
35546      35547     12   31  2002    10      RM    F   14
35547      35548     12   31  2002     7      DO    M   51
35548      35549     12   31  2002     5     NaN  NaN  NaN

[2229 rows x 8 columns]
~~~

We can also select all rows that do *not* contain the year 2002.

~~~python
surveys_df[surveys_df.year != 2002]
~~~

We can use sets of multiple criteria, too:

~~~python
surveys_df[(surveys_df.year >= 1980) & (surveys_df.year <= 1985)]
~~~

> ## Python Syntax Cheat Sheet {.callout}
>
> Use can use the operators below when querying data from a Python object. Experiment
> with selecting various subsets of the data using and combining these operators:
>
> * Equals: `==`
> * Not equals: `!=`
> * Greater than, less than: `>` or `<`
> * Greater than or equal to `>=`
> * Less than or equal to `<=`
> 
> * bitwise AND `&`
> * bitwise OR `|`


> ## Subset puzzles {.challenge}
>
> 1. Select a subset of rows in the `surveys_df` DataFrame that contain data from
>    the year 1999 *and* weight values less than or equal to 8. How
>    many columns did you end up with? What did your neighbor get?
> 2. You can use the `isin` method in Python to query a DataFrame based upon a
>    list of values like this:
>    `surveys_df[surveys_df['sex'].isin([listGoesHere])]`. Use the `isin` function
>    to find all plots that contain species of sex "Z" *or* sex "R" *or* sex "P" in
>    the surveys DataFrame. How many records contain these values?
> 3. Create a query that finds all rows with a weight value > or equal to 0.
> 4. The `~` symbol in Python can be used to return the OPPOSITE of the selection you specified. 
> It is equivalent to **is not in**. Write a query that selects all rows that are not of sex "M" or "F" in the data.


# Using Masks

A mask can be useful to locate where a particular subset of values exist or
don't exist. Masks are `BOOLEAN` objects. Booleans can only take the values of `true` or `false`:

~~~python
# set x to 5
x = 5
# what does this return?
x > 5
~~~
~~~{.output}
False
~~~
~~~{.python}
# what about this?
x == 5
~~~
~~~{.output}
True
~~~

When Python creates a mask, it performs the comparison operation for each value in the object. The mask is a new object that is the same shape as
the original object but with a True or False value for each index location.

Let's identify all locations in the survey data that have
null (missing or NaN, for Not-a-Number) data values. We can use the `isnull` method to do this.
Each cell that has a null value will be assigned a value of `True` in the new mask:


~~~python
pd.isnull(surveys_df)
~~~
~~~{.output}
      record_id  month    day   year   plot species    sex    wgt
0         False  False  False  False  False    True  False   True
1         False  False  False  False  False    True  False   True
2         False  False  False  False  False   False  False   True
3         False  False  False  False  False   False  False   True
4         False  False  False  False  False   False  False   True
5         False  False  False  False  False   False  False   True
6         False  False  False  False  False   False  False   True
7         False  False  False  False  False   False  False   True
8         False  False  False  False  False   False  False   True
9         False  False  False  False  False   False  False   True
10        False  False  False  False  False   False  False   True
11        False  False  False  False  False   False  False   True


[35549 rows x 8 columns]
~~~

To select the rows where there are null values, we can use 
the mask as an index to subset the data:

~~~python
# Use the .any method to select the rows (axis=1) with one or more NaN
surveys_df[pd.isnull(surveys_df).any(axis=1)]
~~~

Note that there are many null or NaN values in the `wgt` column of our DataFrame.
We will explore different ways of dealing with these later.

We can also run `isnull` on a particular column. We are using the Boolean
object as an index and then asking python to select rows that have a `NaN` value
for weight:

~~~python
emptyWeights = surveys_df[pd.isnull(surveys_df).any(axis=1)]['wgt']
~~~


> ## Test your logic {.challenge} 
>
> 1. Create a new DataFrame that only contains observations with sex values that
>    are *not* female or male. Assign each sex value in the new DataFrame to a
>    new value of 'x'. Determine the number of null values in the subset.
> 2. Create a new DataFrame that contains only observations that are of sex male
>    *or* female and where weight values are greater than 0. Create a stacked bar
>    plot of average weight by plot with male vs female values stacked for each
>    plot.
