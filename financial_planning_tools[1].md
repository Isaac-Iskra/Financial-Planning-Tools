# Financial Planning with APIs and Simulations

In this Challenge, you’ll create two financial analysis tools by using a single Jupyter notebook:

Part 1: A financial planner for emergencies. The members will be able to use this tool to visualize their current savings. The members can then determine if they have enough reserves for an emergency fund.

Part 2: A financial planner for retirement. This tool will forecast the performance of their retirement portfolio in 30 years. To do this, the tool will make an Alpaca API call via the Alpaca SDK to get historical price data for use in Monte Carlo simulations.

You’ll use the information from the Monte Carlo simulation to answer questions about the portfolio in your Jupyter notebook.




```python
# Import the required libraries and dependencies
import os
import requests
import json
import pandas as pd
from dotenv import load_dotenv
import alpaca_trade_api as tradeapi
from MCForecastTools import MCSimulation

%matplotlib inline
```


```python
# Load the environment variables from the .env file
#by calling the load_dotenv function
load_dotenv("SAMPLE.env")
alpaca_api_key = os.getenv("ALPACA_API_KEY")
alpaca_secret_key = os.getenv("ALPACA_SECRET_KEY")

alpaca = tradeapi.REST(
    alpaca_api_key,
    alpaca_secret_key,
    api_version="v2"
)
```


```python
alpaca_api_key
```




    'PKLM6S7BV3LLTHA3MRNF'



## Part 1: Create a Financial Planner for Emergencies

### Evaluate the Cryptocurrency Wallet by Using the Requests Library

In this section, you’ll determine the current value of a member’s cryptocurrency wallet. You’ll collect the current prices for the Bitcoin and Ethereum cryptocurrencies by using the Python Requests library. For the prototype, you’ll assume that the member holds the 1.2 Bitcoins (BTC) and 5.3 Ethereum coins (ETH). To do all this, complete the following steps:

1. Create a variable named `monthly_income`, and set its value to `12000`.

2. Use the Requests library to get the current price (in US dollars) of Bitcoin (BTC) and Ethereum (ETH) by using the API endpoints that the starter code supplies.

3. Navigate the JSON response object to access the current price of each coin, and store each in a variable.

    > **Hint** Note the specific identifier for each cryptocurrency in the API JSON response. The Bitcoin identifier is `1`, and the Ethereum identifier is `1027`.

4. Calculate the value, in US dollars, of the current amount of each cryptocurrency and of the entire cryptocurrency wallet.




```python
# The current number of coins for each cryptocurrency asset held in the portfolio.
btc_coins = 1.2
eth_coins = 5.3
```

#### Step 1: Create a variable named `monthly_income`, and set its value to `12000`.


```python
# The monthly amount for the member's household income
monthly_income = 12000

```

#### Review the endpoint URLs for the API calls to Free Crypto API in order to get the current pricing information for both BTC and ETH.


```python
# The Free Crypto API Call endpoint URLs for the held cryptocurrency assets
btc_url = "https://api.alternative.me/v2/ticker/Bitcoin/?convert=USD"
eth_url = "https://api.alternative.me/v2/ticker/Ethereum/?convert=USD"
```

#### Step 2. Use the Requests library to get the current price (in US dollars) of Bitcoin (BTC) and Ethereum (ETH) by using the API endpoints that the starter code supplied.


```python
# Using the Python requests library, make an API call to access the current price of BTC
btc_response = requests.get(btc_url).json()
btc_response

# Use the json.dumps function to review the response data from the API call
# Use the indent and sort_keys parameters to make the response object readable
print(json.dumps(btc_response, indent=4, sort_keys=True))

```

    {
        "data": {
            "1": {
                "circulating_supply": 18865556,
                "id": 1,
                "last_updated": 1636211843,
                "max_supply": 21000000,
                "name": "Bitcoin",
                "quotes": {
                    "USD": {
                        "market_cap": 1149437263607,
                        "percent_change_1h": 0.53163291083954,
                        "percent_change_24h": -0.528650752023259,
                        "percent_change_7d": -2.19847194286229,
                        "percentage_change_1h": 0.53163291083954,
                        "percentage_change_24h": -0.528650752023259,
                        "percentage_change_7d": -2.19847194286229,
                        "price": 60914.0,
                        "volume_24h": 30651555499
                    }
                },
                "rank": 1,
                "symbol": "BTC",
                "total_supply": 18865556,
                "website_slug": "bitcoin"
            }
        },
        "metadata": {
            "error": null,
            "num_cryptocurrencies": 3105,
            "timestamp": 1636211843
        }
    }
    


```python
# Using the Python requests library, make an API call to access the current price ETH
eth_response = requests.get(eth_url).json()
eth_response

# Use the json.dumps function to review the response data from the API call
# Use the indent and sort_keys parameters to make the response object readable
print(json.dumps(eth_response, indent=4, sort_keys=True))

```

    {
        "data": {
            "1027": {
                "circulating_supply": 118232733,
                "id": 1027,
                "last_updated": 1636211888,
                "max_supply": 0,
                "name": "Ethereum",
                "quotes": {
                    "USD": {
                        "market_cap": 522688441895,
                        "percent_change_1h": 0.746837251423843,
                        "percent_change_24h": -1.24847713529032,
                        "percent_change_7d": -0.0232396340056789,
                        "percentage_change_1h": 0.746837251423843,
                        "percentage_change_24h": -1.24847713529032,
                        "percentage_change_7d": -0.0232396340056789,
                        "price": 4421.91,
                        "volume_24h": 16042792432
                    }
                },
                "rank": 2,
                "symbol": "ETH",
                "total_supply": 118232733,
                "website_slug": "ethereum"
            }
        },
        "metadata": {
            "error": null,
            "num_cryptocurrencies": 3105,
            "timestamp": 1636211888
        }
    }
    

#### Step 3: Navigate the JSON response object to access the current price of each coin, and store each in a variable.


```python
# Navigate the BTC response object to access the current price of BTC
btc_price = btc_response['data']['1']['quotes']['USD']['price']

# Print the current price of BTC
print(f"The current price of BTC is ${btc_price}")

```

    The current price of BTC is $60914.0
    


```python
# Navigate the BTC response object to access the current price of ETH
eth_price = eth_response['data']['1027']['quotes']['USD']['price']

# Print the current price of ETH
print(f"THe current price of ETH is ${eth_price}")

```

    THe current price of ETH is $4421.91
    

### Step 4: Calculate the value, in US dollars, of the current amount of each cryptocurrency and of the entire cryptocurrency wallet.


```python
# Compute the current value of the BTC holding 
btc_value = btc_price * btc_coins
btc_value

# Print current value of your holding in BTC
print(f"The curernt value of BTC is ${btc_value}")

```

    The curernt value of BTC is $73096.8
    


```python
# Compute the current value of the ETH holding 
eth_value = eth_price * eth_coins
eth_value

# Print current value of your holding in ETH
print(f"The Current value of ETH is ${eth_value}")

```

    The Current value of ETH is $23436.123
    


```python
# Compute the total value of the cryptocurrency wallet
# Add the value of the BTC holding to the value of the ETH holding
total_crypto_wallet = (btc_value + eth_value)
total_crypto_wallet

# Print current cryptocurrency wallet balance
print(f"The current Cryptocurrency Wallet Balance is ${total_crypto_wallet}")

```

    The current Cryptocurrency Wallet Balance is $96532.92300000001
    

### Evaluate the Stock and Bond Holdings by Using the Alpaca SDK

In this section, you’ll determine the current value of a member’s stock and bond holdings. You’ll make an API call to Alpaca via the Alpaca SDK to get the current closing prices of the SPDR S&P 500 ETF Trust (ticker: SPY) and of the iShares Core US Aggregate Bond ETF (ticker: AGG). For the prototype, assume that the member holds 110 shares of SPY, which represents the stock portion of their portfolio, and 200 shares of AGG, which represents the bond portion. To do all this, complete the following steps:

1. In the `Starter_Code` folder, create an environment file (`.env`) to store the values of your Alpaca API key and Alpaca secret key.

2. Set the variables for the Alpaca API and secret keys. Using the Alpaca SDK, create the Alpaca `tradeapi.REST` object. In this object, include the parameters for the Alpaca API key, the secret key, and the version number.

3. Set the following parameters for the Alpaca API call:

    - `tickers`: Use the tickers for the member’s stock and bond holdings.

    - `timeframe`: Use a time frame of one day.

    - `start_date` and `end_date`: Use the same date for these parameters, and format them with the date of the previous weekday (or `2020-08-07`). This is because you want the one closing price for the most-recent trading day.

4. Get the current closing prices for `SPY` and `AGG` by using the Alpaca `get_barset` function. Format the response as a Pandas DataFrame by including the `df` property at the end of the `get_barset` function.

5. Navigating the Alpaca response DataFrame, select the `SPY` and `AGG` closing prices, and store them as variables.

6. Calculate the value, in US dollars, of the current amount of shares in each of the stock and bond portions of the portfolio, and print the results.


#### Review the total number of shares held in both (SPY) and (AGG).


```python
# Current amount of shares held in both the stock (SPY) and bond (AGG) portion of the portfolio.
spy_shares = 110
agg_shares = 200

```

#### Step 1: In the `Starter_Code` folder, create an environment file (`.env`) to store the values of your Alpaca API key and Alpaca secret key.

#### Step 2: Set the variables for the Alpaca API and secret keys. Using the Alpaca SDK, create the Alpaca `tradeapi.REST` object. In this object, include the parameters for the Alpaca API key, the secret key, and the version number.


```python
# Set the variables for the Alpaca API and secret keys

#Already done in a cell above

```

#### Step 3: Set the following parameters for the Alpaca API call:

- `tickers`: Use the tickers for the member’s stock and bond holdings.

- `timeframe`: Use a time frame of one day.

- `start_date` and `end_date`: Use the same date for these parameters, and format them with the date of the previous weekday (or `2020-08-07`). This is because you want the one closing price for the most-recent trading day.



```python
# Set the tickers for both the bond and stock portion of the portfolio
tickers = ["SPY", "AGG"]
# Set timeframe to 1D 
timeframe = "1D"

# Format current date as ISO format
# Set both the start and end date at the date of your prior weekday 
# This will give you the closing price of the previous trading day
# Alternatively you can use a start and end date of 2020-08-07
start_date = pd.Timestamp("2020-08-01", tz="America/New_York").isoformat()
end_date = pd.Timestamp("2020-08-07", tz="America/New_York").isoformat()


```

#### Step 4: Get the current closing prices for `SPY` and `AGG` by using the Alpaca `get_barset` function. Format the response as a Pandas DataFrame by including the `df` property at the end of the `get_barset` function.


```python
# Use the Alpaca get_barset function to get current closing prices the portfolio
# Be sure to set the `df` property after the function to format the response object as a DataFrame
df_portfolio = alpaca.get_barset(
    tickers,
    timeframe, 
    start = start_date,
    end = end_date
).df

# Review the first 5 rows of the Alpaca DataFrame
df_portfolio.head(5)

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="5" halign="left">AGG</th>
      <th colspan="5" halign="left">SPY</th>
    </tr>
    <tr>
      <th></th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
    </tr>
    <tr>
      <th>time</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2020-08-03 00:00:00-04:00</th>
      <td>119.37</td>
      <td>119.40</td>
      <td>119.1903</td>
      <td>119.400</td>
      <td>17837420</td>
      <td>328.3200</td>
      <td>329.62</td>
      <td>327.73</td>
      <td>328.76</td>
      <td>71741125</td>
    </tr>
    <tr>
      <th>2020-08-04 00:00:00-04:00</th>
      <td>119.42</td>
      <td>119.63</td>
      <td>119.4200</td>
      <td>119.630</td>
      <td>21512268</td>
      <td>327.8600</td>
      <td>330.06</td>
      <td>327.86</td>
      <td>330.03</td>
      <td>73684427</td>
    </tr>
    <tr>
      <th>2020-08-05 00:00:00-04:00</th>
      <td>119.39</td>
      <td>119.49</td>
      <td>119.3100</td>
      <td>119.400</td>
      <td>34175883</td>
      <td>331.4700</td>
      <td>332.39</td>
      <td>331.18</td>
      <td>332.06</td>
      <td>72846458</td>
    </tr>
    <tr>
      <th>2020-08-06 00:00:00-04:00</th>
      <td>119.62</td>
      <td>119.73</td>
      <td>119.5300</td>
      <td>119.580</td>
      <td>9009216</td>
      <td>331.4799</td>
      <td>334.46</td>
      <td>331.13</td>
      <td>334.31</td>
      <td>76900649</td>
    </tr>
    <tr>
      <th>2020-08-07 00:00:00-04:00</th>
      <td>119.66</td>
      <td>119.73</td>
      <td>119.3950</td>
      <td>119.445</td>
      <td>8830420</td>
      <td>333.2800</td>
      <td>334.88</td>
      <td>332.30</td>
      <td>334.55</td>
      <td>98710236</td>
    </tr>
  </tbody>
</table>
</div>



#### Step 5: Navigating the Alpaca response DataFrame, select the `SPY` and `AGG` closing prices, and store them as variables.


```python
# Access the closing price for AGG from the Alpaca DataFrame
# Converting the value to a floating point number
agg_close_price = df_portfolio["AGG"]["close"]
agg_close_price

# Print the AGG closing price
print(agg_close_price)

```

    time
    2020-08-03 00:00:00-04:00    119.400
    2020-08-04 00:00:00-04:00    119.630
    2020-08-05 00:00:00-04:00    119.400
    2020-08-06 00:00:00-04:00    119.580
    2020-08-07 00:00:00-04:00    119.445
    Name: close, dtype: float64
    


```python
# Access the closing price for SPY from the Alpaca DataFrame
# Converting the value to a floating point number
spy_close_price = df_portfolio["SPY"]["close"]
spy_close_price

# Print the SPY closing price
print(spy_close_price)

```

    time
    2020-08-03 00:00:00-04:00    328.76
    2020-08-04 00:00:00-04:00    330.03
    2020-08-05 00:00:00-04:00    332.06
    2020-08-06 00:00:00-04:00    334.31
    2020-08-07 00:00:00-04:00    334.55
    Name: close, dtype: float64
    

#### Step 6: Calculate the value, in US dollars, of the current amount of shares in each of the stock and bond portions of the portfolio, and print the results.


```python
# Calculate the current value of the bond portion of the portfolio
agg_value = agg_close_price[-1] * agg_shares
agg_value

# Print the current value of the bond portfolio
print(f"The current value of AGG bond is ${agg_value}")

```

    The current value of AGG bond is $23889.0
    


```python
# Calculate the current value of the stock portion of the portfolio
spy_value = spy_close_price[-1] * spy_shares
spy_value

# Print the current value of the stock portfolio
print(f"The current vale of the SPY bond is ${spy_value}")

```

    The current vale of the SPY bond is $36800.5
    


```python
# Calculate the total value of the stock and bond portion of the portfolio
total_stocks_bonds = (spy_value + agg_value)
total_stocks_bonds

# Print the current balance of the stock and bond portion of the portfolio
print(f"The Current balance of the Stock and Bond portion of the Portfolio is ${total_stocks_bonds}")

```

    The Current balance of the Stock and Bond portion of the Portfolio is $60689.5
    


```python
# Calculate the total value of the member's entire savings portfolio
# Add the value of the cryptocurrency wallet to the value of the total stocks and bonds
total_portfolio = total_stocks_bonds + total_crypto_wallet
total_portfolio

# Print current cryptocurrency wallet balance
print(f"The current Cryptocurrency Wallet Balance is ${total_crypto_wallet}")

```

    The current Cryptocurrency Wallet Balance is $1113.4784946920533
    

### Evaluate the Emergency Fund

In this section, you’ll use the valuations for the cryptocurrency wallet and for the stock and bond portions of the portfolio to determine if the credit union member has enough savings to build an emergency fund into their financial plan. To do this, complete the following steps:

1. Create a Python list named `savings_data` that has two elements. The first element contains the total value of the cryptocurrency wallet. The second element contains the total value of the stock and bond portions of the portfolio.

2. Use the `savings_data` list to create a Pandas DataFrame named `savings_df`, and then display this DataFrame. The function to create the DataFrame should take the following three parameters:

    - `savings_data`: Use the list that you just created.

    - `columns`: Set this parameter equal to a Python list with a single value called `amount`.

    - `index`: Set this parameter equal to a Python list with the values of `crypto` and `stock/bond`.

3. Use the `savings_df` DataFrame to plot a pie chart that visualizes the composition of the member’s portfolio. The y-axis of the pie chart uses `amount`. Be sure to add a title.

4. Using Python, determine if the current portfolio has enough to create an emergency fund as part of the member’s financial plan. Ideally, an emergency fund should equal to three times the member’s monthly income. To do this, implement the following steps:

    1. Create a variable named `emergency_fund_value`, and set it equal to three times the value of the member’s `monthly_income` of $12000. (You set this earlier in Part 1).

    2. Create a series of three if statements to determine if the member’s total portfolio is large enough to fund the emergency portfolio:

        1. If the total portfolio value is greater than the emergency fund value, display a message congratulating the member for having enough money in this fund.

        2. Else if the total portfolio value is equal to the emergency fund value, display a message congratulating the member on reaching this important financial goal.

        3. Else the total portfolio is less than the emergency fund value, so display a message showing how many dollars away the member is from reaching the goal. (Subtract the total portfolio value from the emergency fund value.)


#### Step 1: Create a Python list named `savings_data` that has two elements. The first element contains the total value of the cryptocurrency wallet. The second element contains the total value of the stock and bond portions of the portfolio.


```python
# Consolidate financial assets data into a Python list
savings_data = [
    total_crypto_wallet,
    total_stocks_bonds
]

# Review the Python list savings_data
display(savings_data)
    
```


    [96532.92300000001, 60689.5]


#### Step 2: Use the `savings_data` list to create a Pandas DataFrame named `savings_df`, and then display this DataFrame. The function to create the DataFrame should take the following three parameters:

- `savings_data`: Use the list that you just created.

- `columns`: Set this parameter equal to a Python list with a single value called `amount`.

- `index`: Set this parameter equal to a Python list with the values of `crypto` and `stock/bond`.



```python
# Create a Pandas DataFrame called savings_df 
display(type('amount'))
savings_df = pd.DataFrame(data=savings_data, index=['crypto', 'stock/bond'], columns=['amount'])

# Display the savings_df DataFrame
display(savings_df)

```


    str



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>amount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>crypto</th>
      <td>96532.923</td>
    </tr>
    <tr>
      <th>stock/bond</th>
      <td>60689.500</td>
    </tr>
  </tbody>
</table>
</div>


#### Step 3: Use the `savings_df` DataFrame to plot a pie chart that visualizes the composition of the member’s portfolio. The y-axis of the pie chart uses `amount`. Be sure to add a title.


```python
# Plot the total value of the member's portfolio (crypto and stock/bond) in a pie chart
savings_df.plot.pie(y='amount', title="Total Savings made up of Crypto Wallet and Stock & Bonds")

```




    <AxesSubplot:title={'center':'Total Savings made up of Crypto Wallet and Stock & Bonds'}, ylabel='amount'>




    
![png](output_45_1.png)
    


#### Step 4: Using Python, determine if the current portfolio has enough to create an emergency fund as part of the member’s financial plan. Ideally, an emergency fund should equal to three times the member’s monthly income. To do this, implement the following steps:

Step 1. Create a variable named `emergency_fund_value`, and set it equal to three times the value of the member’s `monthly_income` of 12000. (You set this earlier in Part 1).

Step 2. Create a series of three if statements to determine if the member’s total portfolio is large enough to fund the emergency portfolio:

* If the total portfolio value is greater than the emergency fund value, display a message congratulating the member for having enough money in this fund.

* Else if the total portfolio value is equal to the emergency fund value, display a message congratulating the member on reaching this important financial goal.

* Else the total portfolio is less than the emergency fund value, so display a message showing how many dollars away the member is from reaching the goal. (Subtract the total portfolio value from the emergency fund value.)


##### Step 4-1: Create a variable named `emergency_fund_value`, and set it equal to three times the value of the member’s `monthly_income` of 12000. (You set this earlier in Part 1).


```python
# Create a variable named emergency_fund_value
emergency_fund_value = 3 * monthly_income
emergency_fund_value
```




    36000



##### Step 4-2: Create a series of three if statements to determine if the member’s total portfolio is large enough to fund the emergency portfolio:

* If the total portfolio value is greater than the emergency fund value, display a message congratulating the member for having enough money in this fund.

* Else if the total portfolio value is equal to the emergency fund value, display a message congratulating the member on reaching this important financial goal.

* Else the total portfolio is less than the emergency fund value, so display a message showing how many dollars away the member is from reaching the goal. (Subtract the total portfolio value from the emergency fund value.)


```python
# Evaluate the possibility of creating an emergency fund with 3 conditions:
if total_portfolio > emergency_fund_value:
    print("Congradulations for having enough money in your fund!")
elif total_portfolio == emergency_fund_value:
    print("Congradulations on reaching an important financial goal!")
elif total_portfolio < emergency_fund_value:
    print("You are currently ${emergency_fund_value - total_portfolio} away from your financial goal.  Keep working hard!")

```

    Congradulations for having enough money in your fund!
    

## Part 2: Create a Financial Planner for Retirement

### Create the Monte Carlo Simulation

In this section, you’ll use the MCForecastTools library to create a Monte Carlo simulation for the member’s savings portfolio. To do this, complete the following steps:

1. Make an API call via the Alpaca SDK to get 3 years of historical closing prices for a traditional 60/40 portfolio split: 60% stocks (SPY) and 40% bonds (AGG).

2. Run a Monte Carlo simulation of 500 samples and 30 years for the 60/40 portfolio, and then plot the results.The following image shows the overlay line plot resulting from a simulation with these characteristics. However, because a random number generator is used to run each live Monte Carlo simulation, your image will differ slightly from this exact image:

![A screenshot depicts the resulting plot.](Images/5-4-monte-carlo-line-plot.png)

3. Plot the probability distribution of the Monte Carlo simulation. Plot the probability distribution of the Monte Carlo simulation. The following image shows the histogram plot resulting from a simulation with these characteristics. However, because a random number generator is used to run each live Monte Carlo simulation, your image will differ slightly from this exact image:

![A screenshot depicts the histogram plot.](Images/5-4-monte-carlo-histogram.png)

4. Generate the summary statistics for the Monte Carlo simulation.



#### Step 1: Make an API call via the Alpaca SDK to get 3 years of historical closing prices for a traditional 60/40 portfolio split: 60% stocks (SPY) and 40% bonds (AGG).


```python
# Set start and end dates of 3 years back from your current date
# Alternatively, you can use an end date of 2020-08-07 and work 3 years back from that date 
#look at 5.3.3 in the lesson for help
start_date = pd.Timestamp("2017-08-07", tz="America/New_York").isoformat()
end_date = pd.Timestamp("2020-08-07", tz="America/New_York").isoformat()


# Set number of rows to 1000 to retrieve the maximum amount of rows
limit_rows = 1000
```


```python
# Use the Alpaca get_barset function to make the API call to get the 3 years worth of pricing data
# The tickers and timeframe parameters should have been set in Part 1 of this activity 
# The start and end dates should be updated with the information set above
# Remember to add the df property to the end of the call so the response is returned as a DataFrame
prices_df = alpaca.get_barset(
    tickers,
    timeframe,
    start = start_date,
    end = end_date,
    limit = limit_rows
).df


# Display both the first and last five rows of the DataFrame
prices_df.head(5)
prices_df.tail(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="5" halign="left">AGG</th>
      <th colspan="5" halign="left">SPY</th>
    </tr>
    <tr>
      <th></th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
    </tr>
    <tr>
      <th>time</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2020-08-03 00:00:00-04:00</th>
      <td>119.37</td>
      <td>119.40</td>
      <td>119.1903</td>
      <td>119.400</td>
      <td>17837420.0</td>
      <td>328.3200</td>
      <td>329.62</td>
      <td>327.73</td>
      <td>328.76</td>
      <td>71741125</td>
    </tr>
    <tr>
      <th>2020-08-04 00:00:00-04:00</th>
      <td>119.42</td>
      <td>119.63</td>
      <td>119.4200</td>
      <td>119.630</td>
      <td>21512268.0</td>
      <td>327.8600</td>
      <td>330.06</td>
      <td>327.86</td>
      <td>330.03</td>
      <td>73684427</td>
    </tr>
    <tr>
      <th>2020-08-05 00:00:00-04:00</th>
      <td>119.39</td>
      <td>119.49</td>
      <td>119.3100</td>
      <td>119.400</td>
      <td>34175883.0</td>
      <td>331.4700</td>
      <td>332.39</td>
      <td>331.18</td>
      <td>332.06</td>
      <td>72846458</td>
    </tr>
    <tr>
      <th>2020-08-06 00:00:00-04:00</th>
      <td>119.62</td>
      <td>119.73</td>
      <td>119.5300</td>
      <td>119.580</td>
      <td>9009216.0</td>
      <td>331.4799</td>
      <td>334.46</td>
      <td>331.13</td>
      <td>334.31</td>
      <td>76900649</td>
    </tr>
    <tr>
      <th>2020-08-07 00:00:00-04:00</th>
      <td>119.66</td>
      <td>119.73</td>
      <td>119.3950</td>
      <td>119.445</td>
      <td>8830420.0</td>
      <td>333.2800</td>
      <td>334.88</td>
      <td>332.30</td>
      <td>334.55</td>
      <td>98710236</td>
    </tr>
  </tbody>
</table>
</div>



#### Step 2: Run a Monte Carlo simulation of 500 samples and 30 years for the 60/40 portfolio, and then plot the results.


```python
# Configure the Monte Carlo simulation to forecast 30 years cumulative returns
# The weights should be split 40% to AGG and 60% to SPY.
# Run 500 samples.
MC_thiryyear = MCSimulation(
    portfolio_data= prices_df,
    weights= [.60, .40], 
    num_simulation= 500,
    num_trading_days= 252 * 30
)

# Review the simulation input data
MC_thiryyear.portfolio_data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="6" halign="left">AGG</th>
      <th colspan="6" halign="left">SPY</th>
    </tr>
    <tr>
      <th></th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
      <th>daily_return</th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
      <th>daily_return</th>
    </tr>
    <tr>
      <th>time</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017-08-07 00:00:00-04:00</th>
      <td>109.69</td>
      <td>109.72</td>
      <td>109.6066</td>
      <td>109.6850</td>
      <td>1425317.0</td>
      <td>NaN</td>
      <td>247.49</td>
      <td>247.87</td>
      <td>247.37</td>
      <td>247.850</td>
      <td>20901988</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2017-08-08 00:00:00-04:00</th>
      <td>109.67</td>
      <td>109.69</td>
      <td>109.5000</td>
      <td>109.5810</td>
      <td>1998999.0</td>
      <td>-0.000948</td>
      <td>247.51</td>
      <td>248.91</td>
      <td>246.83</td>
      <td>247.291</td>
      <td>44619069</td>
      <td>-0.002255</td>
    </tr>
    <tr>
      <th>2017-08-09 00:00:00-04:00</th>
      <td>109.87</td>
      <td>109.87</td>
      <td>109.6600</td>
      <td>109.6700</td>
      <td>2069028.0</td>
      <td>0.000812</td>
      <td>246.47</td>
      <td>247.31</td>
      <td>246.06</td>
      <td>247.230</td>
      <td>48543032</td>
      <td>-0.000247</td>
    </tr>
    <tr>
      <th>2017-08-10 00:00:00-04:00</th>
      <td>109.70</td>
      <td>109.85</td>
      <td>109.6700</td>
      <td>109.8053</td>
      <td>1691677.0</td>
      <td>0.001234</td>
      <td>246.28</td>
      <td>246.44</td>
      <td>243.70</td>
      <td>243.790</td>
      <td>87857827</td>
      <td>-0.013914</td>
    </tr>
    <tr>
      <th>2017-08-11 00:00:00-04:00</th>
      <td>109.75</td>
      <td>109.94</td>
      <td>109.6800</td>
      <td>109.8850</td>
      <td>1613467.0</td>
      <td>0.000726</td>
      <td>244.02</td>
      <td>244.80</td>
      <td>243.75</td>
      <td>244.110</td>
      <td>56404847</td>
      <td>0.001313</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Run the Monte Carlo simulation to forecast 30 years cumulative returns
MC_thiryyear.calc_cumulative_return()

```

    Running Monte Carlo simulation number 0.
    Running Monte Carlo simulation number 10.
    Running Monte Carlo simulation number 20.
    Running Monte Carlo simulation number 30.
    Running Monte Carlo simulation number 40.
    Running Monte Carlo simulation number 50.
    Running Monte Carlo simulation number 60.
    Running Monte Carlo simulation number 70.
    Running Monte Carlo simulation number 80.
    Running Monte Carlo simulation number 90.
    Running Monte Carlo simulation number 100.
    Running Monte Carlo simulation number 110.
    Running Monte Carlo simulation number 120.
    Running Monte Carlo simulation number 130.
    Running Monte Carlo simulation number 140.
    Running Monte Carlo simulation number 150.
    Running Monte Carlo simulation number 160.
    Running Monte Carlo simulation number 170.
    Running Monte Carlo simulation number 180.
    Running Monte Carlo simulation number 190.
    Running Monte Carlo simulation number 200.
    Running Monte Carlo simulation number 210.
    Running Monte Carlo simulation number 220.
    Running Monte Carlo simulation number 230.
    Running Monte Carlo simulation number 240.
    Running Monte Carlo simulation number 250.
    Running Monte Carlo simulation number 260.
    Running Monte Carlo simulation number 270.
    Running Monte Carlo simulation number 280.
    Running Monte Carlo simulation number 290.
    Running Monte Carlo simulation number 300.
    Running Monte Carlo simulation number 310.
    Running Monte Carlo simulation number 320.
    Running Monte Carlo simulation number 330.
    Running Monte Carlo simulation number 340.
    Running Monte Carlo simulation number 350.
    Running Monte Carlo simulation number 360.
    Running Monte Carlo simulation number 370.
    Running Monte Carlo simulation number 380.
    Running Monte Carlo simulation number 390.
    Running Monte Carlo simulation number 400.
    Running Monte Carlo simulation number 410.
    Running Monte Carlo simulation number 420.
    Running Monte Carlo simulation number 430.
    Running Monte Carlo simulation number 440.
    Running Monte Carlo simulation number 450.
    Running Monte Carlo simulation number 460.
    Running Monte Carlo simulation number 470.
    Running Monte Carlo simulation number 480.
    Running Monte Carlo simulation number 490.
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>490</th>
      <th>491</th>
      <th>492</th>
      <th>493</th>
      <th>494</th>
      <th>495</th>
      <th>496</th>
      <th>497</th>
      <th>498</th>
      <th>499</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>...</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.006643</td>
      <td>0.999754</td>
      <td>0.997550</td>
      <td>0.995923</td>
      <td>0.999268</td>
      <td>0.994050</td>
      <td>1.004967</td>
      <td>0.986924</td>
      <td>1.002636</td>
      <td>1.004244</td>
      <td>...</td>
      <td>0.993240</td>
      <td>1.009094</td>
      <td>1.001557</td>
      <td>1.000722</td>
      <td>1.003224</td>
      <td>1.001841</td>
      <td>0.998715</td>
      <td>0.994880</td>
      <td>0.993397</td>
      <td>0.989916</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.006342</td>
      <td>0.992876</td>
      <td>0.993482</td>
      <td>0.990703</td>
      <td>1.001831</td>
      <td>1.000206</td>
      <td>1.005856</td>
      <td>0.986287</td>
      <td>1.008831</td>
      <td>1.007774</td>
      <td>...</td>
      <td>0.994118</td>
      <td>1.008216</td>
      <td>0.999047</td>
      <td>1.005634</td>
      <td>1.007269</td>
      <td>1.011098</td>
      <td>0.993917</td>
      <td>0.995010</td>
      <td>0.993700</td>
      <td>0.989017</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.005932</td>
      <td>0.996704</td>
      <td>0.999376</td>
      <td>0.984282</td>
      <td>0.998827</td>
      <td>0.996774</td>
      <td>1.013077</td>
      <td>0.982551</td>
      <td>1.005117</td>
      <td>0.997648</td>
      <td>...</td>
      <td>0.981293</td>
      <td>1.007188</td>
      <td>0.997289</td>
      <td>1.003256</td>
      <td>1.001927</td>
      <td>1.014955</td>
      <td>0.994793</td>
      <td>0.998862</td>
      <td>0.996170</td>
      <td>0.983296</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.014151</td>
      <td>1.002973</td>
      <td>0.995914</td>
      <td>0.985460</td>
      <td>1.003076</td>
      <td>0.984952</td>
      <td>1.030081</td>
      <td>0.974253</td>
      <td>1.005138</td>
      <td>0.994349</td>
      <td>...</td>
      <td>0.976093</td>
      <td>1.007420</td>
      <td>1.004429</td>
      <td>0.997100</td>
      <td>1.007135</td>
      <td>1.014609</td>
      <td>0.997141</td>
      <td>0.995466</td>
      <td>0.983478</td>
      <td>0.984428</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>7556</th>
      <td>23.218823</td>
      <td>5.381490</td>
      <td>6.963376</td>
      <td>3.018859</td>
      <td>1.969719</td>
      <td>2.303215</td>
      <td>11.427335</td>
      <td>8.577371</td>
      <td>5.238669</td>
      <td>7.927878</td>
      <td>...</td>
      <td>5.739443</td>
      <td>5.761453</td>
      <td>5.193696</td>
      <td>11.686253</td>
      <td>11.705011</td>
      <td>3.811501</td>
      <td>11.120645</td>
      <td>13.231411</td>
      <td>4.297401</td>
      <td>7.720304</td>
    </tr>
    <tr>
      <th>7557</th>
      <td>23.325057</td>
      <td>5.371702</td>
      <td>6.935067</td>
      <td>3.043236</td>
      <td>1.968447</td>
      <td>2.280192</td>
      <td>11.398263</td>
      <td>8.531940</td>
      <td>5.262131</td>
      <td>7.998067</td>
      <td>...</td>
      <td>5.729387</td>
      <td>5.813876</td>
      <td>5.172717</td>
      <td>11.760113</td>
      <td>11.680781</td>
      <td>3.817747</td>
      <td>11.144473</td>
      <td>13.237721</td>
      <td>4.294229</td>
      <td>7.757499</td>
    </tr>
    <tr>
      <th>7558</th>
      <td>23.270846</td>
      <td>5.355426</td>
      <td>6.968796</td>
      <td>3.036814</td>
      <td>1.966151</td>
      <td>2.297336</td>
      <td>11.438736</td>
      <td>8.528203</td>
      <td>5.235837</td>
      <td>7.919601</td>
      <td>...</td>
      <td>5.772617</td>
      <td>5.800077</td>
      <td>5.129600</td>
      <td>11.756540</td>
      <td>11.522995</td>
      <td>3.827874</td>
      <td>11.206709</td>
      <td>13.161957</td>
      <td>4.298513</td>
      <td>7.780780</td>
    </tr>
    <tr>
      <th>7559</th>
      <td>23.316571</td>
      <td>5.362647</td>
      <td>6.918826</td>
      <td>3.068205</td>
      <td>1.991577</td>
      <td>2.274936</td>
      <td>11.363353</td>
      <td>8.548327</td>
      <td>5.282542</td>
      <td>7.927721</td>
      <td>...</td>
      <td>5.769899</td>
      <td>5.810142</td>
      <td>5.119403</td>
      <td>11.791660</td>
      <td>11.520902</td>
      <td>3.827941</td>
      <td>11.196425</td>
      <td>13.136822</td>
      <td>4.332191</td>
      <td>7.701648</td>
    </tr>
    <tr>
      <th>7560</th>
      <td>23.301716</td>
      <td>5.358558</td>
      <td>6.893936</td>
      <td>3.045889</td>
      <td>2.005785</td>
      <td>2.266064</td>
      <td>11.329549</td>
      <td>8.491169</td>
      <td>5.283176</td>
      <td>7.970513</td>
      <td>...</td>
      <td>5.722277</td>
      <td>5.823159</td>
      <td>5.124445</td>
      <td>11.909995</td>
      <td>11.468474</td>
      <td>3.825555</td>
      <td>11.299015</td>
      <td>13.120042</td>
      <td>4.355265</td>
      <td>7.730526</td>
    </tr>
  </tbody>
</table>
<p>7561 rows × 500 columns</p>
</div>




```python
# Visualize the 30-year Monte Carlo simulation by creating an
# overlay line plot
MC_sim__line_plot = MC_thiryyear.plot_simulation()

MC_sim__line_plot.get_figure().savefig("MC_thirtyyear_sim_plot.png", bbox_inches="tight")

```


    
![png](output_59_0.png)
    


#### Step 3: Plot the probability distribution of the Monte Carlo simulation.


```python
# Visualize the probability distribution of the 30-year Monte Carlo simulation 
# by plotting a histogram
MC_sim__line_plot = MC_thiryyear.plot_distribution()


```


    
![png](output_61_0.png)
    


#### Step 4: Generate the summary statistics for the Monte Carlo simulation.


```python
# Generate summary statistics from the 30-year Monte Carlo simulation results
# Save the results as a variable
MC_summary_statistics = MC_thiryyear.summarize_cumulative_return()
MC_summary_statistics


# Review the 30-year Monte Carlo summary statistics
MC_summary_statistics
```




    count           500.000000
    mean              7.804117
    std               4.643140
    min               1.073515
    25%               4.795947
    50%               6.642061
    75%               9.397316
    max              32.047438
    95% CI Lower      2.287327
    95% CI Upper     20.354911
    Name: 7560, dtype: float64



### Analyze the Retirement Portfolio Forecasts

Using the current value of only the stock and bond portion of the member's portfolio and the summary statistics that you generated from the Monte Carlo simulation, answer the following question in your Jupyter notebook:

-  What are the lower and upper bounds for the expected value of the portfolio with a 95% confidence interval?



```python
# Print the current balance of the stock and bond portion of the members portfolio
print(f"The current balance of the stock and bond portion of the members portfolio is ${total_portfolio}")

```

    The current balance of the stock and bond portion of the members portfolio is $61802.978494692055
    


```python
# Use the lower and upper `95%` confidence intervals to calculate the range of the possible outcomes for the current stock/bond portfolio
ci_lower_thirty_cumulative_return = MC_summary_statistics[8] * 10000
ci_upper_thirty_cumulative_return = MC_summary_statistics[9] * 10000

# Print the result of your calculations
print(f"There is a 95% chance that an interval investment of $10,000 in the portfolio"
f" over the next five years will end within the range of "
f" ${ci_lower_thirty_cumulative_return: .2f} and ${ci_upper_thirty_cumulative_return: .2f}.")

```

    There is a 95% chance that an interval investment of $10,000 in the portfolio over the next five years will end within the range of  $ 22873.27 and $ 203549.11.
    

### Forecast Cumulative Returns in 10 Years

The CTO of the credit union is impressed with your work on these planning tools but wonders if 30 years is a long time to wait until retirement. So, your next task is to adjust the retirement portfolio and run a new Monte Carlo simulation to find out if the changes will allow members to retire earlier.

For this new Monte Carlo simulation, do the following: 

- Forecast the cumulative returns for 10 years from now. Because of the shortened investment horizon (30 years to 10 years), the portfolio needs to invest more heavily in the riskier asset&mdash;that is, stock&mdash;to help accumulate wealth for retirement. 

- Adjust the weights of the retirement portfolio so that the composition for the Monte Carlo simulation consists of 20% bonds and 80% stocks. 

- Run the simulation over 500 samples, and use the same data that the API call to Alpaca generated.

- Based on the new Monte Carlo simulation, answer the following questions in your Jupyter notebook:

    - Using the current value of only the stock and bond portion of the member's portfolio and the summary statistics that you generated from the new Monte Carlo simulation, what are the lower and upper bounds for the expected value of the portfolio (with the new weights) with a 95% confidence interval?

    - Will weighting the portfolio more heavily toward stocks allow the credit union members to retire after only 10 years?



```python
# Configure a Monte Carlo simulation to forecast 10 years cumulative returns
# The weights should be split 20% to AGG and 80% to SPY.
# Run 500 samples.
MC_tenyear = MCSimulation(
    portfolio_data=prices_df,
    weights= [.20, .80],
    num_simulation=500,
    num_trading_days=252*10
)

# Review the simulation input data
MC_tenyear.portfolio_data.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="6" halign="left">AGG</th>
      <th colspan="6" halign="left">SPY</th>
    </tr>
    <tr>
      <th></th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
      <th>daily_return</th>
      <th>open</th>
      <th>high</th>
      <th>low</th>
      <th>close</th>
      <th>volume</th>
      <th>daily_return</th>
    </tr>
    <tr>
      <th>time</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017-08-07 00:00:00-04:00</th>
      <td>109.69</td>
      <td>109.72</td>
      <td>109.6066</td>
      <td>109.6850</td>
      <td>1425317.0</td>
      <td>NaN</td>
      <td>247.49</td>
      <td>247.87</td>
      <td>247.37</td>
      <td>247.850</td>
      <td>20901988</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2017-08-08 00:00:00-04:00</th>
      <td>109.67</td>
      <td>109.69</td>
      <td>109.5000</td>
      <td>109.5810</td>
      <td>1998999.0</td>
      <td>-0.000948</td>
      <td>247.51</td>
      <td>248.91</td>
      <td>246.83</td>
      <td>247.291</td>
      <td>44619069</td>
      <td>-0.002255</td>
    </tr>
    <tr>
      <th>2017-08-09 00:00:00-04:00</th>
      <td>109.87</td>
      <td>109.87</td>
      <td>109.6600</td>
      <td>109.6700</td>
      <td>2069028.0</td>
      <td>0.000812</td>
      <td>246.47</td>
      <td>247.31</td>
      <td>246.06</td>
      <td>247.230</td>
      <td>48543032</td>
      <td>-0.000247</td>
    </tr>
    <tr>
      <th>2017-08-10 00:00:00-04:00</th>
      <td>109.70</td>
      <td>109.85</td>
      <td>109.6700</td>
      <td>109.8053</td>
      <td>1691677.0</td>
      <td>0.001234</td>
      <td>246.28</td>
      <td>246.44</td>
      <td>243.70</td>
      <td>243.790</td>
      <td>87857827</td>
      <td>-0.013914</td>
    </tr>
    <tr>
      <th>2017-08-11 00:00:00-04:00</th>
      <td>109.75</td>
      <td>109.94</td>
      <td>109.6800</td>
      <td>109.8850</td>
      <td>1613467.0</td>
      <td>0.000726</td>
      <td>244.02</td>
      <td>244.80</td>
      <td>243.75</td>
      <td>244.110</td>
      <td>56404847</td>
      <td>0.001313</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Run the Monte Carlo simulation to forecast 10 years cumulative returns
MC_tenyear.calc_cumulative_return()

```

    Running Monte Carlo simulation number 0.
    Running Monte Carlo simulation number 10.
    Running Monte Carlo simulation number 20.
    Running Monte Carlo simulation number 30.
    Running Monte Carlo simulation number 40.
    Running Monte Carlo simulation number 50.
    Running Monte Carlo simulation number 60.
    Running Monte Carlo simulation number 70.
    Running Monte Carlo simulation number 80.
    Running Monte Carlo simulation number 90.
    Running Monte Carlo simulation number 100.
    Running Monte Carlo simulation number 110.
    Running Monte Carlo simulation number 120.
    Running Monte Carlo simulation number 130.
    Running Monte Carlo simulation number 140.
    Running Monte Carlo simulation number 150.
    Running Monte Carlo simulation number 160.
    Running Monte Carlo simulation number 170.
    Running Monte Carlo simulation number 180.
    Running Monte Carlo simulation number 190.
    Running Monte Carlo simulation number 200.
    Running Monte Carlo simulation number 210.
    Running Monte Carlo simulation number 220.
    Running Monte Carlo simulation number 230.
    Running Monte Carlo simulation number 240.
    Running Monte Carlo simulation number 250.
    Running Monte Carlo simulation number 260.
    Running Monte Carlo simulation number 270.
    Running Monte Carlo simulation number 280.
    Running Monte Carlo simulation number 290.
    Running Monte Carlo simulation number 300.
    Running Monte Carlo simulation number 310.
    Running Monte Carlo simulation number 320.
    Running Monte Carlo simulation number 330.
    Running Monte Carlo simulation number 340.
    Running Monte Carlo simulation number 350.
    Running Monte Carlo simulation number 360.
    Running Monte Carlo simulation number 370.
    Running Monte Carlo simulation number 380.
    Running Monte Carlo simulation number 390.
    Running Monte Carlo simulation number 400.
    Running Monte Carlo simulation number 410.
    Running Monte Carlo simulation number 420.
    Running Monte Carlo simulation number 430.
    Running Monte Carlo simulation number 440.
    Running Monte Carlo simulation number 450.
    Running Monte Carlo simulation number 460.
    Running Monte Carlo simulation number 470.
    Running Monte Carlo simulation number 480.
    Running Monte Carlo simulation number 490.
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>490</th>
      <th>491</th>
      <th>492</th>
      <th>493</th>
      <th>494</th>
      <th>495</th>
      <th>496</th>
      <th>497</th>
      <th>498</th>
      <th>499</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>...</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.016294</td>
      <td>0.978700</td>
      <td>1.003368</td>
      <td>0.995403</td>
      <td>1.003688</td>
      <td>1.017039</td>
      <td>0.994203</td>
      <td>0.992510</td>
      <td>0.991861</td>
      <td>1.006274</td>
      <td>...</td>
      <td>0.996635</td>
      <td>0.977514</td>
      <td>0.982164</td>
      <td>0.990167</td>
      <td>0.998655</td>
      <td>1.012144</td>
      <td>0.994224</td>
      <td>1.002907</td>
      <td>1.007674</td>
      <td>1.012797</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.010836</td>
      <td>0.970445</td>
      <td>1.016559</td>
      <td>0.997700</td>
      <td>1.018994</td>
      <td>1.002286</td>
      <td>0.983967</td>
      <td>0.988950</td>
      <td>0.990786</td>
      <td>1.002661</td>
      <td>...</td>
      <td>0.989993</td>
      <td>0.957346</td>
      <td>0.990219</td>
      <td>1.003028</td>
      <td>0.993831</td>
      <td>1.012502</td>
      <td>0.984180</td>
      <td>0.988342</td>
      <td>1.013178</td>
      <td>1.023940</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.014889</td>
      <td>0.964507</td>
      <td>1.012057</td>
      <td>0.992191</td>
      <td>1.026152</td>
      <td>1.010364</td>
      <td>1.010536</td>
      <td>0.988771</td>
      <td>0.976563</td>
      <td>1.005486</td>
      <td>...</td>
      <td>0.977265</td>
      <td>0.975899</td>
      <td>0.994948</td>
      <td>1.005235</td>
      <td>0.988906</td>
      <td>1.000137</td>
      <td>0.969243</td>
      <td>0.998430</td>
      <td>1.029366</td>
      <td>1.020818</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.986298</td>
      <td>0.961040</td>
      <td>1.018008</td>
      <td>0.993732</td>
      <td>1.028642</td>
      <td>1.011386</td>
      <td>1.013114</td>
      <td>0.986617</td>
      <td>0.996096</td>
      <td>0.994004</td>
      <td>...</td>
      <td>0.976485</td>
      <td>0.960634</td>
      <td>0.977465</td>
      <td>0.995018</td>
      <td>1.016108</td>
      <td>1.023122</td>
      <td>0.955927</td>
      <td>0.985170</td>
      <td>1.012416</td>
      <td>1.028351</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2516</th>
      <td>3.782231</td>
      <td>8.501086</td>
      <td>1.548300</td>
      <td>1.569878</td>
      <td>6.176942</td>
      <td>1.721892</td>
      <td>2.629878</td>
      <td>3.063723</td>
      <td>3.045598</td>
      <td>1.355940</td>
      <td>...</td>
      <td>2.021786</td>
      <td>1.512016</td>
      <td>3.493573</td>
      <td>3.422362</td>
      <td>1.909410</td>
      <td>1.237124</td>
      <td>3.866551</td>
      <td>6.436581</td>
      <td>5.212602</td>
      <td>2.939274</td>
    </tr>
    <tr>
      <th>2517</th>
      <td>3.825318</td>
      <td>8.490858</td>
      <td>1.560246</td>
      <td>1.574175</td>
      <td>6.067398</td>
      <td>1.705186</td>
      <td>2.627277</td>
      <td>3.073681</td>
      <td>3.049202</td>
      <td>1.342932</td>
      <td>...</td>
      <td>2.014638</td>
      <td>1.529066</td>
      <td>3.460614</td>
      <td>3.475261</td>
      <td>1.917061</td>
      <td>1.223805</td>
      <td>3.866354</td>
      <td>6.542624</td>
      <td>5.204648</td>
      <td>2.888818</td>
    </tr>
    <tr>
      <th>2518</th>
      <td>3.814261</td>
      <td>8.545896</td>
      <td>1.577835</td>
      <td>1.566410</td>
      <td>6.034881</td>
      <td>1.695466</td>
      <td>2.643275</td>
      <td>3.117132</td>
      <td>2.991310</td>
      <td>1.324545</td>
      <td>...</td>
      <td>2.001328</td>
      <td>1.535738</td>
      <td>3.453069</td>
      <td>3.471011</td>
      <td>1.917331</td>
      <td>1.208364</td>
      <td>3.817759</td>
      <td>6.508123</td>
      <td>5.252182</td>
      <td>2.881386</td>
    </tr>
    <tr>
      <th>2519</th>
      <td>3.836511</td>
      <td>8.548240</td>
      <td>1.565683</td>
      <td>1.575603</td>
      <td>5.982964</td>
      <td>1.671686</td>
      <td>2.656944</td>
      <td>3.207066</td>
      <td>3.041888</td>
      <td>1.350738</td>
      <td>...</td>
      <td>2.004730</td>
      <td>1.520483</td>
      <td>3.530296</td>
      <td>3.504661</td>
      <td>1.910730</td>
      <td>1.208187</td>
      <td>3.791726</td>
      <td>6.682444</td>
      <td>5.151865</td>
      <td>2.899194</td>
    </tr>
    <tr>
      <th>2520</th>
      <td>3.844280</td>
      <td>8.568358</td>
      <td>1.601869</td>
      <td>1.553709</td>
      <td>5.945056</td>
      <td>1.671154</td>
      <td>2.666366</td>
      <td>3.286272</td>
      <td>2.987783</td>
      <td>1.362944</td>
      <td>...</td>
      <td>1.991307</td>
      <td>1.512541</td>
      <td>3.573722</td>
      <td>3.507149</td>
      <td>1.897613</td>
      <td>1.217772</td>
      <td>3.846007</td>
      <td>6.592535</td>
      <td>5.134013</td>
      <td>2.905261</td>
    </tr>
  </tbody>
</table>
<p>2521 rows × 500 columns</p>
</div>




```python
# Visualize the 10-year Monte Carlo simulation by creating an
# overlay line plot
MC_sim__line_plot = MC_tenyear.plot_simulation()
MC_sim__line_plot.get_figure().savefig("MC_tenyear_sim_plot_line.png", bbox_inches="tight")

```


    
![png](output_70_0.png)
    



```python
# Visualize the probability distribution of the 10-year Monte Carlo simulation 
# by plotting a histogram
MC_sim__line_plot = MC_tenyear.plot_distribution()

```


    
![png](output_71_0.png)
    



```python
# Generate summary statistics from the 10-year Monte Carlo simulation results
# Save the results as a variable
MC_summary_statistics_ten = MC_tenyear.summarize_cumulative_return()
MC_summary_statistics_ten


# Review the 10-year Monte Carlo summary statistics
MC_summary_statistics_ten
```




    count           500.000000
    mean              2.763903
    std               1.737647
    min               0.427214
    25%               1.640805
    50%               2.390213
    75%               3.292657
    max              15.203225
    95% CI Lower      0.850763
    95% CI Upper      7.501477
    Name: 2520, dtype: float64



### Answer the following questions:

#### Question: Using the current value of only the stock and bond portion of the member's portfolio and the summary statistics that you generated from the new Monte Carlo simulation, what are the lower and upper bounds for the expected value of the portfolio (with the new weights) with a 95% confidence interval?


```python
# Print the current balance of the stock and bond portion of the members portfolio
print(f"The current balance of the stock and bond portion of the members portfolio is ${total_portfolio}")

```

    The current balance of the stock and bond portion of the members portfolio is $61802.978494692055
    


```python
# Use the lower and upper `95%` confidence intervals to calculate the range of the possible outcomes for the current stock/bond portfolio
ci_lower_ten_cumulative_return = MC_summary_statistics_ten[8] * 10000
ci_upper_ten_cumulative_return = MC_summary_statistics_ten[9] * 10000

# Print the result of your calculations
print(f"There is a 95% chance that an interval investment of $10,000 in the portfolio"
f" over the next five years will end within the range of "
f" ${ci_lower_ten_cumulative_return: .2f} and ${ci_upper_ten_cumulative_return: .2f}.")

```

    There is a 95% chance that an interval investment of $10,000 in the portfolio over the next five years will end within the range of  $ 8507.63 and $ 75014.77.
    

#### Question: Will weighting the portfolio more heavily to stocks allow the credit union members to retire after only 10 years?
No the credit union members won't be able to retire after 10 years.