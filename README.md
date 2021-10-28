# Textual analysis of the tweets in order to predict the trend in the value of Bitcoins

## Index
1. [Introduction](#intro)<br>
2. [Data collection](#dc)<br>
3. [Preprocessing](#pp)<br>
4. [Classical approach of time series](#tm)<br>
5. [Exploratory analysis](#ea)<br>
6. [Predictive analytics](#pan)<br>
7. [Process automation](#pau)<br>
8. [Conclusions](#conc)<br>


## 1. Introduction<a id=intro> </a>

Cryptocurrencies are virtual currencies that are not controlled by any central authority and are not accepted as legal tender in most countries. They can be used, by those who accept them, as a bargaining chip to purchase products and services. Theirs is a very thin market, which moves very quickly and therefore increases the volatility of prices.

Bitcoin (BTC) is the best known cryptocurrency. In order to access it, you need to have a *wallet* on your PC or smartphone, i.e. a virtual wallet managed by a personal password. The entire network is based on the *blockchain*, the database in which all confirmed transactions are recorded.

Since their valuation is not regulated by financial mechanisms, the main factor affecting the fluctuation of their value is the relationship between supply and demand. Other factors that can contribute to the price change are news disclosed by the mass media or social networks, technical server problems (hacker attacks) or global political and economic events.

Our study focuses on the monitoring of tweets written by users of the social Twitter relating to news concerning BTC in the period between 2014 and 2020. The profiles were chosen among large investors, *whales*, authoritative users who hold about 40% of the entire market.

The purpose of the analysis is to present and address the specific problem of the classification of the collected data. We try to understand which of these positively or negatively affect the value of the currency, with particular emphasis on forecasting the trend.


## 2. Data collection<a id=dc> </a>

To carry out the project and data analysis, the Python 3 language is used. It uses a data extraction technique called *Web Scraping*, which involves the collection of useful information directly from Web pages. In our case we rely on APIs that provide this data retrieval technique; in particular as regards tweets, the *twitterscraper* library is used which, given a profile, returns the tweets of the latter (with specific limitations). The *twitterscraper* query is parallelized to gain efficiency, in order to request tweets in parallel for each selected profile.
As for financial data, we rely on the *quandl* library, which allows you to download the adjusted closing values of the BTC / USD exchange rate.


## 3. Preprocessing<a id=pp> </a>

After data collection, we proceed with their preprocessing. The first stage of the process involves grouping tweets by publication date. The next step is the *text mining* process, broken down into the following steps:
* ***Cleaning***, i.e. the replacement of specific patterns within the text to be processed, such as hyperlinks or HTML tags.
* ***Tokenization***, or the reduction of the text into smaller parts called tokens.
* ***Stop words removal***, i.e. the removal of punctuation and stop words, words belonging to a specific language that serve as a link in certain sentences but are extraneous to the lexicon of the context to be analyzed. Among these are, for example, conjunctions, adverbs, words that appear in the text to be analyzed too frequently or too rarely.
The list of stop words relating to the English language is used, the language present in all the tweets we obtain. The complete list of punctuation symbols offered by the *string* library of the Python language is not exploited, since it also includes the $ symbol, which instead is decisive in the context relating to the BTC cryptocurrency. Therefore, $ is removed from the list and all other symbols are considered.
* ***Stemming***, or the reduction of words to their root. The words processed by stemming are separated and inserted in different lists. A list is created for each tweet, containing the words present in the specific tweet, including any duplicates. The words obtained at the end of this process contain only certain characters, of standard alphanumeric type and the symbols of $ and #, duly filtered because they indicate words inherent to the BTC context or hashtag, which you decide to take into consideration.
* ***Creation of the matrix of the stems***, that is a matrix with many rows as the number of days considered, as many columns as there are unique stems found in the tweets and containing the frequency of appearance of the stem among the tweets of that day.


## 4. Classical approach of time series<a id=tm> </a>

Before starting the exploratory analysis, it was decided to analyze the historical series of BTC prices in the period considered. The vector of interest containing the prices in the period considered, already obtained previously, is divided into three parts: train, test and validation in proportion *60%*, *20%* and *20%*. Unlike the usual division, which is done randomly, the temporal order of the observations is maintained in order to grasp their autocorrelation.
A grid of values is defined *(p, d, q)*, which indicates the order of the ARIMA models considered, *(p, d, q) = {0,. . . , 5} × {0, 1, 2} × {0,. . . , 5}*. For each of the possible combinations of the order of the parameters, the ability to make lag 1 predictions is evaluated. For each observation contained in the test set, an ARIMA model of the corresponding order is estimated on the data of the train set combined with already considered values of the test set and the prediction for the next value is made. The mean square error of the one-step predictions in the set of tests is then calculated, which we use as a metric of the goodness of the model. For combinations of parameters that lead to non-invertible models, for which the mean square error cannot be calculated, this indicator is set equal to +∞. The model with minor root mean square error is chosen, which turns out to be an ARIMA(0, 1, 0), also called Random Walk.
The predictive capacity of the Random Walk in the validation set, measured as accuracy based on the upward / downward forecast, returns *49.8%* as a result, a value consistent with the fact that a Random Walk has no capacity predictive, as it proposes the value observed today as a forecast for tomorrow.
A curiosity is given by the fact that the best ARIMA model obtained, the RW, respects the hypothesis of efficiency of the financial markets, according to which the information present in the historical series of the value has no use in predicting a its course
future.


## 5. Exploratory analysis<a id=ea> </a>

To proceed with the exploratory analysis, we choose to introduce a dichotomous variable that indicates a decrease in the price (value 0) or an increase (value 1) compared to the previous date instead of using the value of the current day's currency.
Bar graphs are used to represent the most frequent tokens and hashtags, distinguished between tweets associated with an increasing trend and others associated with a
decreasing trend.

![Figure 1](https://github.com/alefabris/bitcoin-prediction-NLP-twitter-sentimental-analysis/blob/main/chart/20%20token.png)

Figure 1: *Left: most frequent tokens in tweets linked to an increasing trend. Right: most frequent tokens in tweets linked to a downward trend.*

Please note that the *"link"* token appears very often as all the internet addresses contained in the tweets are replaced by the word in question.
In figure 1 we note how the word that appears most frequently is bitcoin, both in messages linked to an increasing and a decreasing trend.

![Figure 2](https://github.com/alefabris/bitcoin-prediction-NLP-twitter-sentimental-analysis/blob/main/chart/5%20hashtag.png)

Figure 2: *Left: most frequent hashtags in tweets associated with a growing trend. Right: Most frequent hashtags in tweets associated with a bearish trend.*

Figure 2 shows how the most frequent hashtags are #bitcoincash and #bitcoin, both as regards the tweets associated with increases and decreases in value.

A further graphical analysis via *WordCloud* shows us the frequency of the words most present in the tweets associated with the rise and fall in the value of BTCs through their size (Fig. 3).

![Figure 3](https://github.com/alefabris/bitcoin-prediction-NLP-twitter-sentimental-analysis/blob/main/chart/wordcloud.png)

Figure 3: *WordCloud with tweets associated with rising (left) or falling value (right)*


## 6. Predictive analytics<a id=pan> </a>

With the data collected, before continuing with the analysis, it was decided to divide the matrix of stems and the downward vector into three parts each, training, testing and validation set, respectively in the proportion *60%*, *20%* and *20%*, extracted randomly.
The first approach followed consists in the implementation of a dummy classifier, which performs the classification using the observation mode. This model appears to have an accuracy of *53.5%*.
A more sensible approach is based on the use of decision trees. Two types are implemented, one with the default hyperparameters proposed by the sklearn library, the other with the optimization of the following hyperparameters:
* *Maximum depth*
* *Minimum number of elements for each leaf*
* *Minimum decrease in impurities per split*
The values of the hyperparameters are chosen from a set of possible values. The tree with default hyperparameters has an accuracy of 53.9%, following optimization this indicator rises to *54.2%* (Fig. 4).

![Figure 4](https://github.com/alefabris/bitcoin-prediction-NLP-twitter-sentimental-analysis/blob/main/chart/tree.png)

Figure 4: *Classification tree with hyperparameter optimization*

The last approach discussed concerns logistic regression. Two different models are compared: logistic regression with likelihood penalty,
where one must choose an optimal penalty parameter, and logistic regression based on principal components to reduce dimensionality.
The penalized log-likelihood takes the following form:

![Figure 5](https://github.com/alefabris/bitcoin-prediction-NLP-twitter-sentimental-analysis/blob/main/chart/penalized%20log-likelihood.png)

in which the intercept is not penalized and where λ represents the penalty parameter to be optimized.
The logistic regression based on the penalized likelihood reaches an accuracy of *53.9%*, with the PCA, on the other hand, only *50.8%* is obtained.


## 7. Process automation<a id=pau> </a>

At the end of the analysis, a *pipeline* is created, a series of commands implemented in a single function to automate the work previously carried out. The pipeline takes the previously saved data relating to BTC prices, the dates associated with them, the original and processed tweets from a file and updates them with the data of the period from the last known observation to the day in which this procedure is called. .
At the end of the process, the value of the future trend forecast is returned, 0 if down, 1 if up.


## 8. Conclusions<a id=conc> </a>

By evaluating the accuracy of the various models, it can be concluded that the forecast of a financial index such as the BTC / USD is a goal that can be achieved with great difficulty. The study of investor sentiment, which influences the value through the change of supply / demand through their tweets, can give interesting insights to understand their movement. On the contrary, if taken individually, the same study does not provide enough information to make predictions that can be used for profit.
