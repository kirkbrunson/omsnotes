---
title: "Python"
date: 2018-03-06T20:09:09-05:00
draft: false
weight: 1
---

# Python: Numpy & Pandas


## Dataframes

A dataframe is a data structure in pandas that allows multiple datasets to be mapped to
the same indices. For example, a data frame that maps dates to closing prices could be:

|   | **SPY**| **AAPL**  | **GOOG**  | **GLD**  |
|---|---|---|---|---|
|  2000-01-09 | 101.01  | 50.89  | NaN  | NaN  |
|  2000-01-10 | 100.05  | 50.91  | NaN  | NaN  |
|  ... | ...  | ...  | ...  |  ... |
|  2015-12-31 | 200.89  | 600.25  | 559.50  | 112.37  |


The indices in this dataframe are the dates on the left, and the closing prices for that date
are stored in each column. The ”NaN”s appear because GOOG and GLD were not publicly
traded during those periods.


###  Reading CSVs into Dataframes

To begin using the dataframes, you need data first. Historical stock data from Yahoo is
provided in the form of a CSV file, which can be easily read into a dataframe using pandas’s
function read csv().

```python
import pandas as pd

def test_run():
    df = pd.read_csv("data/AAPL.csv")
    print df

if __name__ == "__main__":
    test_run()

```

This example reads in a CSV corresponding to the historic data for AAPL (Apple, Inc) into
the variable `df`. `df` is a DataFrame object, which means any DataFrame methods may be
used on it.


An example of a method that can be used is `max()`, which returns the maximum
value in the range.

```python
import pandas as pd

def get_max_close(symbol):
    df = pd.read_csv("data/{}.csv".format(symbol))
    return df ['Close'].max()

def test_run():
    for symbol in ['AAPL','IBM']
        print "Max close "
        print symbol, get_max_close(symbol)

if __name__ == "__main__":
    test_run()

```


### Plotting

Matplotlib can be used to plot the data in the dataframes, as pandas can conveniently tap into the matplotlib API. Plotting data in a dataframe is as simple as calling `plot()` on one of the series in the frame.

Example: Plotting the Adjusted Closea price of AAPL


```python
import pandas as pd
import matplotlib.pyplot as plt

def test_run():
	df = pd.read_csv('data/AAPL.csv')
	print df['Adj Close']

	df[['Adj Close', 'Close']].plot()
	plt.title('Comparison')
	plt.show()

if __name__ == '__main__':
	test_run()

```

|index|AdjClose|
|---|---|
|0|       669.79|
|1|       660.59|
|2|       662.74|
|3|       680.44|
|4|       676.27|
|...|     ...|
|3173|     24.60|
|3174|     24.96|

![Fig1](/7646/images/fig1.png)




### Issues
There are some issues with the data that need to be solved to effectively use it in the way we want.

- **Trading days:** The NYSE only trades for a certain number of days per year, which means that indexing by dates will return some results when the exchanges were not open. This poses problems for trying to pull out certain date ranges from the dataframe.
- **Multiple stocks:** One of the dataframe’s powers is to be able to contain multiple ranges, which means that we need to be able to retrieve multiple datasets and store them into the dataframe.
- **Date order:** The data in the Yahoo CSV are in reverse chronological order (most recent at the top), so any analysis on the dataframe will be going backwards in time, which is not ideal.


### Solution to the issues

To solve the trading days problem, we’ll use an Exchange-Traded Fund (ETF) called *SPY* (S&P 500) to serve as a basis for what days the stock market is open. The only days that exist in the dataset for this ETF are the days the stock market traded, so if we use this as a reference and use joining on the dataframes, we can recover data on only the days which had trading.


Example: Using joins to get only traded days

```python
start_date = '2010-01-22'
end_date = '2010-01-26'
dates = pd.date_range(start_date, end_date)
df1 = pd.DataFrame(index=dates)  # build empty dataframe

```


If we were to print out `df1`, the output would be:

```
Empty DataFrame
Columns : []
Index : [2010-01-22 00:00:00, 2010-01-23 00:00:00, 2010-01-25 00:00:00, 2010-01-26 00:00:00]
```


This empty dataframe will be the basis for the data we want to retrieve.
The next step is to join this dataframe with a dataframe with the data for SPY. This will
keep only indices of the SPY dataframe that also exist in the empty one.

Example: Reading in the new dataframe and joining them

```python
dfSPY = pd.read_csv("data/SPY.csv", index_col="Date", parse_dates=True, usecols=[ 'Date','Adj Close'], na_values =['nan'])
df1 = df1.join(dfSPY)
```

The output would now be:

```
           Adj Close
2010-01-22 104.34
2010-01-23 NaN
2010-01-24 NaN
2010-01-25 104.87
2010-01-26 104.43
```


To get rid of the ”NaN”s, you can call `dropna()` on the newly joined dataframe, but there is
a better way of joining them such that the ”NaN”s don’t appear in the first place. The join
type is called an inner join, which joins at the intersection of the two dataframes. This way,
only the dates which are in both will be kept as indices. Everything else will be thrown away.

Example: The inner join

```python
df1 = df1.join(dfSPY, how='inner')
```


### Multiple stocks

Reading in multiple stocks is as easy as just adding a for loop:

Example: Reading in multiple stocks into a single dataframe

```python
dfSPY = dfSPY.rename(columns={'Adj Close': 'SPY'})
df1 = df1.join(dfSPY, how='inner')

symbols = ['GOOG', 'IBM', 'GLD']
for symbol in symbols:
    df_tmp = pd.read_csv("data/{}.csv".format(symbol),
                         index_col="Date", 
                         parse_dates=True, 
                         usecols=['Date', 'Adj Close'], 
                         na_values=['nan'])

# rename to prevent name clash (multiple columns with same name)
df_tmp = df_tmp . rename(columns={'Adj Close': symbol})
df1 = df1.join(df_tmp, how='inner')
```


Here’s an example of reading and plotting multiple stocks’ closing price on one plot

Example: Reading and plotting multiple stocks

```python

import os
import pandas as pd
import matplotlib.pyplot as plt


def plot_selected(df, columns, start_ind, end_ind):
    print df.ix[start_ind: end_ind, columns]


plot_data(df.ix[start_ind:end_ind, columns])


def symbol_to_path(symbol, base_dir="data"):
    return os.path.join(base_dir, "{}. csv". format(str(symbol)))


def get_data(symbols, dates):
    df = pd.DataFrame(index=dates)

    if 'SPY' not in symbols:
        symbols.insert(0, 'SPY')

    for symbol in symbols:
        tmp = pd.read_csv(symbol_to_path(symbol),
                          index_col="Date",
                          parse_dates=True,
                          usecols=['Date', 'Close'],
                          na_values=['nan'])
        tmp = tmp.rename(columns={'Close': symbol})

        df = df.join(tmp)
        if symbol == 'SPY':
            df = df.dropna(subset=['SPY'])

    return df

def plot_data(df, title=" Stock prices"):
    ax = df.plot(title=title, fontsize=12)
    ax.set_xlabel("Date")
    ax.set_ylabel("Closing price")
    plt.show()

def test_run():
    dates = pd.date_range('2010-01-01', '2010-12-31')
    symbols = ['IBM ', 'GLD ']
    df = get_data(symbols, dates)
    plot_selected(df, ['SPY', 'IBM '], '2010-03-01', '2010-04-01')


if __name__ == '__main__':
    test_run()

```

![MultipleStocks](/7646/images/fig1.2.png)



### Normalizing

Sometimes when plotting, the values of a stock will be significantly different from the other
stocks such that it becomes difficult to tell some of them apart. Normalizing the data allows
all of them to start at the same point and then show divergences from the initial point,
making it easier to compare them at the same time.

Normalizing the dataframe is as simple as dividing the entire dataframe by its first row

Example: Normalizing a dataframe

```python
def normalize_data(df):
    df = df / df.ix[0 ,:]
    return df
```


![MultipleStocksNormalized](/7646/images/fig1.3.png)



## NumPy

The actual data in the dataframe is actually an ndarray in NumPy (A multidimensional
homogeneous array). That means we can do operations on the data using NumPy. For
example, if you have a dataframe `df1`, the ndarray would be extracted by doing

```python
nd1 = df1.values
```

Accessing a cell in the array is as simple as:

```python
val = nd1[row, col]
```

You can also access subarrays by indexing with the colon:

```python
sub = nd1[0:3, 1:3]
```

would capture the rectangular subarray from the first to the third rows and the second to
third columns.

### Indexing 

Note that the second part of the index is 1 past the actual index that will be
the last, so 0:3 only pulls out 0, 1, and 2. Like in MATLAB, you can pull out everything using just the colon. For example:

```python
sub = nd1[3 ,:]
```

would retrieve all columns of row 3.

### Negative indexing

To get the last index, you can use negative numbers (the last index
would be -1, second to last -2, etc.)

```python
sub = nd1[-1, 1:3]
```

would get columns 1,2 of the last row


### Boolean indexing/masking

Suppose we want to get the values in an array, `a`, which
are all less than the mean. NumPy’s masking feature makes it really intuitive, as all you
need to do is:

```python
lessThanMean = a[a < a.mean()]
```

The array `a < a.mean()` would be a boolean array, which might look like

```python
[[ True, True, False, False]]
```


### Assignment

Assigning values in an array is easy using the NumPy notation. For example,
say we wanted to replace the values in the first 2x2 square of `nd1` with the 2x2 square in
nd2 with columns 2 and 3, and rows 3, and 4. The operation would be:

```python
nd1[0:2, 0:2] = nd2[-2:, 2:4]
```

### Creating an array

Creating a numpy array is as easy as passing in a normal python list
into the array method:

```python
import numpy as np
print np.array([1, 2, 3])
```

Creating a 2D m x n array is as simple as passing in a m-long list of n-tuples.

```python
print np.array([(1, 2, 3), (4, 5, 6)])
```

would output

```
[[1 ,2 ,3]
 [4 ,5 ,6]]
 ```


### More initializers

You can also create arrays with certain initial values.

```python
np.empty((5, 3, 2))
```

initializes an ”empty” 5x3x2 dimensional array. The values in the array are actually whatever
was in the memory locations of the array pointers, so the output could look like
garbage.

```python
np.ones((5, 4), dtype=np.int)

```

creates a 5x4 array, where the value in each cell is the integer 1.

```python
np.random.random((5, 4))
```

creates a 5x4 array with random numbers from a uniform distribution in [0.0,1.0). An
example result could be:
```
[[ 0.82897637 0.36449978 0.91209931 0.96307279]
 [ 0.63777312 0.24482194 0.5817991 0.18043012]
 [ 0.85871221 0.98874123 0.68491831 0.53831711]
 [ 0.52908238 0.81083147 0.97440602 0.81032768]
 [ 0.98566222 0.38902445 0.16922005 0.0873198 ]]
```

Other methods or fields, such as `sum()` or `size()` can be looked up in [online documentation](https://docs.scipy.org/doc/numpy-dev/dev/).