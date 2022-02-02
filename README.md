# Financial Planning Application
## Introduction
This application is created by two seperate financial analysis tools using a single Jupyter Notebook. The first financial analysis tool is used to visualize an individual's current savings and determine if there is enough reserves for an emergency func. The second tool is a financial planner for retirement which uses a Monte Carlo Simulation to forecast this individual's portfolio in 30 years.

This application involves communicating to (2) different API endpoints (Alpaca SDK and a free crypto API named Alternative).

Please note: This application involves the set up of a .env file stored in your directory when git cloned. This .env file should have variables associated with YOUR API keys.

---

## Technologies

The following technology/software was used for this application:


-Python 3.7.11 (Programming language)

    Imported Python Libraries/Packages:
    
    -import os
    
    -import requests
    
    -import json
    
    -import pandas as pd
    
    -from dotenv import load_dotenv
    
    -import alpaca_trade_api as tradeapi
    
    -from MCForecastTools import MCSimulation
    
    -%matplotlib inline
    
    -import matplotlib.pyplot as plt
    
-JupyterLab

-Git version 2.34.11.windows.1

-Windows 10 Operating System

---

## Installation Guide

To install this application on your machine, download (or clone using http link) all files contained in this repository davidlp94/05_DLP_Financial_Planning_Tools.

Next, open GitBash (terminal for MacOS), change your directory to the 05_DLP_Financial_Planning_Tools, open JupyterLab in a dev environment and open/run the financial_planning_tools.ipynb file.

---

## Usage

This financial_planning_tools.ipynb application contains the following files/directories:
```
.ipynb_checkpoints/ - This directory contains checkpoint/auto-save files.
MFForecastTools.py - Monte Carlo Simulation python file/code that is imported into the main notebook.
financial_planning_tools.ipynb - This file contains the main source code for this application and can be ran in JupyterLab.
README.md - A README file that contains an overview of this application.
license.txt
```

---

## Main Source Code

### Part 1: Create a Financial Planner for Emergencies

In this portion of code, we will determine the individual's current value of their cryptocurrency wallet. We will use an API endpoint from a free crypto API "Alternative.me" by using the Python Requests library. Once we communicate with the endpoint, we will convert our payload into a readable JSON file and then convert it again into a Pandas DataFrame for further analysis.

```
load_dotenv()
btc_coins = 1.2
eth_coins = 5.3
btc_coins = 1.2
eth_coins = 5.3
monthly_income = 12000

btc_url = "https://api.alternative.me/v2/ticker/Bitcoin/?convert=USD"
eth_url = "https://api.alternative.me/v2/ticker/Ethereum/?convert=USD"

btc_response = requests.get(btc_url).json()
print(json.dumps(btc_response, indent=4, sort_keys=True))
print('-----')

eth_response = requests.get(eth_url).json()
print(json.dumps(eth_response, indent=4, sort_keys=True))
print('-----')
```
The following payloads is recieved (Note the indent and sort_keys arguments above to read in the data in an exceptable format):

![image](https://user-images.githubusercontent.com/96163075/152093726-35efd992-9076-4cc2-b877-37a9b2f30fc8.png)

![image](https://user-images.githubusercontent.com/96163075/152093752-b640514e-f1a9-4f61-ab50-998793a38358.png)

Next, we can now navigate the JSON response object and store certain values as variables in our notebook. (Please note: the Bitcoin and Ethereum prices indicated below can vary depending on when the main source code is executed. The values listed below are based on an execution date of 2/1/2022.)

```
# Navigate the BTC response object to access the current price of BTC
btc_price = btc_response['data']['1']['quotes']['USD']['price']

# Print the current price of BTC
print(f"The current price of Bitcoin is ${btc_price}.")
print('-----')
```
The current price of Bitcoin is $38615.0.

```
# Navigate the BTC response object to access the current price of ETH
eth_price = eth_response['data']['1027']['quotes']['USD']['price']

# Print the current price of ETH
print(f"The current price of Ethereum is ${eth_price}.")
print('-----')
```
The current price of Ethereum is $2779.82.

```
# Compute the current value of the BTC holding 
btc_value = btc_price*btc_coins

# Print current value of your holding in BTC
print(f"The current value of BTC holdings in USD is ${btc_value}.")
print('-----')
```
The current value of BTC holdings in USD is $46338.0.

```
# Compute the current value of the ETH holding 
eth_value = eth_price*eth_coins

# Print current value of your holding in ETH
print(f"The current value of ETH holdings in USD is ${eth_value}.")
print('-----')
```
The current value of ETH holdings in USD is $14733.046.

```
# Compute the total value of the cryptocurrency wallet
# Add the value of the BTC holding to the value of the ETH holding
total_crypto_wallet = btc_value + eth_value

# Print current cryptocurrency wallet balance
print(f"The current cryptocurrency wallet balance is ${total_crypto_wallet:.2f}.")
print('-----')
```
The current cryptocurrency wallet balance is $61071.05.

Our next step is to make an API call using the Alpaca SDK. The Alpaca SDK requires that we create a REST object that holds and stores our API keys. Once this REST object is created, we will use the Alpaca get_barset function to make an API call and recieve a payload (JSON). From there we will further analyze this member's portfolio. (Please note: snippets of code have been intentionally removed from the README.md file due to its length. Refer to main source code for entire application code.)

```
spy_shares = 110
agg_shares = 200

# Set the variables for the Alpaca API and secret keys
alpaca_api_key = os.getenv('ALPACA_API_KEY_ENV')
alpaca_secret_key = os.getenv('ALPACA_SECRET_KEY_ENV')

# Create the Alpaca tradeapi.REST object
alpaca = tradeapi.REST(
    alpaca_api_key,
    alpaca_secret_key,
    api_version="v2"
)

# Use the Alpaca get_barset function to get current closing prices the portfolio
# Be sure to set the `df` property after the function to format the response object as a DataFrame
stock_portfolio_df = alpaca.get_barset(
    tickers,
    timeframe,
    start = start_date,
    end = end_date,
    limit = 1000
).df

# Review the first 5 rows of the Alpaca DataFrame
stock_portfolio_df.head()
```
![image](https://user-images.githubusercontent.com/96163075/152094363-6bcedd10-cf7e-4a91-8663-75d08dfe2b2e.png)
```
# Calculate the total value of the member's entire savings portfolio
# Add the value of the cryptocurrency walled to the value of the total stocks and bonds
total_portfolio = total_stocks_bonds+total_crypto_wallet

# Print current cryptocurrency wallet balance
print(f"The total value of this member's entire savings portfolio is ${total_portfolio:.2f}.")
```

The total value of this member's entire savings portfolio is $132044.25.

Finally, to determine if this individual has enough savings for an emergency fund, we can set emergency_fund_value equal to their monthly income times 3 and generate an if-else statement. In this case, this member has enough funds for an emergency fund!

```
emergency_fund_value = monthly_income*3
# Evaluate the possibility of creating an emergency fund with 3 conditions:
if total_portfolio > emergency_fund_value:
    print(f"Congratulations! You have enough money in this fund for emergency purposes.")
    print('-----')
elif total_portfolio == emergency_fund_value:
    print(f"Congratulations on acheving this important financial goal of saving enough money for emergency purposes!")
    print('-----')
else:
    print(f"You currently need an additional ${(emergency_fund_value - total_portfolio):.2f} in your portfolio to have a healthy emergency savings fund.")
    print('-----')
```

## Part 2: Create a Financial Planner for Retirement
(Please review main source code file for entirety of application code.)




















