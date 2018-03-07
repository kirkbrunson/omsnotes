---
title: "Statistical Analysis of Time Series Data"
date: 2018-03-06T20:09:17-05:00
draft: false
weight: 2
---


# Statistical Analysis of Time Series Data


Pandas makes it simple to perform statistical analysis on dataframes, which is extremely
important in determining different indicators and acting as inputs to the learning algorithms.

## Global statistics

For example, if you had a dataframe `df1` which had the closing prices for various stocks
over a given time period, you can retrieve an ndarray with the mean of the columns by just
calling `df1.mean()`.

Figure 2.1: Example output array for `mean()` (called on closing prices for January 2010
through December 2012)

In addition to mean, there around 32 other global statistics that are [available in pandas](https://pandas.pydata.org/pandas-docs/stable/computation.html#method-summary).


## Rolling statistics

Instead of doing analysis on the entire dataset, you might want to do a rolling analysis,
which only looks at certain snapshots of the data to sample. For example, you could have a
20-day moving mean, which you would calculate day-by-day by averaging the last 20 days’
data. In later sections, this moving average will be explained in more detail, but some
critical points of interest are when the moving average crosses the data.

###  Bollinger bands

Some analysts believe that significant deviations from the moving mean will result in movement
back towards the mean. If the price dips far below the mean, then it might be a buy
signal, whereas if it goes too high, it could indicate a time to sell. Bollinger bands are a
way of measuring this deviation.

Bollinger observed that if you look at the volatility of the stock, and if it’s too large, then
you discard the movements above and below the mean, but if it’s not, then it might be
worth paying attention to.

What he did was place two new moving means, one $2\sigma$ above, and another $2\sigma$ below the
moving average. If you look at deviations near to $2\sigma$, then they’re worth paying attention
to. If the price drops below $2\sigma$, and then rises back up through it, then it could be a buy
signal. (the price is moving back towards the average).

Conversely, if the price rises above $2\sigma$, then falls back down, it could be a sell signal.


### Computing rolling statistics in pandas

Pandas provides some methods to easily calculate rolling mean (`rolling mean()`) and rolling
standard deviation (`rolling_std()`).

Example: Calculating a 20-day rolling mean

```python
rm_SPY = pd.rolling_mean(df['SPY'], window=20)
```

![BollingerBands](/7646/images/fig2.2.png)


The Bollinger bands are calculated as follows:
```python
def get_bollinger_bands(rm, rstd):
    return rm + 2*rstd, rm - 2*rstd
```



## Daily returns

Daily returns are how much a stock’s price went up or down on a given day. They are an
extremely important statistic as they can be a good comparison between different stocks.

{{% math_center "dailyReturn[t] = \dfrac{price[t] - price[t - 1]}{price[t - 1]} = \dfrac{price[t]}{price[t - 1]} - 1" %}}


```python
daily_ret = (df / df.shift(1).values) - 1
daily_ret.ix[0,:] = 0
```


![DailyPriceVReturn](/7646/images/fig2.3.png)


## Cumulative Returns

Cumulative return is calculated by finding the gain from the beginning of the range to the
current time, i.e.

{{% math_center "CumlativeReturn[t] = \dfrac{price[t]}{price[0]} - 1" %}}

For example, if the price at the beginning was \$125, and the current price is \$142, then the
gain/cumulative return is $\dfrac{142}{125} - 1 = .136 = 13.6\%$

Cumulative returns are essentially the original dataset normalized.


## Incomplete data

People assume that financial data is extremely well-documented and that perfect data is
recorded minute by minute. They also believe that there are no gaps or missing data points.
However, for any particular stock, it might have different prices on different stock exchanges!
It’s difficult to know who’s right all the time. Also, not all stocks trade every day (they
might suddenly start trading or stop trading).

You might think you can just interpolate the data between breaks, but that’d cause statistical
errors and a side-effect of ”looking into the future” when doing analysis on that subset
of data. The better way of doing it to minimize error is to fill forward and backwards.


### Filling

To fix the ”NaN”/empty data, you can use filling to maintain the last known value until
known data is reached. For example, if you had a stock that didn’t have data until 2001 and
then stopped having data in 2006 but then started having data again in 2012, you could
fill forward from 2006-2012 and then fill backwards from 2001 back to whenever you want
your data to start.

Example: Filling in missing data using `fillna()`

```python
df = get_data(symbols, dates)
df.fillna(method="ffill", inplace=True)
df.fillna(method="bfill", inplace=True)
```

![NaNFilledData](/7646/images/fig2.4.png)



## Histograms and scatter plots

It’s difficult to draw conclusions directly from daily returns plots, so histograms make it
easier to see what’s going on. A histogram allows you to see how many occurrences of each
return happens relative to other returns. This histogram typically follows a Gaussian over
large periods of time.


### Histograms

From the histogram we can determine a few key statistics: mean, standard deviation, and
kurtosis. Kurtosis is a measure of how close the curve is to a Gaussian. In stock data, there
are usually more occurrences at high deviations (causing sort of ”fat tails”), which would
be reflected as a positive kurtosis. Skinny tails would mean a negative kurtosis.

![Kurtosis](/7646/images/fig2.5.png)


Example: Getting a histogram The histogram above was generated by just calling `hist()`
on the daily returns dataframe as such:

```python
daily_returns.hist(bins=20)
```

The bins parameter is essentially the resolution of the histogram. The domain is divided
into 20 bins and anything within those bins counts for that bin’s count in the histogram.
Other statistics like mean and standard deviation are easily calculated:

```python

mean = daily_returns['SPY'].mean()
print " mean =", mean

std = daily_returns['SPY'].std()
print "std deviation =", std
```

Which outputs:

```
mean = 0.000509326569142
std deviation = 0.0130565407719
```



We can now plot the mean and standard deviations on the plot to make analysis easier:

```python
plt.axvline(mean, color='w', linestyle='dashed', linewidth=2)
plt.axvline(mean+std, color='r', linestyle='dashed', linewidth=2)
plt.axvline(mean-std, color='r', linestyle='dashed', linewidth=2)
```

![HistogramMeans](/7646/images/fig2.6.png)

Showing this:
```python
print daily_returns.kurtosis()
>>> SPY 3.376644
```

which means that the data has fat tails since it’s positive.

The utility of these histograms comes when plotting them together. It’s easy to compare
multiple stocks in terms of their returns and volatility. If stock A’s curve is skewed more
positive and is thinner than stock B, then it has a low volatility with higher returns vs stock
B.

![HistogramComparison](/7646/images/fig2.7.png)

By looking at this chart, you can see that SPY and XOM are about the same in volatility

`$\sigma_{SPY} = 0.013057$`, `$\sigma_{XOM} = 0.013647$`. However, SPY would have higher returns since
`$R_{SPY} = 0.000509$` whereas `$R_{XOM} = 0.000151$`

### Scatter plots

Scatter plots are another way of visualizing the correlation between two stocks. Say you
had a dataframe with the daily returns for SPY and XYZ. If you took just the ndarray
containing the y-axis values, and then plotted SPY on the x-axis and XYZ on the y-axis,
you would see a bunch of points that might have a certain trend.

If you take a linear regression of this data, the slope would be called the beta ($\beta$) value. If
the $\beta$ value for SPY and XYZ is 1, it means that, on average, if SPY (the market) moves
up by 1%, then XYZ also moves up by 1%.

The y-intercept of the line is called $\alpha$. It describes how the stock on the y-axis performs
with respect the stock on the x-axis. If the $\alpha$ value of XYZ with respect to SPY is positive,
then, on average, XYZ is returning more than the market overall.


**Correlation:** If there isn’t any correlation in the dataset, then the linear regression doesn’t
tell you anything about the relationship. A common method for calculating the correlation is by finding the sample *Pearson correlation coefficient*, $r_{xy}$. It’s calculated by the following:


{{% math_center "r_{xy} = \dfrac{cov(X, Y)}{\sigma_X\sigma_Y} = \dfrac{\sum_{i=1}^{n} (x_i - \bar{x})(y_i - \bar{y})}{\sqrt{\sum_{i=1}^{n} (x_i - \bar{x})^2 }\sqrt{\sum_{i=1}^{n} (y_i - \bar{y})^2}}"%}}




where $cov$ is the covariance. In this case, $X$ would be the daily return for SPY and
$Y$ would be the daily return for XYZ. If $|r X,Y| = 1$, then the two are perfectly correlated
(either positively or negatively, depending on the sign of $\rho$). If $|r| < 1$, then there is possible
correlation, but a value closer to 1 means better correlation. If $r = 0$, there is no correlation.

Example: Plotting scatter plots, getting $\alpha$ and $\beta$ values, and determining correlation

```python
# scatter plot for XOM vs SPY
daily_ret.plot(kind='scatter', x='SPY', y='XOM', title="Scatterplot")
beta_XOM,alpha_XOM = np.polyfit(daily_ret['SPY'],daily_ret['XOM'],1)

plt.plot(daily_ret['SPY'], beta_XOM*daily_ret['SPY'] + alpha_XOM,'-',color='r')
plt.show()

# scatterplotforGLDvsSPY
daily_ret.plot(kind='scatter', x='SPY', y='GLD', title="Scatterplot")
beta_GLD, alpha_GLD = np.polyfit(daily_ret['SPY'], daily_ret['GLD'], 1)
plt.plot(daily_ret['SPY'], beta_GLD*daily_ret['SPY'] + alpha_GLD, '-', color='r')
plt.show()


print "BetaXOM: ", beta_XOM
print "AlphaXOM: ", alpha_XOM
print "BetaGLD: ", beta_GLD
print "AlphaGLD: ", alpha_GLD

# calculate correlation using pearson method
print "Correlationmatrix:\n", daily_ret.corr(method='pearson')

```

Which results in:
```
Beta XOM: 0.85753872112
Alpha XOM: -0.000285580653638
Beta GLD: 0.0663816850634
Alpha GLD: 0.000660583984316

Correlation matrix:
         SPY      XOM      GLD
SPY 1.000000 0.820423 0.074771
XOM 0.820423 1.000000 0.079401
GLD 0.074771 0.079401 1.000000
```


Looking at the $\beta$ values, you can see that XOM is more responsive to market changes, while
GLD is relatively unresponsive. However, GLD tends to perform better than the market
on average, since its $\alpha$ is positive.

But these values are meaningless without seeing what their correlations are. Looking at the
correlation matrix, XOM is pretty well correlated with SPY, whereas GLD has a very low
correlation, so changes GLD aren’t really correlated with changes in the market.

![CorrelationScatterPlots](/7646/images/fig2.8.png)


Looking at the plots, it’s easy to see that the points for XOM are more correlated and
match the line better than do those of GLD.

## Sharpe ratio and other portfolio statistics

The **portfolio** is the collection of all stocks currently owned by a person. It’s important to
know various statistics associated with the portfolio to make informed decisions on what to
sell/buy.

Suppose you begin with a portfolio p consisting of the following parameters:

```python

start_val = 1000000
start_date = '2009-01-01'
end_date = '2011-12-31'
symbols = ['SPY','XOM','GOOG','GLD']
allocs = [0.4,0.4,0.1,0.1] # @ beginning , 40% to SPY , 40% to XOM, etc
```


Now suppose we want to find the value of this portfolio day-by-day. If we normalize the
portfolio dataframe, we essentially have a dataframe containing cumulative returns for each
index. If we multiply this by allocs, we get returns scaled by each percentage of the total
portfolio. Then, multiply by start val to get each stock’s total value. Finally, take the sum
of this penultimate dataframe to get a single-column dataframe with the total portfolio
value at each point in time. In Python,

```python
# get cumulative returns
df = get_data(symbols, pd.date_range(start_date,end_date))
df = normalize(df)

# get changes for each stock by their percentages of the starting value
alloced = df * allocs

# get dollar value of changes
vals = alloced * start_val

# sum to get total value
portfolio_value = vals.sum(axis=1)
```

We may now compute various statistics on the portfolio’s value.


- **Daily returns:** Obviously, daily returns of the entire portfolio would be an important
statistic, as they indicate how the portfolio changes over time. For some statistics, we need
to get rid of the 0 at the beginning of the daily return or else it’ll throw off the values.

```python
daily_rets = daily_rets[1:]
```

- **Cumulative returns:** The total cumulative return of the portfolio is another interesting
statistic, as you can see if the overall gain was positive or negative.

```python
cum_ret = (port_val[-1]/port_val.ix[0,:]) - 1
```

- **Avg. and Std. Deviation:** These two are the main statistics that get thrown off by the
0 at the beginning. If it were there, the mean would be closer to 0, even though technically
0 isn’t actually one of the returns.

```python
avg_daily_ret = daily_rets.mean()
std_daily_ret = daily_rets.std()
```


###  Sharpe Ratio

The **Sharpe Ratio** is a metric that adjusts return for risk. It enables a quantitative way to
compare two stocks in terms of their returns and volatility. The Sharpe Ratio is calculated
based on the assumption that, *Ceteris paribus*,

- Lower risk is better
- Higher return is better

Being an economic indicator, it also takes into account the opportunity cost/return of
putting the money in a risk-free asset such as a bank account with interest.
A sort of risk-adjusted return may be calculated as follows:

{{% math_center "R_{adj} = \dfrac{R_p-R_f}{\sigma_p}" %}}


where $R_p$ is the portfolio return, $R_f$ is the risk-free rate of return, and $\sigma_p$ is the volatility
of the portfolio return.

This ratio is a sort of basis for how the Sharpe Ratio is calculated. The Sharpe Ratio is as
follows:

{{% math_center "S = \dfrac{E[R_p-R_f]}{std(R_p - R_f)}"%}}


Since we’re looking at past data, the expected value is actually the mean of the dataset, so
this becomes:

{{% math_center "S = \dfrac{\overline{(R_p - R_f)}}{std(R_p - R_f)}"%}}

One question is where $R_f$ comes from. There are three main ways of getting the data for
the risk-free rate:

1. The London Inter-Bank Offer Rate (LIBOR)
2. The interest rate on the 3-month Treasury bill
3. 0% (what people have been using recently...)

LIBOR changes each day, and the Treasury bill changes slightly each day, but interest in
bank accounts are typically paid in 6-month or yearly intervals. Using this simple trick,
you can convert the annual/biannual amount to a daily amount:

Suppose the yearly interest rate is $I$. If we start at the beginning of the year with a value
$P$, the new value after interest is paid will be $P'$. To find the equivalent daily interest value,
$I_{eq}$,

{{% math_center "P' = P(1 + I_{eq})^{252}"%}}
{{% math_center "P(1 + I) = P(1 + I_{eq})^{252}"%}}
{{% math_center "1 + I = (1 + I_{eq})^{252}"%}}
{{% math_center "(1 + I_{eq}) = \sqrt[252]{1+I}"%}}
{{% math_center "I_{eq} = \sqrt[252]{1+I} - 1"%}}


Therefore, $R_f$, the daily risk-free rate, is just $\sqrt[252]{1 + I} - 1$. The reason it’s 252 instead of
365 is because there are only 252 trading days in a year.


Since we’re treating $R_f$ as constant, the standard deviation in the denominator just becomes
$std(R_p)$, so the final equation for the Sharpe Ratio becomes:

{{% math_center "S = \dfrac{\overline{(R_p - R_f)}}{\sigma_{R_p}}"%}}

Sampling rate The Sharpe Ratio can vary widely depending on the sampling frequency.
Since $SR$ is an annual measure, any calculations that are done with samples more frequent
than yearly need to be scaled to get the annual ratio. To adjust the calculated Sharpe Ratio
to be ”annualized”, you just multiply by a factor of $\sqrt{\textrm{#samples per year}}$. So if you sample
daily, the Sharpe Ratio would become:

{{% math_center "S = \dfrac{\overline{(R_p - R_f)}}{\sigma_{R_p}} \sqrt{252}"%}}

Example: Given 60 days of data with the following statistics:

- $R_p = 10bps$
- $R_f = 2bps$
- $\sigma R_p = 10bps$,

what is the Sharpe Ratio? One bps is one hundredth of a percent.


## Optimizers

An optimizer can:
- Find minimum/maximum values of functions
- Build parameterized models based on data
- Refine allocations to stocks in portfolios
For example, say you have the function $f(x) = (x - 1.5)^2 + .5$, and you want to find the
minimum. It’s trivial to use calculus and find the minimum analytically, but you can’t
always do so if you don’t have an analytical model of the data. Let’s put this in Python:

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import scipy . optimize as spo


def f(x):
    y = (x - 1.5)**2 + .5
    print "x = {}, y = {}".format(x, y)
    return y


def test_run():
    guess = 2.0
    min_result = spo.minimize(f, guess, method='SLSQP', options={'disp': True})
    print " minima found at:"
    print "x = {}, y = {}".format(min_result.x, min_result.fun)


if __name__ == " __main__ ":
    test_run()
```

outputs:

```python
x = [2.], y = [0.75]
x = [2.], y = [0.75]
x = [2.00000001], y = [0.75000001]
x = [0.99999999], y = [0.75000001]
x = [1.5], y = [0.5]
x = [1.5], y = [0.5]
x = [1.50000001], y = [0.5]

Optimization terminated successfully. (Exit mode 0)
Current function value: [0.5]
Iterations: 2
Function evaluations: 7
Gradient evaluations: 2
minima found at:
    x = [1.5], y = [0.5]
```


![OptimizeExample](/7646/images/fig2.9.png)


### Pitfalls

Optimizers aren’t perfect, and since the method used above uses the gradient of the current
point to move to the next point, it can be tripped up by various abnormalities in the function
it’s trying to minimize, such as:

- *Flat ranges:* If a portion of the graph is flat (the slope is close to or is 0), then the
solver will either take a lot of iterations to solve for the minimum or it might not ever
be able to move to a new point, unless it can find a way out.
- *Discontinuities:* If there are discontinuities, the gradient might not be defined well
enough for the solver to continue.
- *Multiple minima:* Say you have a function $f(x) = x^4 − 2x^2 + x^2$. This function has
2 minima at $(0, 0)$ and $(1, 0)$. If the solver starts at $x = 1.5$, it’ll find the minimum
at $(1, 0)$, but it won’t ever reach the other minimum. Conversely, if the solver starts
at $x = −1.5$, it’ll find the minimum at $(0, 0)$. Therefore, it’s easy to get trapped in a
local minimum that may not be the actual global minimum.

![MultipleMinima](/7646/images/2minima.png)



###  Convex problems

A real-valued function $f(x)$ defined on an interval is called convex if the line segment between
any two points on the graph of $f(x)$ on that interval lies above the graph. Otherwise,
it’s called non-convex.

![Convex_v_Nonconvex](/7646/images/convex_nonconvex.png)

Left: a convex function. Right: a non-convex function. It is much easier to find the bottom of the surface in the convex function than the non-convex surface. (Source: [Reza Zadeh](https://www.matroid.com/blog/post/the-hard-thing-about-deep-learning))


### Building a parameterized model

If you have a set of data points representing rainfall and humidity that were gathered, you
might want to find a function that best fits those points. Say you wanted to fit a line
$f(x) = mx + b$ to the points. In this case, you can use linear algebra and find the leastsquares
solution, but you can also use an optimizer to find the best parameters $m$ and $b$.
What does ”best” mean? Well, we can devise a measure for the error for each point:

{{% math_center "e_i = (y_i - f(x_i))^2 = (y_i - (mx_i + b))^2"%}}

which is just the difference between the actual value and our model’s predicted value. The
reason it’s squared is to ensure that negative errors don’t reduce the total error when we
sum up every $e_i$.

{{% math_center "E = \sum_{i=1}^{n} (y_i - (mx_i + b))^2" %}}

Now that we have what we want to minimize, $E$, we can use a minimizer to find the best $m$
and $b$. To make the parameters nicer to work with in Python (and allow generalization to
higher degrees of polynomials), we’ll rename $m$ and $b$ to $C_0$ and $C_1$. Now, $f(x) = C_0x+C_1$.


For example in python:

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import scipy . optimize as spo

# line is a tuple (C0 , C1)
def error(line, data):
    return np.sum((data[:, 1]-(line[0]*data[:, 0]+line[1]))**2)


def fit_line(data, error_func):
    # initial guess for parameters
    l = np.float32([0, np.mean(data[:, 1])])
    return spo.minimize(error_func, l, args=(data,), method='SLSQP', options={'disp': True}).x


def test_run():
    original = np . float32([4, 2])
    print " original line : C0 = {} , C1 = {}". format(original[0], original[1])
    Xoriginal = np . linspace(0, 10, 40)
    Yoriginal = original[0] * Xoriginal + original[1]

    plt.plot(Xoriginal, Yoriginal, 'b--', linewidth=2.0, label="Originalline")

    # add some random noise to the data
    noise_sigma = 4.0
    noise = np.random.normal(0, noise_sigma, Yoriginal.shape)
    data = np.asarray([Xoriginal, Yoriginal+noise]).T

    plt.plot(data[:, 0], data[:, 1], 'go', label="Data points")

    l_fit = fit_line(data, error)
    print " Fitted line : C0 = {}, C1 = {}".format(l_fit[0], l_fit[1])
    plt.plot(data[:, 0], l_fit[0]*data[:, 0] + l_fit[1],
             'r- -', linewidth=2.0, label=" Fitted line ")

    plt.legend(loc='upperright')
    plt.show()


if __name__ == '__main__':
    test_run()

```


![parameterized_model](/7646/images/fig2.10.png)



## Portfolio Optimization


Now that wee have the tools to optimize a function, we can use it to optimize our portfolio!
We can choose to optimize/minimize/maximize various measures, such as daily returns,
cumulative returns, or Sharpe Ratio based on the percent allocation of all of the stocks in
the portfolio.

### Framing the problem

First we need three things:

1. a function, $f(x)$, to minimize
2. an initial guess for $x$
3. the optimizer

In our case, $x$ is actually the set of allocations for the stocks. Also, since we want to
maximize Sharpe Ratio, we need to multiply $f(x)$ by $-1$ to call the minimizer.



<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>

<script type="text/x-mathjax-config">
MathJax.Hub.Config({
  tex2jax: {
    inlineMath: [['$','$'], ['\\(','\\)']],
    displayMath: [['$$','$$'], ['\[','\]']],
    processEscapes: true,
    processEnvironments: true,
    skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
    TeX: { equationNumbers: { autoNumber: "AMS" },
         extensions: ["AMSmath.js", "AMSsymbols.js"] }
  }
});
</script>

<script type="text/x-mathjax-config">
  MathJax.Hub.Queue(function() {
    // Fix <code> tags after MathJax finishes running. This is a
    // hack to overcome a shortcoming of Markdown. Discussion at
    // https://github.com/mojombo/jekyll/issues/199
    var all = MathJax.Hub.getAllJax(), i;
    for(i = 0; i < all.length; i += 1) {
        all[i].SourceElement().parentNode.className += ' has-jax';
    }
});
</script>