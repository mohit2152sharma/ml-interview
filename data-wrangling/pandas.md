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
