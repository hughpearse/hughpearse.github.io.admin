title: Automated Forex Trading (MetaTrader & JForex)
author: Hugh Pearse
date: 2018-06-15 10:01:55
tags:
---
## Introduction
Foreign Exchange (forex) trading is the act of buying or selling currencies. The exchange rate is the rate at which one currency will be exchanged for another. Supply and demand for any given currency, and thus its value, are not influenced by any single element, but rather by several. 

Algorithmic trading is a method of executing orders using automated pre-programmed trading instructions. Most forex market trades are now performed by algorithms, not humans, and they've already caused [crashes][0].

## Foreign Exchange Market 
The [foreign exchange market][1] is the "place" where currencies are traded. It is by far the largest market in the world. One unique aspect of this international market is that there is no central marketplace for foreign exchange. Rather, currency trading is conducted electronically over-the-counter (OTC), which means that all transactions occur via computer networks between traders around the world, rather than on one centralized exchange. The market is open 24 hours a day, five and a half days a week

## The Interbank Network
The interbank network contains the central and major banks. They form the core of the Foreign Exchange network. 

## Market Makers (MM)
To be able to start making trades as a private individual you need to sign up to a broker. Your broker provides access the interbank network to start making trades with the rest of the world. Brokers are the market makers. When you place a trade order, the MM will first try to match up your trade with another client of the MM, bypassing the interbank network all together. If this is successful the MM now has a free hedge trade and is at no risk of loss. If the MM can't match up the trade amongst its own clients, they will usually open a trade in the opposite direction to yours (a hedge trade) on the interbank network to protect themselves or even profit from any losses your trade may attract. MM's have access to the cheaper interbank network market prices. The MM adds their commission onto the interbank price and passes that on to you. The difference in prices is called the "Bid-Ask spread". The spread is one of the ways MMs make a profit.

## Currency Pairs
A currency pair is the quotation of the relative value of a currency unit against the unit of another currency. The base currency is the first currency appearing in a currency pair quotation, followed by the second part of the quotation, called the quote currency: BBB/QQQ. From its inception in 1999 and as stipulated by the European Central Bank, the euro has first precedence as a base currency. Therefore, all currency pairs involving it should use it as their base, listed first. For example, the U.S. dollar and Euro exchange rate is identified as EUR/USD.

## Price Quotations
The quotation "EUR/USD 1.3225" means that for every Euro you will get 1.3225 U.S. Dollars. And for every 1 U.S. Dollar you will get 1/1.3225=0.7561 Euro.

## Electronic Trading Platform
An electronic trading platform is a software program that can be used to place orders for financial products over a computer network through a financial intermediary.

## Automated Forex Trading
Automated forex trading is a system of trading foreign currencies with a computer program based on a set of analyses that help determine whether to buy or sell a currency pair at a given time. Automated forex trading uses a computer program that the trader configures to make decisions based on a set of rules. The biggest advantage of forex trading systems is that they take the emotion out of the process, which tends to reduce the behavioral finance biases that negatively impact investment decision making. Traders can also backtest trading system to see how they perform based on past data.

## MetaTrader
MetaQuotes' [MetaTrader][2] is undoubtedly the most popular software for online trading available for free. It is primarily used for Forex market trading. MetaTrader uses its own proprietary MetaQuotes Language (MQL) designed for developing automated trading robots, technical market indicators, scripts and function libraries within the MetaTrader software. It can be downloaded for free from the official website and through most [Brokers' websites][5].

![MetaTrader Screenshot](/images/pasted-18.png)

Metatrader also comes with the ability to download example code from the online community for free. Simply right click on "Scripts" and press "Online Library".

![MetaTrader Community Examples](/images/pasted-17.png)

This will then display a list of examples available to download. These examples can be executed and also modified.

![MetaTrader Community list](/images/pasted-19.png)

To modify the examples they can be easily loaded in to the MetaEditor tool. This will show the code which can be modified, compiled and executed.

![MetaEditor Example](/images/pasted-20.png)

## JForex
Dukascopy is a broker that also provide their clients with their own [JForex trading platform][3]. JForex is a free software for manual and automated trading and/or developing and testing trading strategies based on the Java programming language. Dukascopy provide a sample Java project that can be imported directly into Eclipse for developing automated trading strategies.

![JForex Eclipse Project](/images/pasted-13.png)

The JForex platform tool also comes with its own lightweight integrated development environment for editing and compiling strategies in Java. 

![JForex Platform Tool](/images/pasted-14.png)

Dukascopy also runs a [monthly strategy contest][6] for the best performing trading strategies. The historical submissions can be easily downloaded through the website or through JForex Platform Tool by navigating to the "Strategies" tab and pressing the folder icon.

![JForex Strategy Competition](/images/pasted-15.png)

Note that a lot of these strategies may have been created with the assistance of the [VisualJforex web tool][4] so a visual strategies' source code may be difficult to understand as it is automatically generated by the VisualJforex software.

![VisualJForex Tool](/images/pasted-16.png)

## Testing
Both MetaTrader and JForex have the ability to create trading robots (strategies). These robots can be backtested locally using historical financial data, or alternatively they can be backtested online in your Broker's account using either simulated money with a demo account or real money with a live account. The main difference is that the simulated money does not impact on the simulated financial market, whereas using real money impacts supply and demand, and as a result, the price.

## Summary
These tools give software developers the tools to trade (and even sometimes profit) on the Forex market. There are examples available in both tools and Brokers often offer a demo account to run simulations for free.

[0]: https://en.wikipedia.org/wiki/Flash_crash "Flash Crash"
[1]: https://www.theforexguy.com/how-forex-trading-works/ "The Forex Market Structure"
[2]: https://www.metaquotes.net "MetaTrader"
[3]: https://www.dukascopy.com "JForex"
[4]: https://www.dukascopy.com/vjforex/ "VisualJforex web tool"
[5]: https://admiralmarkets.com/trading-platforms "Admiral Markets"
[6]: https://www.dukascopy.com/strategycontest/?language=en "Dukascopy Strategy Contest"