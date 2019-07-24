# Twitter Sentiment Analysis ICC 2019 games in Singapore 

Twitter Sentiment Analysis was performed using the Twitter API to mine the tweets we wanted by hashtag. 

Information was then visualized from the twitter data we acquired, such as the overall sentiment of the tweets that contained that particular hashtag, as well as a word cloud representing the most common words.

Data was used for predictive modelling using Naive Bayes classifier.

## Connecting to the Twitter API and getting the needed Tweet data

The library `twitteR` was used to connect to the Twitter API, and then search for all tweets with the following hashtags:

1. #ManUtd
2. #ICC2019

![Twitter Search](https://i.gyazo.com/04cb5f59929ae5153eedf7c1b01fdf73.png)

2000 tweets for each Hashtag were retrieved, and **to ensure relevance and recency, only tweets after 1st July were searched for.**

## Initial Data Processing/Cleaning

The data was then stored as two dataframes, `manu_tweets` and `icc_tweets`, and the dataframes were then cleaned to be suitable for analysis, in a process which  involved:

- Cleaning the `text` column (where the content of the tweet primarily is), and removing emojis and special characters from it.
- Cleaning the column that held information on Device Type (`statusSource`) to make it suitable for analysis by removing HTML encoding.

## Visualizing Information on Device Type

There were a total of 48 Device types for `manu_tweets`, and 24 Device Types for `icc_tweets`. To reduce clutter, only the top 10 or so Device Types were visualized. Since the top 10 constituted the overwhelming majority of devices anyway, no significant data was lost.

The outcomes of the visualization are as follows:

![manu tweet viz](https://i.gyazo.com/7f189147fb048f3aa7f85ccade4efc83.png)

![icc tweet viz](https://i.gyazo.com/8657d3cc02512c5fe93f208d09dbe89e.png)

We see above that in the `manu_tweets` dataframe, the Twitter apps for Android and iPhone account for the most tweets. **What's interesting to note is that most of the time a tweet occurs on a mobile app, it is a retweet much more often than an original tweet.** On web clients, users are more likely to post original tweets, rather than re-tweet.

For the `icc_tweets` dataframe, the biggest thing that stands out is that compared to the #ManUTD hashtag, the #ICC2019 hashtag has a significantly higher proportion of re-tweets than original tweets.

**This could be because fans might be more likely to re-tweet about an event, rather than make an original tweet about it, but when it comes to football teams and people they have emotional investments in, they're more likely to make original tweets.**

## Word Cloud Building

The `wordcloud` library was used to create wordclouds.

A corpus of words from the `texts` columns in the dataframes `manu_tweets` and `icc_tweets` was built, and then subjected to even more cleaning:
- Removed all white space
- Removed Punctuation
- Removed Special Characters
- Removed stop words ("a", "the", "of", etc)
- Made all letters lower case

Word Clouds for both tweets were then generated:

![word cloud manu](https://i.gyazo.com/b2439f77e6813aa4752cce3820d9c3c1.png)

![word cloud icc](https://i.gyazo.com/626df521b110d3cb462ea9c49fa060c9.png)

## Sentiment Analysis

The `get_nrc_sentiment()` function from the `syuzhet` library was used to designate an emotional attribute to our tweets in each dataframe, which could then be graphically represented.

Tables were constructed summing up the scores for each emotion across all the tweets, and these 2 tables for each dataframe were visualized. 

![manu sentiment](https://i.gyazo.com/22104cb6e495a7871b0929e1bd20db45.png)

![icc sentiment](https://i.gyazo.com/7e971a5f34282ed25e9b01d14daba5b1.png)

## Naive Bayes Predictive Modelling

### Preparing the Datasets

1. Using the results from the `get_nrc_sentiment()` function, each tweet was assigned an attribute of "positive" or "negative", based on its overall sentiment.

2. A corpus was then created for the `text` column in both dataframes, cleaned, and then represented with a Document Term Matrix (DTM). 

3. 75:25 partitions of each dataframe, corpus and, document term matrix were made to create the train and test sets respectively. We also **narrowed down the the number of features by disregarding words which didn't appear in at least 3 unique tweets**, using the `findfreqTerms` function to identify frequent words, and then restricting the database to using only those.


### Using Binarized Naive Bayes

The Binarized Naive Bayes is a variation of the naive bayes algorithm where the frequency of a given term is replaced by a binary value instead, the logic being that when it comes to sentiment analysis, the *occurrence* of a word matters more than the *frequency* of said occurrence.

We thus wrote the following function that converted word presence or absence to a binary value, and then applied it to the existing DTMs to get the final training and testing DTM sets:

![Binary Conversion Function](https://i.gyazo.com/e1c8fadafa9f8a99d4695e710809c472.png)

### Naive Bayes Classifier Training

We then used the `naiveBayes` function from the `e1071` package to train the models.

Because Naive Bayes evaluates products of probabilities, we had to use Laplace 1 to assign non-zero probabilities to words which didn't occur in the samples.

We started by training the classifiers.

![classifier training](https://i.gyazo.com/bb991b7ce664a3e74277967ef9dfd4b4.png)

### Testing the classifiers 

The classifiers were then tested:

![classifier test](https://i.gyazo.com/49df7a4bd0378517dd6680f9f12bae65.png)

### Outcome of Naive Bayes Modelling

A truth table was created that tabulated the predicted sentiment label against what the actual sentiment label was based on the outcome from the `get_nrc_sentiment()` function.

Using the `caret` library, confusion matrices were then created and displayed to show the results of the predictive modelling.

![manu conf mat](https://i.gyazo.com/d77c3e6000a7220df14cdc3637a7d372.png)
![icc conf mat](https://i.gyazo.com/e33f55a237a1b077ab164405228f2e76.png)


Outcome of Naive Bayes model

The accuracy of the `icc_classifier` was 97.4%, and that of the `manu_classifier` was 92%.

We saw that the Naive Bayes algorithm did a decent job in analyzes the sentiments of the tweets in the test data, despite the assumptions of the Naive Bayes model.

## Possible expansions of this work in future projects

- Usage of Machine Learning algorithms other than Naieve Bayes, such as Support Vector Machine (SVM)
- Inclusion of emoticons in Sentiment Analysis
- Detection of Sarcasm

## Application of project to other uses

Just as information was mined using the #ManUTD and #ICC2019 hashtags, the same can be done to monitor the sentiments of users when it comes to a product, or with a brand.

This can be used for a company to monitor the reaction of people to a product of theirs. 

- Apart from their sentiment with the product, it could also **measure their anticipation in the days leading up to its release**. 
- Tweets could even by analyzed by the time they occur to understand what time users of that product are most active.
- **The overall sentiment score could be measured over time on a line graph**, and kept watch on to make sure users are generally satisfied with the product. 
- Even the Wordclouds might be useful, because these **could indicate trending topics among the users of your product, so you might know how to better target your advertisements.**
