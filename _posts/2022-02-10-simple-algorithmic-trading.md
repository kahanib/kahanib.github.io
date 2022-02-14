# Simple Algorithmic Trading

### Goal
My goal here is to build a very simple trading algorithm that can trade crypto (although it can be used to trade any asset) and show how to: 1. anlyse the trading result, 2. make changes accordingly, and 3. eventually be deployed to run on the cloud and show real time results.

Most people think that a trading algorithm should be very sophisticated with thousands of lines of code and complicated machine learning that can only be done using GPU. In reality, some are using complicated AI for trading, and some AI are making money, while some are not. 
I will save my thoughts on why some succeed and some do not for another blog post, but the point is this: you can write a trading algorithm bot that will run on a very simple code (simplae logic), AND it will still work. 

### Why Use the Bot to Trade Crypto?
I am not a heavy crypto trader. My backround is more in traditional assets however, I think that trading bots have an advantage in trading crypto over stocks. 
1. The crypto market is open 24/7, and as a human trader, this could drive me insane. It's emotionally hard to trade in a market that can move at any given time, even on the weekend.  For a bot, it doesn't matter, it's even an advantage. Why? Because we know that if we put a stop at X, we'll be out at X, and not X minus 100. On the other hand, when trading stocks we can wake up with a gap down and be out with a much bigger dent to our capital than originaly planned. 
2. Another advantage is that a lot of crypto exchanges have a nice and simple API and we don't need much margin or maintenance capital to run our bot live. We can simply trade live without much risk comparing to let's say the future market, which is also a good candidate for algorithmic trading bots.

### Trading Logic
As mentioned above, I am thinking of writing another blog post that will focus more on the trading side and logic behind the main ideas of this algorithm. 
For now, the idea is to focus on simplicity, but we still want our bot to have positive PnL. 
The trading strategy will be very simple using two moving averages, fast and slow.
1. Buy (long): when the market crosses above the fast moving average (fast MA), if the slow moving average (slow MA) is under the fast MA, use the slow MA as trailing stop.
2. Sell (short): When the market breaks down (crosses under) the fast MA given that the slow MA is above the Fast MA. Use the slow MA as a trailing stop.  
   
In addition, we may use a limit (take profits) on both trading directions. For now we'll leave this part (limit) for later, perhaps we'll use it, perhaps not. We'll see.
Our trading strategy will be based on trend following. The bot will likely have profitable trades when the market is trending, and lose money by taking lots of small losses when the market moves sideways. 
*If the trading logic is not clear, just look at the charts showing trades down the road which you'll see on my blog.


### Data
Cryptodatadownload (https://www.cryptodatadownload.com/data/binance/) is a good resource. They have data sets for the main crypto coins which include, Daily, Hourly and Minutes.
I am using the One Minute data set. I have downloaded it in a CSV format and cleaned it with Python.
For the 5 Minutes time frame, the first data point is September 9, 2019 and the last data point is: Feb 7, 2022. This contains 252,963 data points (for 30 minutes we have 42,165 data points, and 10,548 for the 2 hours).

### Parameters
There are many parameters we can play with, let's focus on the main one: we will use two moving averages, fast and slow. So we need to make a decision. Also we need to decide about the time frame, for example, how often do we want our bot to check the price?

What to consider when chosing time frames.
As we can logicaly assume, and can see in the charts below, as the time frame is bigger, the percent changes from one point to another and would likely get bigger. By using 30 minute, instead of 5 minute time frames, we give the market more time to move, therefore allowing for greater price moves. The advantage is that bigger time frames mean less noise, but could also mean that the market has more time (and room) to move against us before our bot checks the price and makes a decision.
![2H vs 30M](/images/01-5_and_30Mpct_change.png)
I will play with three different time frames:
- 5 Minutes
- 30 Minutes 
- 2 Hours

We'll run tests on all three time frames seperately and analyse the results.

Another parameter our trading bot needs is moving averages (fast and slow). The same logic applies here, bigger moving averages mean less sensitivity to price movement. Our bot will be slower to react, but we'll have less noise. Let's try two different sets: one with fast=25 and with slow=50, and another with fast=250 and slow=500.
Again the possibilities (numbers of combinations) are endless, so I will just pick two and see how it plays out.


Now let's run the backtest and look on an example:  
Black triangle - Open position (up is Buy, down is Sell)  
Red triangle - Closed position is a loss, green is closed position in a gain. 
![30M with MA trades for 2022](/images/a_30m_ma_25_50_for_2022.png)
We can clearly see what as we thought earlier, that our bot will make lots of small losing trades when the market goes sideways and profit when the market is trending.

We can also zoom in to look on the trading logic:
![30M MA for Jan 19 to 25, 2022](/images/a_30m_ma_25_50_jan_19_to_25_2022.png)


### Another Strategy
Since our bot loses money when market goes sideways (and I have a feeling that BTC/USD is moving sideways alot within the 5 minute time frame) I will add another strategy to backtest. 
Since we already have a strategy to catch trends, let's try to find a strategy that works better in a sideways market (remember, we want to keep it simple!):
How about Bollinger Bands. The *trading logic* will be:  
Buy when the price crosses above the moving average after touching the lower bands, and exit once price touches the upper bands (limit) or lower bands (stop).    
Sell (short) once the price crosses under the moving average after touching the upper bands, and exit once price touches the lower bands (limit) upper bands (stop).  

Here is our Bollinger strategy for 2022:
![30M with bollinger trades for 2022](/images/30m_bollinger_100.png)

And let's focus in to clearly see trades:
![30M  bollinger for Jan 19 to 25, 2022](/images/a_30m_bollinger_100_jan_19_to_25_2022.png)


For the Bollinger strategy, we'll use 100 and 500 for the moving average (to calculate the Bollinger bands) and use the default multiplier of 2 for the standard deviation.  


## The Result
After runing all our strategies on the different time frames, we get:

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


After sorting by gains for every $1 investment, we'll focus on the top three results.  
The MA 250 500 2H	(moving average, fast: 250, slow: 500) for a 2 hour time frame traded only 33 times, so we'll leave it out for now.  
We have two strategies, one that uses MA, and another that uses Bollinger. It's interesting too see that the MA strategy wins only 28.21% of the time, but when it wins, it wins big. On the other hand, the Bollinger strategy wins 64.01% of the time, but with an average gain of 2.7% per trade, and loss of -3.95% per trade. 

So the two strategies are quite the opposite; one trades lots of losers for small loss and big gains. The other trades lots of winners for small gains and bigger losess. I usually prefer visualisation (charts) over dry numbers. Start with histogram of all Pnl as percentages.

Bollinger             |  Moving Average Cross
:-------------------------:|:-------------------------:
![](/images/bollinger_hist_pct_change.png)  |  ![](/images/ma_hist_pct_change.png)

I definitely like the MA cross historgam better. There is something to work with and usually no one ever blows up an account and goes out of business from accumulating small losses.
Another interesting chart is the compound PnL in percentage (or how much will you make on investing $1)
for example here is the MA strategy for 2022:
![Return Over $1 invested in 2022](/images/1_dollar_invested_ma_2022.png)

Now, compare both strategies:

Bollinger: 
![](/images/1_dollar_invested_bollinger.png)

Moving Average Cross:
![](/images/1_dollar_invested_ma.png)

Both strategies have times of big loss, Bollinger at the beginning of 2021, and MA at the last half of 2020. Further investigation can help us find a way to try and avoid some trades.  

I also like to look at box charts. What I plot here is the maximum gain and loss that the position was at, and the final result of the trade. 
![MA trades as box for 2019](/images/ma_box_2019.png)

Now let's look at both strategies for 2020 trades
Bollinger             |  Moving Average Cross
:-------------------------:|:-------------------------:
![](/images/bollinger_box_2020.png)  |  ![](/images/ma_box_2020.png)

Looking at the charts, my feeling is that I should go with the MA strategy because there is a lot of room for improvement. I should also test with a limit order of 2x or 3x. This means if I risk $1, I'll exit if the profit is $2 or $3, since we can see lots of black tail to the upside. 
I also think that with a 28.21% success rate, there is much room to improve. We just need to find another parameter that will tell us when NOT to trade (perhaps a future post).


### What's Next
I will deploy the bot to the cloud and let it run live for maybe a week or two.  
Then, I will write another post with the result, noting if there are any improvements to be made, then run it again.  
Once I have more time, I'm planning to build a dashboard for anyone to see the result live.

Stay tuned.
