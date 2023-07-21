# pandas

1. **How do you check which version of `pandas` you have installed?**

    The python packages have a `__version__` attribute which we can use to see their version. If we are in python interpretor, I can do the following:

    ```python
    import pandas
    print(pandas.__version__)
    ```
    Or if I am in terminal and have `pip` installed, I can run the command `pip show pandas` and it will show me the `pandas` version and other related information about it.

2. **What's the difference between a `series` and a `dataframe`?**

    A series is a one dimensional array. A dataframe is a two dimensional array. It has a row and a column. Dataframe is made of multiple series i.e. when you combine multiple series (either by row or by column), you get a dataframe.

    Both have indexes. `index` labels or identifies data in dataframe or series. And it enables, efficient data retrieval and manipulation. It can be thought of as a unique identifier of a data. In series, an index uniquely identifies a data. Dataframe has two indexes, one for rows and one for columns.

3. **You are given a series `S` and you are trying index it with a label `"some_index"`, should you use `.loc` or `.iloc` and what would it return?**

    Since it's a index label, I should use `.loc` because it's indexing by label name whereas `.iloc` is indexing by label position. The return value can be a scalar value or it can be a series. If there are not duplicates of `"some_index"` in index labels, then it will return a scalar value, otherwise it will return a series. For example:

    ```python
    >>> S = pd.Series([1,2,3,4,5], index=['a', 'b', 'c', 'c', 'c'])
    >>> S
    a    1
    b    2
    c    3
    c    4
    c    5
    dtype: int64
    >>> S.loc['a']
    1
    >>> S.loc['c']
    c    3
    c    4
    c    5
    dtype: int64
    >>> type(S.loc['a'])
    <class 'numpy.int64'>
    >>> type(S.loc['c'])
    <class 'pandas.core.series.Series'>
    ```
4. **What are the different ways you can use to save memory on a dataframe (or reduce the memory footprint of a dataframe)?**

    1. Make sure, that `categorical` columns are not stored as `string` or `object` type. 

    ```python
    import pandas as pd 

    # to convert to category type
    some_column.astype('category')

    # to order categories 
    some_column.astype('category').reorder_categories(values=[*sorted_categories], ordered=True)

    # we can also create ordered categories using pd.CategoricalDtype
    ordered_categories = pd.CategoricalDtype(values=[*sorted_categories], ordered=True)
    some_column.astype(ordered_categories)
    ```
    2. By default, `pandas` will store the numeric column in `Int64` and float column in `float64` type. If we have numeric columns which don't have large numbers, we can store them in smaller type like `int8` or `int16`. But we should be cautious about the range of the column, for example, `int8` can store numbers only from `0` to `255`, for any number larger than `255` it will throw an error.
