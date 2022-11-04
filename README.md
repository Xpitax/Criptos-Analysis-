# Criptos-timeseries-Analysis

An analysis of cryptomonets; Bitcoin, Ethereum and Dogecoin. Scraping data from Twitter and Binance. Trying to demonstrate the unpredictability of cryptos through different statistical models (GARCH, ARIMA, Z Score)

An analysis of cryptomonets; Bitcoin, Ethereum and Dogecoin.
I scraped data on cryptomonets via the Binance API.
The objectives of the analysis were different:
- to demonstrate the unpredictability of cryptomonets using different statistical models (GARCH Model, ARIMA Model)
- to demonstrate that the trend of cryptocurrencies is sensitive to certain tweets depending on the topic and the person tweeting; 
the first step in this process was a Z Score Analysis to highlight the most obvious trend changes
the second step was scraping the tweets through a python library called Scweet
the last step was to match tweets with trend variability to demonstrate that the trend cold be, in part, influenced by those.

GetHistoricalData is a function that made me scrape the data from Binance about the pre-determinated currency and just the columns I was interest in. I just had to declair "client" and the nuber of days I wanted to scrape "971"
NaNcleaner is a function that was pointing out the NaN values as False and avoids to consider them
TypeChecker is a function that converts the data type in the type I need to process the rest of the analysis

I did, afterwards, a couple of views to understand the trend of the coins and get a first glimpse of the variability of the trend:
a line chart developed with matplotlib where I show close and lowest price of the coin throughout the timeseries.
an OHLC chart to get a new perspective on the market

The charts served me to filter out the outliers and thus highlight, in a another chart, the days of greatest rise or fall of the coin, for information purposes only.

Once the data was clean, I reduced my dataframe to a dictionary.
This step was necessary to store my data in MongoDB.
I set up MongoDB database in Ubunto instance and created a bridge connecting my machine with the virtual machine: I named the database "Crypto" and inside I loaded the three databases (collections) called BTC, ETH and DOGE respectively.
(((Classified as a NoSQL-type database, MongoDB moves away from the traditional table-based structure of relational databases in favour of JSON-style documents with dynamic schema (MongoDB calls the format BSON), making data integration of certain types of applications easier and faster.)))


THE STATISTICAL PART IN PARTICULAR:

GARCH Model,
I calculated the daily returns of the stock investment based on the percentage of the close price changes
The pct_change method will automatically calculate the percentage changes of the current day’s close price compared with the previous day. Please note the percentage changes can be either positive or negative depending on the direction of the price changes. The resulting percentage changes are stored in the dataframe new column, “Return”.
I plotted a line chart to visualize the daily returns over time.

Later I calculated the volatility of the stock in three levels: daily, monthly and annual.
I used std method to calculate the standard deviation of the daily return prices and the resulting values are assigned to a variable daily_volatility and display the output using the print statement.
I assumed there are 30 trading days per month and therefore the monthly volatility is computed by multiplying the square root of 30 with the daily volatility.
I assumed there are 365 trading days per month and therefore the annual volatility is computed by multiplying the square root of 365 with the daily volatility.

All the volatilities are quite high. This is the reason crypto investment is always accompanied by high risks and should never be traded at a random guess.

I built a GARCH (Generalized Auto Regressive Conditional Heteroskedasticity) model to predict the volatility of the cryptos based on the historical close prices and volatility.
I used the Python arch library to encapsulate the mathematical complexity into just a few lines of scripts.
Fetch the cripto daily return data into the arch_model function. I set the p and q to 1 and then leave the setting for mean, vol, and dist as default, which are ‘constant’, ‘GARCH’, and ‘normal’.
I fitted the Garch model and printed the result. The larger the resulting alpha value, the bigger the immediate volatility impact of the stock. On another hand, a larger beta infers a longer duration of the volatility impact.
I used the forecast method to make a 5-period ahead forecast (horizon=5) and print the output in the variance.

Create an empty list to hold the rolling prediction values. Set the test size to 365 days.
In each loop, a GARCH model is trained by the current training set with the parameters p and q set to 1. The current model is used to forecast volatility with a 1-time step (horizon=1) and then the predicted volatility variance is squared root. The final value (standard deviation) is appended to the rolling_predictions.
I constructed a Pandas series for the rolling_predictions and I created a line chart to display the rolling prediction of the volatility over the last 365 days.

In order to gain a better picture of how accurate our GARCH model in predicting the volatility, I created another subplot of daily return (true values) and overlay it with the rolling prediction line plot.

I consider the result not accurate enought due to the high volatility.


ARIMA Model,
A time series can be broken down into 3 components.

Trend: Upward & downward movement of the data with time over a large period of time (i.e. house appreciation)
Seasonality: Seasonal variance (i.e. an increase in demand for ice cream during summer)
Noise: Spikes & troughs at random intervals

Before applying any statistical model on a time series, I wanted to ensure it’s stationary:
The mean of the series should not increase over time.
The variance of the series should not be a function of time. This property is known as homoscedasticity.
Finally, the covariance of the ith term of the (i+m)th term should not be a function of time.
The three of them should be constant with the time to get the timeseries stationary.

There are two primary way to determine whether a given time series is stationary:
- Rolling Statistics: Plot the rolling mean and rolling standard deviation. The time series is stationary if they remain constant with time (with the naked eye look to see if the lines are straight and parallel to the x-axis).
- Augmented Dickey-Fuller Test: The time series is considered stationary if the p-value is low (according to the null hypothesis) and the critical values at 1%, 5%, 10% confidence intervals are as close as possible to the ADF Statistics.

I calculated than the Rolling Mean and Rolling Standard Deviation (Window = 12) on the close prices of the cryptos and I plotted them. I demonstrated that they are not stationary since they increase and decreas over time.

Afterwards I plotted the ADF Statistic. The three cryptos were far from the critical values and the p-value is greater than the threshold (0.05). Thus, we can conclude that the time series is not stationary.
get_stationarity is a function that compute and plot what I said so far all in once.

There are multiple transformations that I could have applied to a time series to render it stationary:
First step has been computing the log of the dependent variable that is as simple way of lowering the rate at which rolling mean increases.
I tried to transform the datas to get a good p value:
I subtracted the rolling mean.
I applied exponential decay.
I applied the time shifting, I subtracted every the point by the one that preceded it (null, (x1−x0), (x2−x1), (x3−x2), (x4−x3), …, (xn−xn−1))

I plotted all the three transformations using get_stationary function.
The one that performed better fot the three cryptos is the exponential decay: infact the p value was under the limit (< 0,05) and the the critical values at 1%, 5%, 10% confidence intervals were the closest to the ADF statistics.

*
AutoRegressive Model (AR)
Autoregressive models operate under the premise that past values have an effect on current values. AR models are commonly used in analyzing nature, economics, and other time-varying processes. As long as the assumption holds, we can build a linear regression model that attempts to predict value of a dependent variable today, given the values it had on previous days.
The order of the AR model corresponds to the number of days incorporated in the formula.

Moving Average Model (MA)
Assumes the value of the dependent variable on the current day depends on the previous days error terms.
Xt = μ+εt+θ1εt−1+...+θqεt−q
where μ is the mean of the series, the θ1, …, θq are the parameters of the model and the εt, εt−1,…, εt−q are white noise error terms. The value of q is called the order of the MA model.

Auto Regressive Moving Average (ARMA)
The ARMA model is simply the combination of the AR and MA models.

AutoRegressive Integrated Moving Average Model (ARIMA)
The ARIMA (aka Box-Jenkins) model adds differencing to an ARMA model. Differencing subtracts the current value from the previous and can be used to transform a time series into one that’s stationary.
*

Three integers (p, d, q) are typically used to parametrize ARIMA models.

p: number of autoregressive terms (AR order)
d: number of nonseasonal differences (differencing order)
q: number of moving-average terms (MA order)
I created and fitted an ARIMA model with AR of order 2, differencing of order 1 and MA of order 2.
Then, I could see how the model compares to the original time series.

Given that we have data going for every day going back 3 years and want to forecast the close prices for the next 2 years.
"results.plot_predict(1,1750)"

In the case of the ARIMA model, I found the case closest to stationarity with exponential decay.
In any case, this is not enough to make an adequate prediction; in fact, the prediction is by no means reliable as there are too many variables influencing the trend of cryptocurrencies


Z Score Model,
Clean the dataframe to work with z score.
function adder adds a certain number of columns
function deleter deletes a certain number of columns
function jump deletes a certain number of rows from the beginnng
function ma calculate the moving average
function volatility calculate the volatility

function z-score indicator helped me to compute the z score value.

The Z-score measures the current analyzed value relative to its mean in terms of how many standard deviation away. The Z-score is easily interpreted this way:

If the Z-score is 0, it indicates that the data point’s score is the same as the mean’s score.
If the Z-score is 1.0 would indicate a value that is one standard deviation above the mean.
If the Z-score is -1.0 would indicate a value that is one standard deviation below the mean.
To calculate the Z-score, we must use the below formula:
z = (x-μ)/σ

Afterwards I calculated the 21 period Z-score putting as lower barrier -2 and as upper barrier +2
I found several points corresponding to dates and I created a dataframe with index, date and z-score

I scraped data from twitter. Each tweet corresponding to the days of the Z-score analysis. 
I put everything into a dataframe to test my hypothesis.

It turned out that there is not too much correlation between tweets and cryptocurrency rises/falls; perhaps it is when the trend rises that something can be predicted in terms of tweets in the days before the rise
