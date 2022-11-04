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

GARCH,
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


ARIMA,


