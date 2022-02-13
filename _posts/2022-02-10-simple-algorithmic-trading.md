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
*If the trading logic is not clear just look on the charts showing the trads down the road of thos blog

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


Now lets run the backtest and look on an example:
Black triangel - Open position (up is Buy, down is Sell)
Red triangle is a closed position in a loss, green is closed position in a gain. 

![30M with MA trades for 2022](/images/a_30m_ma_25_50_for_2022.png)
We can clearly see what we tought earlier, we our bot will make lots of small losing trades when the market go sideways and profit in when the market trending.

We can also zoom in to look on the trading logic:
![30M MA for Jan 19 to 25, 2022](/images/a_30m_ma_25_50_jan_19_to_25_2022.png)


### Another Strategy
Since our bot loss money in market that gos sideways and I have feeling that BTC/USD is moveing more sideways. I will add another strategy to our backtest. 
Since we already have a strategy to catch trends lets try to find a strategy that works better in a sideways market (and remember we want to keep it simaple):
How about Bollinger Bands, the *trading logic* will be:
Buy onc the price cross above the moving average after tuching the lower bands, exit once price tuch the upper bands.  
Sell once the prce cross under the moving average after tuching the upper bands, exit once price tuch the lower bands.  

Here is our Bollinger strategy for 2022:
![30M with bollinger trades for 2022](/images/30m_bollinger_100.png)

And lets focus in too clearly see trades:
![30M  bollinger for Jan 19 to 25, 2022](/images/a_30m_bollinger_100_jan_19_to_25_2022.png)


So for the Bollinger strategy will use 100 and 500 for the moving avergae (to calculate the bollinger bands) and use the default multiplier of 2 for the standard deviation.  


## The result
After runing all our strategy on the different tiem frame we get:

|	Strategy	|	PnL (in points)	|	Pnl percent mean	|	Return on $1	|	Wining percentage	|	Average % gain per trade	|	Average % loss per trade	|	Number of trades
|	:---: 	|	:---: 	|	:---: 	|	:---: 	|	:---: 	|	:---: 	|	:---: 	|	:---: 
|	MA 25&50 2H	|	44,692	|	0.64%	|	3.16	|	28.21%	|	6.5%	|	-1.65%	|	234
|	MA 250 500 2H	|	41,935	|	4.45%	|	2.89	|	21.21%	|	30.85%	|	-2.66%	|	33
|	Bollinger MA 100 30M	|	47,699	|	0.31%	|	2.40	|	64.01%	|	2.7%	|	-3.95%	|	389
|	Bollinger MA 500 30M	|	14,757	|	0.73%	|	1.54	|	66.25%	|	4.44%	|	-6.54%	|	80
|	MA 250&500 30M	|	1,684	|	0.51%	|	1.49	|	19.11%	|	10.3%	|	-1.8%	|	157
|	Bollinger MA 100  2H	|	27,019	|	0.54%	|	1.38	|	64.77%	|	4.3%	|	-6.36%	|	88
|	MA 25&50 5M	|	-25,697	|	0.0%	|	0.94	|	22.34%	|	0.96%	|	-0.27%	|	7255
|	Bollinger MA 500 5M	|	-6,250	|	0.02%	|	0.94	|	59.85%	|	1.74%	|	-2.54%	|	543
|	MA 25&50 30M	|	-40,279	|	0.01%	|	0.86	|	21.55%	|	2.61%	|	-0.7%	|	1151
|	Bollinger MA 500 2H	|	-14,668	|	-1.74%	|	0.61	|	62.5%	|	8.2%	|	-18.3%	|	16
|	MA 250&500 5M	|	-29,938	|	-0.06%	|	0.36	|	15.92%	|	2.73%	|	-0.59%	|	1300


After sorting by gain for every $1 investment will focus on the top three result.  
The MA 250 500 2H	 (moving average fast: 250, slow :500) on 2 hours time frame traded only 33 times so will leave it out for now.  
We have too strategies, one that use MA and another that use bollinger. Its interesting too see that the MA strategy win only 21.21% of the times but when its win its win big. On the other hand the bollinger strategy win 64.01% of the time, but with an average gain of 2.7% per trade and loss of -3.95% per trade. 

So the two strategyis are quite the opposate, one trades losts of lossers for small loss and big gains. The other, trade lots of winners for small gains and bigger losess. I usually prefer visulasation (charts) over dry numbers. 

Bollinger             |  Moving Average Cross
:-------------------------:|:-------------------------:
![](/images/bollinger_hist_pct_change.png)  |  ![](/images/ma_hist_pct_change.png)

I defently like the MA cross historgam better. There is what to work with and no one ever bloww up an account and went out of businss from accoumulationg small loss.



Bollinger             |  Moving Average Cross
:-------------------------:|:-------------------------:
![](/images/1_dollar_invested_bollinger.png)  |  ![](/images/1_dollar_invested_ma.png)

Both strategiyes have times of big loss, bollinger at the begening of 2021 and MA at the last part of 2020. forther invastigation can help us find a way to try and avoide some trades. 


I also like to look on box chart. What I plot here is the maximum gain and loss the poition was at the the final result of the trade 
![MA trades as box for 2019](/images/ma_box_2019.png)

Looking at the charts my feelings is that I should go with the MA strategy. There is a lot of room to improve. I should test with a limit order of 2x or 3x  (meaning if I risk a dollar exit if the profit is 2) as we can see lots of black tail to the upsie. 
I also think that with 28.21% seccess rate there is much room to improve. Just need to find another parameter that will say when NOT to trade (maybe a future post)

And here is the MA strategy if we started in 2022: (made 15 cent on the dollar)




### What next
I will deply the bot to the cloud and let it run live for maybe a week.  
Than, I will write another blog with the result, see if there are any improvemnts to make and run it again.  
Once I have some time I am planing to build a dashbord for anyone to see the result live.

Stay tund.
