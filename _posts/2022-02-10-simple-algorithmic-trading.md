# Simple Algorithmic Trading

### Goal
My goal here is to build a very simaple trading algoritim that will trade crypto (altought it can be use to trade any asset) and show how to anlyse the trading result, make changes accordily and eventually deploy to run on the cloude and show realy time result.

Most people think that a trading algo should be somehing very sufisticate with thusends of line of code and some complicated machine learnign that can only be done usgin GPU. In reality some are usign coplicated AI for trading and some AI are making money and some arn't. 
I will save my tought on why some seccedd and some not for another blog but the point is that you can also write a trading algorithum bot that will run on a very simaple code (simplae logic) AND that will still work. 

### Why the bot is trading it on crypto
Well, I am not a heavy crypto trader, my backround is more in treditional assets but:
I think that trading bot have a dvantage in trading crypto over stocks; 
The crypto market is open 24/7, as a humen trader this will drive me insane. Its emotunaly hard to trade a market that can move at any giving time, even on weekend. But, for a bot it dosen't matter, its even an advantqge: we know that if we put stop at X we will be out at X and not X minus 100. On the other hand, trading stocks we can wake up with a gap down and be out in a much bigger dent to our capitl than originaly palnd. 
Another advantage is that a lot of crypto exchange have a nice simple API and we don't need much margin or mintancne capital to try our bot live. We can trade live without much risk comparing to latsy say the future market how is alos a good candate for algorithmic trading bot.

### Trading Logic
As mention above, I am thinkning on writing another blog that will focus more on the trading side and logic behaind the main ideas of this algo. 
For now, the idea is to focus on simplisity, but we still want our bot to have positive PnL. 
The trading strategy will be very simple (Using too moving averge, fast and slow):
Buy (Long) when the market cross above the fast moving average (fast MA) if the slow moving average (slow MA) is under the fast MA. Use the slow MA as trailing stop.
Sell (short): When the market break down (cross under) the fast MA giving that the slow MA is above the Fast MA. Use the slow MA as a trailing stop. 
In addition, we may use a limit (take profit) on boh trading directoin. But, for now will leave this part (limit) for latte, maybe will useit maybe not, will see.
Our trading strategy will be base on trend following. The bot will probably have profitable trades when the market is trending, and loss money by takign losts of small loss when the market jsut move sideways. 

![2H vs 30M](/images/01-5_and_30Mpct_change.png)

### Data
cryptodatadownload (https://www.cryptodatadownload.com/data/binance/) is a good resurses, they have data sets for the main crypto coines which incloud Daily, Hourly and minuts
I am using the one minuts data set. I have download it in a CSV format and than clean it with Python. I have also upload a jupyter notebook with code I am usign in the following link:
For the 5 minutes time frame the first data point is Speptember 9, 2019 and the last is: Feb 7, 2022. containig  252,963 data points (30 minuts we have 42,165 data points and 10,548 for the 2 hours).

### Parameters
There are many parmaeters we can play with, lets focus on the main one. We will use two moving average, fast and slow, so we need to decide about that. Also we need to decide about time frame...i.g how often do we want our bot to check the price.

What to consider when choseing time frames.
As we can logicaly assum, and can see in the charts below, as the time frame is bigger the percent change from one point to another would probably get bigger (by using 30 minuts insted of 5 minuts time frame we give the market more time to move and so allowing for grater price moves). The advantage is that bigger time frame means less noise, but could also mean that the market have more time (and room) to move agains us before our bot check the price and make a decision.
In this blog I will play with three different time frames:
- 5 minuts
- 30 minuts 
- 2 Hours

Will run test on all the three time frames seperatly and analyse the result.

Another parmeter our trading bot need is the moving averags (fast and slow). The same logic applies here, bigger moving average mean less sensativity to price movment; our bot will be slower to react, but will have less noise. Lets try two different sets, one with fast=25, slow=50 and another with fast=250, slow=500.
Again the possabilits (numbers of combinations) are endless, so I will just pick tow and see how its going.


### What next
I will deply the bot to the cloud and let it run live for maybe a week.  
Than, I will write another blog with the result, see if there are any improvemnts to make and run it again.  
Once I have some time I am planing to build a dashbord for anyone to see the result live.
