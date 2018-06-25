title: Anatomy of a JForex Strategy
author: Hugh Pearse
date: 2018-06-25 19:31:01
tags:
---
## Prior Art
Before writing this I would like to highlight that a very good description of JForex strategies has been explained on the Quantasian website [here][1], [here][2] and [here][3].

## Introduction
JForex strategy programming involves writing a single java class. The JForex API provides an interface called "com.dukascopy.api.IStrategy". All strategies must be an implementation class for the IStrategy interface. The IStrategy interface contains a list of public methods which all strategies should implement: 
- onStart
- onTick
- onBar
- onAccount
- onMessage
- onStop

``` java
import com.dukascopy.api.*; 
public class EmptyStrategy implements IStrategy { 
    public void onStart(IContext context) throws JFException { } 
    public void onTick(Instrument instrument, ITick tick) throws JFException { } 
    public void onBar(Instrument instrument, Period period, IBar askBar, IBar bidBar)
        throws JFException { } 
    public void onMessage(IMessage message) throws JFException { } 
    public void onAccount(IAccount account) throws JFException { } 
    public void onStop() throws JFException { } 
}
```

## Strategy onStart method
This is the first method that is called when the execution environment calls your strategy and is only called once at the very beginning. The onStart method expects an IContext object as a parameter. This object is a core JForex object and a gateway to access many important components of the JForex system such as the ordering engine, charts, console, indicators etc. When onStart is called it is the only time that this object will be set.

## Strategy onTick method
This method is called for each and every instrument that your JForex execution environment is subscribed to. A tick denotes a market's smallest possible price movement. A price change from 1.2345 to 1.2346 would represent one tick. Some markets might measure price movements in minimum increments of 0.25. So for them a price change from 450.00 to 450.25 is a tick. The method expect 2 parameters: an Instrument and an ITick. The Instrument object allows you to detect at runtime which currency pair is being referenced in the ITick object. And the ITick object allows you to access the price and volume information for that instrument. The onTick method is usually where the buying and selling happens.

## Strategy onBar method
onBar is similar to onTick. OnBar is called for every subscribed instrument, but with an extra dimension: onBar is also called for every time period. onBar expects 4 parameters: Instrument, Period, IBar and another IBar. The Instrument object contains information so that you can detect at runtime which currency pair is being referenced in the other parameters. The Period object describes the period of time being used to calculate the buying and selling prices in the IBar objects (1 minute, 1 day etc). The bid IBar contains the price information stating the price people are willing to pay, and the volume stating how many people are willing to pay it. The ask IBar is similar to the bid IBar, except the ask IBar has information about what people are selling. You are probably wondering what happens when multiple time periods overlap. The methods are simply called multiple times. For example for time periods 1, 2, 5, 10 it would be called as follows: 1min [1min 2min] 1min [1min 2min] [1min 5min] [1min 2min] 1min [1min 2min] 1min [1min 2min 5min 10min].

## Strategy onMessage method
This method is called when the Dukascopy server sends your strategy a message such as letting you know that your order has been filled. Messages sent are not guaranteed to be received. They are sent once from the server to your strategy and if for whatever reason they are not received, then they are simply discarded. The onMessage object expects an IMessage object. The IMessage object contains things like message type, message reason, related order.

## Strategy onAccount method
This method is called irregularly and only when your account update information is received. The IAccount object is not connected live to your account in the server. It is just a snapshot in time of your account information. For example, if you keep a local copy of an IAccount object within the scope of the strategy. Do some trading to change your balance. Then ask the same IAccount for account balance information, you will not see a change. This is why you should always update your strategies' local copy of the IAccount object instance from within the onAccount method.

## Strategy onStop method
This method is called once the strategy receives a stop command. You can use this to perform a clean up when the strategy is closing.

## Filtering
Filters are usually placed in the onTick and onBar methods. The standard practice is to filter out method calls for periods or instruments that you don't want with a simple if-true-return statement. For example if your execution environment is subscribed to 2 instruments, and you have seperate strategies for each instrument, you might include a filter such as this:

``` java
if (!instrument.equals(selectedInstrument)) {
    return;
}
```

Or if your strategy only allowed maximum 1 order open at a time you might have something like this:

``` java
if (!engine.getOrders(selectedInstrument).isEmpty()) {
    return;
}
```

## JForex Eclipse Project
Dukascopy have made available a [JForex Software Development Kit][4] (JForex SDK) allows the user to use JForex API without JForex client. This is an eclipse project that can be downloaded from the Dukascopy website. The JForex SDK has an example class JFOREX-3-SDK/src/singlejartest/Main.java which is a test execution container for backtesting strategies. Main.java calls an example strategy called MA_Play.java. When the execution container is started it calls client.startStrategy() which in turn calls the public methods in your class that implement the methods of the IStrategy interface.

## Subscribing to Instruments
In order for an instrument to be passed as a parameter to a strategy's onTick() method the execution environment must have the instruments configured first. In the Main.java file in the JForex SDK there is an example method called [subscribeToInstruments()][8] which will inform the execution environment which instruments' tick data to use. JForex downloads this tick data on-the-fly based on the time period being used. An example of this might be:
``` java
Set<Instrument> instruments = new HashSet<>();
instruments.add(Instrument.EURUSD);
client.setSubscribedInstruments(instruments);
```

## Backtesting Specific Time Periods
It is possible to test a strategy with data from a specific time period from within the driver class Main.java. This can be done by making a call to the [client.setDataInterval()][6] method. This method sets the DataLoadingMethod to use for ticks generation and time interval when to start and end testing process. This method expects 3 parameters: dataLoadingMethod, from, to. The parameter dataLoadingMethod lets you specify which type of data to load. The "from" and "to" parameters allow you to configure the time range. An example of this might be:
``` java
client.setDataInterval(DataLoadingMethod.ALL_TICKS, dateFrom.getTime(), dateTo.getTime());
```

## Starting Money
It is also possible to configure the starting cash of a strategy from the Main.java class by making a call to [client.setInitialDeposit()][7]. An example of this might be:
``` java
client.setInitialDeposit(Instrument.EURUSD.getSecondaryJFCurrency(), 100000);
```

## Placing an Order
In the onTick method in the MA_Play strategy placing a market order is as simple as calling the [engine.submitOrder()][5] method. The submitOrder method has a few overloaded variations. The simplest is a market order. A market order has 4 parameters: label, instrument, orderCommand and amount.
- Label must be unique for the given user account among the current orders. Allowed characters: letters, numbers and "_". Label must have at most 256 characters.
- Instrument is the currency pair to be bought/sold
- orderCommand is the type of order BUY/SELL
- amount is the amount in millions for the order

Here is an example of an if statement making a market order based on a 50:50 chance.

``` java
if(Math.random() < 0.5){
    engine.submitOrder("Uniq_Label_BUY", selectedInstrument, IEngine.OrderCommand.BUY, 0.0010);
} else {
    engine.submitOrder("Uniq_Label_SELL", selectedInstrument, IEngine.OrderCommand.SELL, 0.0010);
}
```

[1]: https://www.quantisan.com/getting-started-learning-jforex-programming/ "Quantisan JForex 1"
[2]: https://www.quantisan.com/anatomy-of-a-jforex-strategy-part-1/ "Quantisan JForex 2"
[3]: https://www.quantisan.com/anatomy-of-a-jforex-strategy-part-2/ "Quantisan JForex 3"
[4]: https://www.dukascopy.com/wiki/en/development/get-started-api/use-jforex-sdk/download-jforex-sdk "JForex SDK"
[5]: https://www.dukascopy.com/client/javadoc/com/dukascopy/api/IEngine.html#submitOrder(java.lang.String,%20com.dukascopy.api.Instrument,%20com.dukascopy.api.IEngine.OrderCommand,%20double) "Order API"
[6]: https://www.dukascopy.com/client/javadoc/com/dukascopy/api/system/ITesterClient.html#setDataInterval(com.dukascopy.api.system.ITesterClient.DataLoadingMethod,%20long,%20long) "Set backtesting period"
[7]: https://www.dukascopy.com/client/javadoc/com/dukascopy/api/system/ITesterClient.html#setInitialDeposit(com.dukascopy.api.ICurrency,%20double) "Set initial deposit"
[8]: https://www.dukascopy.com/client/javadoc/com/dukascopy/api/system/IClient.html#setSubscribedInstruments(java.util.Set) "Set subscribed instruments"
