# Financial_Planning
Challenge_5

Financial Planning with APIs and Simulations
In this Challenge, you’ll create two financial analysis tools by using a single Jupyter notebook:

Part 1: A financial planner for emergencies. The members will be able to use this tool to visualize their current savings. The members can then determine if they have enough reserves for an emergency fund.

Part 2: A financial planner for retirement. This tool will forecast the performance of their retirement portfolio in 30 years. To do this, the tool will make an Alpaca API call via the Alpaca SDK to get historical price data for use in Monte Carlo simulations.

You’ll use the information from the Monte Carlo simulation to answer questions about the portfolio in your Jupyter notebook.

###### Import the required libraries and dependencies
import os
import requests
import json
import pandas as pd
from dotenv import load_dotenv
import alpaca_trade_api as tradeapi
import warnings
warnings.filterwarnings('ignore')

from MCForecastTools import MCSimulation
from alpaca_trade_api.rest import TimeFrame
%matplotlib inline

###### Load the environment variables from the .env file
#by calling the load_dotenv function
load_dotenv()

Part 1: Create a Financial Planner for Emergencies
Evaluate the Cryptocurrency Wallet by Using the Requests Library
In this section, you’ll determine the current value of a member’s cryptocurrency wallet. You’ll collect the current prices for the Bitcoin and Ethereum cryptocurrencies by using the Python Requests library. For the prototype, you’ll assume that the member holds the 1.2 Bitcoins (BTC) and 5.3 Ethereum coins (ETH). To do all this, complete the following steps:

1. Create a variable named monthly_income, and set its value to 12000.
###### The monthly amount for the member's household income
monthly_income = 12000

2. Use the Requests library to get the current price (in US dollars) of Bitcoin (BTC) and Ethereum (ETH) by using the API endpoints that the starter code supplies.
###### Using the Python requests library, make an API call to access the current price of BTC
btc_response = requests.get(btc_url).json()

###### Use the json.dumps function to review the response data from the API call
###### Use the indent and sort_keys parameters to make the response object readable
print(json.dumps(btc_response, indent=2, sort_keys=True))

###### Using the Python requests library, make an API call to access the current price ETH
eth_response = requests.get(eth_url).json()

###### Use the json.dumps function to review the response data from the API call
###### Use the indent and sort_keys parameters to make the response object readable
print(json.dumps(eth_response, indent=2, sort_keys=True))

3. Navigate the JSON response object to access the current price of each coin, and store each in a variable.

Hint Note the specific identifier for each cryptocurrency in the API JSON response. The Bitcoin identifier is 1, and the Ethereum identifier is 1027.

###### Navigate the BTC response object to access the current price of BTC
btc_price = btc_response["data"]["1"]["quotes"]["USD"]["price"]

###### Print the current price of BTC
print(btc_price)
btc_price = 44657.0

###### Navigate the BTC response object to access the current price of ETH
eth_price = eth_response["data"]["1027"]["quotes"]["USD"]["price"]

###### Print the current price of ETH
print(eth_price)
eth_price = 3150.69

4. Calculate the value, in US dollars, of the current amount of each cryptocurrency and of the entire cryptocurrency wallet.
###### Compute the current value of the BTC holding 
btc_value = btc_price * btc_coins

###### Print current value of your holding in BTC
print(btc_value)
btc_value = 53588.4

###### Compute the current value of the ETH holding 
eth_value = eth_price * eth_coins

###### Print current value of your holding in ETH
print(eth_value)
eth_value = 16698.657

###### Compute the total value of the cryptocurrency wallet
###### Add the value of the BTC holding to the value of the ETH holding
total_crypto_wallet = btc_value + eth_value

###### Print current cryptocurrency wallet balance
print(total_crypto_wallet)
total_crypto_wallet = 70287.057

## Evaluate the Stock and Bond Holdings by Using the Alpaca SDK
In this section, you’ll determine the current value of a member’s stock and bond holdings. You’ll make an API call to Alpaca via the Alpaca SDK to get the current closing prices of the SPDR S&P 500 ETF Trust (ticker: SPY) and of the iShares Core US Aggregate Bond ETF (ticker: AGG). For the prototype, assume that the member holds 110 shares of SPY, which represents the stock portion of their portfolio, and 200 shares of AGG, which represents the bond portion. To do all this, complete the following steps:

1. In the Starter_Code folder, create an environment file (.env) to store the values of your Alpaca API key and Alpaca secret key.

2. Set the variables for the Alpaca API and secret keys. Using the Alpaca SDK, create the Alpaca tradeapi.REST object. In this object, include the parameters for the Alpaca API key, the secret key, and the version number.
###### Set the variables for the Alpaca API and secret keys
alpaca_api_key = os.getenv("ALPACA_API_KEY")
alpaca_secret_key = os.getenv("ALPACA_SECRET_KEY")
display(type(alpaca_api_key))
display(type(alpaca_secret_key))

###### Create the Alpaca tradeapi.REST object
alpaca = tradeapi.REST(
    alpaca_api_key,
    alpaca_secret_key,
    api_version="v2")
    
3. Set the following parameters for the Alpaca API call:

- tickers: Use the tickers for the member’s stock and bond holdings.

- timeframe: Use a time frame of one day.

- start_date and end_date: Use the same date for these parameters, and format them with the date of the previous weekday (or 2020-08-07). This is because you want the one closing price for the most-recent trading day.

###### Set the tickers for both the bond and stock portion of the portfolio
tickers = ["SPY", "AGG"]

###### Set timeframe to 1D 
timeframe = "1Day"

###### Format current date as ISO format
###### Set both the start and end date at the date of your prior weekday 
###### This will give you the closing price of the previous trading day
###### Alternatively you can use a start and end date of 2020-08-07

start_date = pd.Timestamp("2020-08-07", tz="America/New_York").isoformat()
end_date = pd.Timestamp("2020-08-07", tz="America/New_York").isoformat()

4. Get the current closing prices for SPY and AGG by using the Alpaca get_barset function. Format the response as a Pandas DataFrame by including the df property at the end of the get_barset function.
###### Use the Alpaca get_barset function to get current closing prices the portfolio
###### Be sure to set the `df` property after the function to format the response object as a DataFrame
shares_data = {"shares":[110,200]}
portfolio = pd.DataFrame(shares_data, index=tickers)
portfolio

###### Be sure to set the `df` property after the function to format the response object as a DataFrame
###### Review the first 5 rows of the Alpaca DataFrame

portfolio_df = alpaca.get_bars(tickers, timeframe, start=start_date , end=end_date).df

AGG = portfolio_df[portfolio_df["symbol"] == "AGG"].drop("symbol", axis=1)
SPY = portfolio_df[portfolio_df["symbol"] == "SPY"].drop("symbol", axis=1)

portfolio_df = pd.concat([AGG, SPY], axis=1, keys=["AGG", "SPY"])

portfolio_df.head()

5. Navigating the Alpaca response DataFrame, select the SPY and AGG closing prices, and store them as variables.
###### Access the closing price for AGG from the Alpaca DataFrame
###### Converting the value to a floating point number
agg_close_price = float(portfolio_df["AGG"]["close"])

###### Print the AGG closing price
print(agg_close_price)
agg_close_price = 334.57

6. Calculate the value, in US dollars, of the current amount of shares in each of the stock and bond portions of the portfolio, and print the results.
###### Calculate the current value of the bond portion of the portfolio
agg_value = agg_close_price * portfolio.loc["AGG"]["shares"]

###### Print the current value of the bond portfolio
print(f"The current value of the {portfolio.loc['AGG']['shares']} AGG shares is ${agg_value:0.2f}")
The current value of the 200 AGG shares is $23886.00

###### Calculate the current value of the stock portion of the portfolio
spy_value = spy_close_price * portfolio.loc["SPY"]["shares"]

###### Print the current value of the stock portfolio
print(f"The current value of the {portfolio.loc['SPY']['shares']} SPY shares is ${spy_value:0.2f}")
The current value of the 110 SPY shares is $36802.70

###### Calculate the total value of the stock and bond portion of the portfolio
total_stocks_bonds = agg_value + spy_value

###### Print the current balance of the stock and bond portion of the portfolio
print(f"The current value of the entire portfolio is ${(total_stocks_bonds):0.2f}")
The current value of the entire portfolio is $60688.70

###### Calculate the total value of the member's entire savings portfolio
###### Add the value of the cryptocurrency walled to the value of the total stocks and bonds
total_portfolio = total_stocks_bonds + total_crypto_wallet

###### Print current cryptocurrency wallet balance
print(total_portfolio)
total_portfolio = 130975.757

## Evaluate the Emergency Fund
In this section, you’ll use the valuations for the cryptocurrency wallet and for the stock and bond portions of the portfolio to determine if the credit union member has enough savings to build an emergency fund into their financial plan. To do this, complete the following steps:

1. Create a Python list named savings_data that has two elements. The first element contains the total value of the cryptocurrency wallet. The second element contains the total value of the stock and bond portions of the portfolio.
###### Consolidate financial assets data into a Python list
###### Review the Python list savings_data
savings_data = [total_crypto_wallet, total_stocks_bonds]
                        
print(savings_data)
savings_data = [70287.057, 60688.7]

2. Use the savings_data list to create a Pandas DataFrame named savings_df, and then display this DataFrame. The function to create the DataFrame should take the following three parameters:

- savings_data: Use the list that you just created.

- columns: Set this parameter equal to a Python list with a single value called amount.

- index: Set this parameter equal to a Python list with the values of crypto and stock/bond.

###### Create a Pandas DataFrame called savings_df 
savings_df = pd.DataFrame(savings_data, columns=["amount"], index=["crypto", "stock/bond"])

###### Display the savings_df DataFrame
print(savings_df)
crypto      70287.057
stock/bond  60688.700

3. Use the savings_df DataFrame to plot a pie chart that visualizes the composition of the member’s portfolio. The y-axis of the pie chart uses amount. Be sure to add a title.
###### Plot the total value of the member's portfolio (crypto and stock/bond) in a pie chart
savings_df.plot.pie(y="amount", title="Composition of the member's portfolio")

4. Using Python, determine if the current portfolio has enough to create an emergency fund as part of the member’s financial plan. Ideally, an emergency fund should equal to three times the member’s monthly income. To do this, implement the following steps:

Step.4.1 # Create a variable named emergency_fund_value, and set it equal to three times the value of the member’s monthly_income of $12000. (You set this earlier in Part 1).
###### Create a variable named emergency_fund_value
emergency_fund_value = monthly_income * 3

Step.4.2 #Create a series of three if statements to determine if the member’s total portfolio is large enough to fund the emergency portfolio:

If the total portfolio value is greater than the emergency fund value, display a message congratulating the member for having enough money in this fund.

Else if the total portfolio value is equal to the emergency fund value, display a message congratulating the member on reaching this important financial goal.

Else the total portfolio is less than the emergency fund value, so display a message showing how many dollars away the member is from reaching the goal. (Subtract the total portfolio value from the emergency fund value.)

Step.4.2: Create a Python list named savings_data that has two elements. The first element contains the total value of the cryptocurrency wallet. The second element contains the total value of the stock and bond portions of the portfolio.
###### Evaluate the possibility of creating an emergency fund with 3 conditions:

if total_portfolio > emergency_fund_value:
    print(f"Congratulations! You have enough money in your emergency fund.")
elif total_portfolio == emergency_fund_value:
    print(f"Congratulations!, You havereaching this important financial goal.")
elif total_portfolio < emergency_fund_value:
    print(f"You are ${emergency_fund_value - total_portfolio:0.2f} away from your emergency fund goal, continue saving your monthly income to reach your goal.")
    
Congratulations! You have enough money in your emergency fund.

# # Part 2: Create a Financial Planner for Retirement
Create the Monte Carlo Simulation
In this section, you’ll use the MCForecastTools library to create a Monte Carlo simulation for the member’s savings portfolio. To do this, complete the following steps:

Step 1: Make an API call via the Alpaca SDK to get 3 years of historical closing prices for a traditional 60/40 portfolio split: 60% stocks (SPY) and 40% bonds (AGG).
###### Set start and end dates of 3 years back from your current date
###### Alternatively, you can use an end date of 2020-08-07 and work 3 years back from that date 
start_date = pd.Timestamp('2017-08-07', tz='America/New_York').isoformat()
end_date = pd.Timestamp('2020-08-07', tz='America/New_York').isoformat()

###### Set number of rows to 1000 to retrieve the maximum amount of rows
###### Using 1000 will only return 756 rows of AGG and 242 rows of SPY.
limit_rows = 2000 

###### Use the Alpaca get_barset function to make the API call to get the 3 years worth of pricing data
###### The tickers and timeframe parameters should have been set in Part 1 of this activity 
###### The start and end dates should be updated with the information set above
###### Remember to add the df property to the end of the call so the response is returned as a DataFrame
df_stock_data = alpaca.get_bars(tickers,timeframe,start=start_date,end=end_date,limit=limit_rows).df

AGG2 = df_stock_data[df_stock_data["symbol"] == "AGG"].drop("symbol", axis=1)
SPY2 = df_stock_data[df_stock_data["symbol"] == "SPY"].drop("symbol", axis=1)

df_stock_data = pd.concat([AGG2, SPY2], axis=1, keys=["AGG", "SPY"])

###### Display both the first and last five rows of the DataFrame
display(df_stock_data.head())
display(df_stock_data.tail())

Step 2: Run a Monte Carlo simulation of 500 samples and 30 years for the 60/40 portfolio, and then plot the results.
###### Configure the Monte Carlo simulation to forecast 30 years cumulative returns
###### The weights should be split 40% to AGG and 60% to SPY.
###### Run 500 samples.
Monte_Carlo_thirty_year = MCSimulation(portfolio_data = df_stock_data, weights = [.40,.60], num_simulation = 500,num_trading_days = 252 * 30)

###### Review the simulation input data
Monte_Carlo_thirty_year.portfolio_data

###### Run the Monte Carlo simulation to forecast 30 years cumulative returns
Monte_Carlo_thirty_year.calc_cumulative_return()

###### Visualize the 30-year Monte Carlo simulation by creating an
###### overlay line plot
line_plot = Monte_Carlo_thirty_year.plot_simulation()

Step 3: Plot the probability distribution of the Monte Carlo simulation.
###### Visualize the probability distribution of the 30-year Monte Carlo simulation 
###### by plotting a histogram
dist_plot = Monte_Carlo_thirty_year.plot_distribution()

Step 4: Generate the summary statistics for the Monte Carlo simulation.
###### Save the results as a variable
Monte_Carlo_summary_statistics = Monte_Carlo_thirty_year.summarize_cumulative_return()

###### Review the 30-year Monte Carlo summary statistics
print(Monte_Carlo_summary_statistics)

## Analyze the Retirement Portfolio Forecasts
Using the current value of only the stock and bond portion of the member's portfolio and the summary statistics that you generated from the Monte Carlo simulation, answer the following question in your Jupyter notebook:

- What are the lower and upper bounds for the expected value of the portfolio with a 95% confidence interval?
**Answer** The 95% CI lower bounds expected value is 2.488112, the upper bounds expected calue is 43.023505.

###### Print the current balance of the stock and bond portion of the members portfolio
total_portfolio = 130975.757 

###### Use the lower and upper `95%` confidence intervals to calculate the range of the possible outcomes for the current stock/bond portfolio
ci_lower_thirty_cumulative_return = Monte_Carlo_summary_statistics[8] * 130975.757
ci_upper_thirty_cumulative_return = Monte_Carlo_summary_statistics[9] * 130975.757

###### Print the result of your calculations

print(f"There is a 95% chance that an initial investment of $130,963.828 in the portfolio"
    f" over the next 30 years will end within in the range of"
    f" ${ci_lower_thirty_cumulative_return: .2f} and ${ci_upper_thirty_cumulative_return: .2f}.")
    
There is a 95% chance that an initial investment of $130,963.828 in the portfolio over the next 30 years will end within in the range of $ 325882.31 and $ 5635036.08.

## Forecast Cumulative Returns in 10 Years
The CTO of the credit union is impressed with your work on these planning tools but wonders if 30 years is a long time to wait until retirement. So, your next task is to adjust the retirement portfolio and run a new Monte Carlo simulation to find out if the changes will allow members to retire earlier.

For this new Monte Carlo simulation, do the following:

- Forecast the cumulative returns for 10 years from now. Because of the shortened investment horizon (30 years to 10 years), the portfolio needs to invest more heavily in the riskier asset—that is, stock—to help accumulate wealth for retirement.

- Adjust the weights of the retirement portfolio so that the composition for the Monte Carlo simulation consists of 20% bonds and 80% stocks.

###### Configure a Monte Carlo simulation to forecast 10 years cumulative returns
###### The weights should be split 20% to AGG and 80% to SPY.
###### Run 500 samples.
Monte_Carlo_ten_year = MCSimulation(portfolio_data = df_stock_data, weights = [.20,.80], num_simulation = 700,num_trading_days = 252 * 10)

###### Review the simulation input data
Monte_Carlo_ten_year.portfolio_data

- Run the simulation over 500 samples, and use the same data that the API call to Alpaca generated.
###### Run the Monte Carlo simulation to forecast 10 years cumulative returns
Monte_Carlo_ten_year.calc_cumulative_return()


###### Visualize the 10-year Monte Carlo simulation by creating an
###### overlay line plot
line_plot = Monte_Carlo_ten_year.plot_simulation()

###### Visualize the probability distribution of the 10-year Monte Carlo simulation 
###### by plotting a histogram
dist_plot = Monte_Carlo_ten_year.plot_distribution()

###### Generate summary statistics from the 10-year Monte Carlo simulation results
###### Save the results as a variable
Monte_Carlo_ten_year_summary_statistics = Monte_Carlo_ten_year.summarize_cumulative_return()

###### Review the 10-year Monte Carlo summary statistics
print(Monte_Carlo_ten_year_summary_statistics)

- Based on the new Monte Carlo simulation, answer the following questions in your Jupyter notebook:

- Using the current value of only the stock and bond portion of the member's portfolio and the summary statistics that you generated from the new Monte Carlo simulation, what are the lower and upper bounds for the expected value of the portfolio (with the new weights) with a 95% confidence interval?

###### Print the current balance of the stock and bond portion of the members portfolio

print(f"The 95% confidence interval lower bounds expected value is $ 0.845073 and the upper bounds expected calue is $ 7.603268.")

The 95% confidence interval lower bounds expected value is $ 0.845073 and the upper bounds expected calue is $ 7.603268.

###### Use the lower and upper `95%` confidence intervals to calculate the range of the possible outcomes for the current stock/bond portfolio

ci_lower_ten_cumulative_return = Monte_Carlo_ten_year_summary_statistics[8] * 130975.757
ci_upper_ten_cumulative_return = Monte_Carlo_ten_year_summary_statistics[9] * 130975.757

###### Print the result of your calculations

print(f"There is a 95% chance that an initial investment of $130,963.828 in the portfolio"
    f" over the next 10 years will end within in the range of"
    f" ${ci_lower_ten_cumulative_return: .2f} and ${ci_upper_ten_cumulative_return: .2f}.")

There is a 95% chance that an initial investment of $130,963.828 in the portfolio over the next 10 years will end within in the range of $ 110684.09 and $ 995843.78.

- Will weighting the portfolio more heavily toward stocks allow the credit union members to retire after only 10 years?

**Answer** Looking after weighting the protfolio and stocks, Because of the shortened investment compare the range between 10 years and 30 years , the portfolio needs to invest more heavily in the riskier asset stock, this will help accumulate wealth for retirement. As for current status, I would not recommend the credit union members to retire after only 10 years.