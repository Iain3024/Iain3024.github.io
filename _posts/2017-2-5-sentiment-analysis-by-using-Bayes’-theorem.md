---
layout: post
title: Sentiment analysis by using Bayes’ theorem
modified: 2017-2-5
categories: [C#, Algorithm]
tags: [Sentiment analysis, Bayes’ theorem]
comments: true
---
The content in this blog is part of my assignment of Adaptive Business Intelligence during my postgraduate studying.

<!-- more -->
<a href="#dataset">1. Dataset </a>

<a href="#solutiondesign">2. Solution design </a>

<a href="#development">3. Development </a>

<a href="#results">4. Results </a>

<a href="#discussion">5. Discussion </a>

<a href="#conclusion">6. Conclusion </a>

----------------------------------------------------I am the split line o(^_^)Y------------------------------------------------

### <a name="dataset">1. Dataset </a>
<br>
The dataset for this task is downloaded from <a href="http://help.sentiment140.com/for-students/">here </a> for public use, which is tweets retrieved from Twitter. There are three datasets which contain 2,000 tweets, 7,000 tweets and 10,000 tweets for training and 2,000 tweets for testing. Same weight (equal number of comments) of both positive and negative tweets. 

### <a name="solutiondesign">2. Solution design </a>
<br>

To identify a tweet is positive or negative, the possibility of it is positive or negative needs to be calculated by using Bayes’ theorem. To calculate the possibility, there are two datasets which have been classified as positive and negative are needed as evidence. When identify a new tweet, the tweet is split up to words and a value Q which stands for the possibility of the word is from a negative tweet or a positive tweet is calculated by using Bayes’ theorem. Q is the result of the comparison of the possibility of the word from a negative tweet and the possibility of the word from a positive tweet. If this Q is greater than 1, which means the possibility of the word is from a negative tweet is larger than the possibility of the word is from a positive tweet. The word comes more likely from a negative tweet. Otherwise, the word is more likely from a positive one. 

As a result,

If Q is greater than 1, the word is negative.

Otherwise, if Q is less than 1, the word is positive.

This possibility is calculated as for each word. The hypothesis h1 and h2 are:

H1 = Hnegative = 1/2

H2 = Hpositive = 1/2

Therefore, the tweet is either negative or positive.

P (h1) = P (h2) = 1/2

Then the training dataset is needed. In the training dataset, there are 5000 negative tweets and 5000 positive tweets. These tweets will present the evidence which are needed to identify if the word is come from a negative tweet or a positive one.

For example, a word ‘awesome’ from a tweet being checked. The word might be appearing 500 times in the negative tweets of the training dataset and 2500 times in the positive tweets. 

P (E|h1) and P (E|h2) is calculated below:

P (E|h1) = 1 / 10 

P (E|h2) = 5 / 10 

Using Bayes to calculate P (h1|E) and P (h2|E).

P (h1|E) = P (h1) * P (E/h1) / P (E)

P (h2|E) = P (h2) * P (E/h2) / P (E)

Here P (E) is the tweet that is under checking. So it is one. 

P (h1|E) = 1/2 * 1/10 / 1 = 1/20

P (h2|E) = 1/2 * 5/10 / 1 = 5/20

Therefore, the two results showing the possibility of the word is negative or positive. comparing the two results to determine if the word is negative or not.

If P (h1|E) > P (h2|E), the word is negative;

If P (h1|E) <= P (h2|E), the word is positive.

Using Q to stand for the result of this comparison.

Q = P (h1|E) / P (h2|E)

If Q is greater than 1, the word is more likely from a negative tweet. Otherwise, the word is more likely from a positive one.

Q = P (h1|E) / P (h2|E) = 1/20 / 5/20 = 1/5 = 0.2

Q is smaller that one, that means the word ‘awesome’, is more often in the positive tweet. 

Calculate Q for every word in the tweet and calculate the average Q of these word as the possibility of the tweet.

Q overall = sum (calculate Q for every word) / count (every word)

If Q overall greater than 1, it is a negative tweet

Otherwise, the tweet is positive.

All in all, a tweet can be classified by following the steps shown below:

Step 1, split tweet up to words; 

Step 2, calculate Q for each word; 

Step 3, calculate the average of Q as Q overall;

Step 4, compare Q overall with 1 to decide if the tweet is negative or positive.

The architecture diagram of the system is shown below.

<img src="/images/sentiment/architecture-diagram.jpg">

For the user interfaces design, the application would display the file which is under checked and output the results. Therefore, the interface is designed as below.

<img src="/images/sentiment/interface.jpg">

In this solution, Bayes’ theorem is used to calculate the possibility of a word from negative tweet and positive tweet.




### <a name="development">3. Development </a>
<br>
The system is developed in C#.net under Visual Studio 2015 platform. The training dataset is stored in txt files and the testing dataset is stored in excel file. To use excel file as a data source, “System.Data.OleDb” library is used in the system. 
The diagram which shown the system running mechanism is below.

<img src="/images/sentiment/development-diagram.jpg">




### <a name="results">4. Results </a>
<br>

To test the accuracy of the system, there are two datasets had been used. One dataset contains 1000 positive tweets which had been labeled. The other one contains 1000 negative tweets labeled. To test the influence of the training dataset on accuracy, there are three training dataset which contain 2000 tweets, 7000 tweets and 10000 tweets were used. 

The results are shown below when the training dataset includes 1000 negative tweets and 1000 positive tweets.

<img src="/images/sentiment/result-1000-n.jpg">

<img src="/images/sentiment/result-1000-p.jpg">

The results are shown below when the training dataset increase to 3500 negative tweets and 3500 positive tweets.

<img src="/images/sentiment/result-3500-n.jpg">

<img src="/images/sentiment/result-3500-p.jpg">

The results are shown below when the training dataset increase to 5000 negative tweets and 5000 positive tweets.

<img src="/images/sentiment/result-5000-n.jpg">

<img src="/images/sentiment/result-5000-p.jpg">

The correct rate and error rate for different size of dataset and different testing dataset are shown below.

<img src="/images/sentiment/result.jpeg">



### <a name="discussion">5. Discussion </a>
<br>
From these results, it is obvious that for the positive testing dataset, the correct rate is relatively stable which is between 70% and 80%. On the other hand, for the negative testing dataset, the correct rate is increasing sharply from 46.5% to 67.2% when the training dataset was increased. 

In spite of the increase of accuracy, there are still some weakness in the system. Firstly, some words may have more influence on judging the sentences. However, the impact of these words is the same as the words which may have less influence when calculating the possibility which could cause inaccuracy. Secondly, some words and symbols which have nothing to do with the sentiment identify are included when calculating the possibility. Finally, the training dataset still relatively small.


### <a name="conclusion">6. Conclusion </a>
<br>
The system could identify the sentiment of sentence by using the training data which is based on social media. The testing dataset could be changed to customers’ opinions on some product. By identify the number of positive and negative in the opinions, the decision maker could use it as a support to know the customers’ views. In addition, the outcome would be meaningful on public opinion monitoring. The product also could be changed to a brand, a company or anything people like to talk about. Through the result, it is obvious that people’s opinions on a specific topic could be find. It also could be used on calculate the support rate.

However, there are some limitations in this system. For performance, the training dataset is stored in txt file which cause the time of calculation is relatively long. For accuracy, as the system has not distinguished words on their influence when calculating the possibility, the accuracy would be decreased. Moreover, the word and symbol which have nothing to do with the sentiment identify has not been excluded when calculating the possibility which could cause inaccuracy. At last, the training dataset is relatively small.






