# Year_Prediction :telescope:

## Introduction
The goal of the project is to provide a model able to predict for a given scientific article on NLP the year of publication.
We are in charge to split the data in train/test/validation sets with 80/10/10 respectively for
example. </br>
We can evaluate the model with a MSE error, since making a mistake of ten years or
one year is not as important.
The goal is not to reach the highest score. It’s easy, we can train on all the datas and generate a
model that is just a memory and achieve a perfect model, which is not very interesting.</br>
Two mains expectations of the project :
* Propose a model to achieve this goal and explain all modifications step by step, in
order to improve the result. 
* We need to analyse the results : Which years are more difficults ? Why ? Which articles
are more difficult, or which key-words are considered more difficults ?</br>

Answering to this questions is an other way to synthetise the corpus and understand the evolution of
Natural Language Processing.
The number of articles per year are not equals. We will need to force balanced year distribution
between train/test/validation sets. For example, if year 2001 is present only in train articles and year
2010 only in evaluation articles, it’s a loss of representativity in evaluation and train respectively.

Data can be found here : https://www.ortolang.fr/market/corpora/corpus-taln

## PREPROCESSING
Before going any further to the problem, we need to prepare our dataset based on our
previous work. To do so, we’ve gathered differents functions that will help us automatize
data retrieval :

#### CreateDataFrame
#### Lemmatizer
Lemmatizer based on french core news library.
#### Preprocessing
GetRidOfApostrophe : Regex replacement
GetRidOfSpecialCharacter : Regex replacement
GetRidOfAccent : Regex replacement
#### UseStopWords
French stopwords
GetRidOfThreeWords : Get rid of words with length <=3
GetRifOfEtreAvoir : Get rid of to be and have verbs that occurs a lot in the corpus</br>
## Feature Analysis
After the preprocessing part, we could look at our dataframe, containing Title, Abstract,
Corpus, Keyword and the Date. We didn’t make any assumptions at the beginning to choose
which of those features would fit the best.
We started to analyse our target variable date and simply look at the distribution of it. We
see that our articles have a normal distribution. Problem is, there are only 3 articles from 1997,
which is very few when we have to deal with a regression
![GitHub Logo](/images/distributionpng.png)

Based on our previous conclusions in the previous report, we thought of different features to add, with the help of vectorizer.</br>

To categorize articles with respect to years, we can add Tfidf vectors from our different corpus to help our future model analyse and recognize some correlations.
We first compute Tf-idf vectors for our different features to see if there is some kind of clustering effect.

#### Corpus
![GitHub Logo](/images/corpus.png)
#### Title 
![GitHub Logo](/images/title.png)
#### Abstract
![GitHub Logo](/images/abstract.png)
#### Keyword
![GitHub Logo](/images/keyword.png)

The 4 graphs give us different clues about how each feature can be displayed on a 2D plot. We can clearly identify two clusters on keywords (which aren’t the ones with “None”) and three others when plotting corpus.

## Model Implementation 

Now that we have our data cleaned, processed and balanced, we can start to test our first regression model.
Since we have to predict years according to our data, we will first see which of the 3 text features will best fit to our regression. Keyword features cannot be exploited alone because it has a lot of None values for several years, which would block us from predicting a whole spectrum of year.
Firstly, we will compare the 3 features in a TFIDF vectorizer that will be implemented in a ridge regression. It is a simple model that can quickly learn, without much data, based on plenty features, which would be our tf-idf matrix.
For each of these features, we vectorize it with the top 50 words and insert it into our ridge model. We make a cross-validation for each of them to output the best Alpha parameters, which seems to be the same.
![GitHub Logo](/images/models.png)
Problem is, we obtain a MSE varying between 25.5 and 34.0 according to the three features. Title seems to give better results in the test set, so that would mean the feature has less text.
If we focus on our error, we see that our model doesn’t predict the year quite precisely. It does predict something around the year 2000, but fail to go further than that. 
Let’s dig into the MSE distribution to see which years are difficult to predict for our model.
![GitHub Logo](/images/firstmodel.png)
![GitHub Logo](/images/secondmse.png)
Here, we can see MSE error according to the years, for our prediction model based on Title and Corpus respectively.
Our model doesn’t do really well on dates between 1998-2005 and between 2015 and 2019. A possible explanation would be the amount of data that we have. In fact when we compare this graph to the distribution of articles per year, we clearly see that the model does not do well on articles where he doesn’t have that much data.
</br>
## Model Improvements

### I - Aggregation 

Next, we tried to aggregate our 3 features to see if the model can be improved by stacking the three Tfidf matrices into one.
We obtained better results than Abstract and title models, but not as good as our corpus model when extracting the top 5 features.

### II - Regularization Parameters

Our next work was based on looking at the regularization of our linear regression. We tried to use Lasso and Elasticnet Regression but failed to have any improvements. The same problem was encountered when trying to use SGD to optimize our model.

The conclusion of these parameters tweaking was that we needed to change our approach to feature analysis whenever we implement the model. We will now focus on our corpus model which gave us the best result so far.

### III - Vectorizer Parameters

The base of our model lies in the feature transformation that we do, with scitkit learn Tf-idf Vectorizer. We then tried to tweak different parameters used and add some according to our problem : 
Changing the Max_features allowed in the vocabulary built 
Replacing our term frequency by a 1+ log(tf) scaling
changing ngram range (From unigram to 6-gram)
Built-on french stopword
Adding a character vectorizer with a ngram range and stack it to our first matrix

Out of those parameters, only replacing our TF by 1+log(TF) improved our MSE since we could scale easier our words frequency and avoid slightly less frequent words from being unconsidered in our model.

### IV - Improving our feature selection

We have been working on our model based on his parameters so far, but we haven’t really dug into how each of the words can have an impact on our model. In this part, we will focus on the words of the corpus and see if we can alter some of them.

Based on our previous work, we have tried to output the most common words in the articles, and also frequent words per year. The main idea here is to try to build our own vocabulary dictionary which will be a base for our TF-IDF vectorizer. 
We computed the first K common words, included in the vectorizer function and predicted the year. After the computation, we saw that 4500 words was the optimal number that was required to improve our model.

To sum up our result, we can plot an RMSE graph that can be more visual and more understandable based on our problem to see where we stand at the end of our project. 
![GitHub Logo](/images/model2.png)
The RMSE can be interpreted as a unit error, which means that a RMSE of 2 would be an error of 2 years in the prediction. 


## Perspectives

To conclude on this project, we have made the decision to exploit a simple, but efficient regression model that has been improved with regularization and based on one feature that we chose : the Corpus. This feature contains lots of words, which meant that it would be difficult to identify the ones that can really be significant for each year's prediction. However, with a built-in dictionary and some feature selection, we managed to have a model that trained well and didn’t give as bad results as it seemed.
Further improvements would be in the stacking of other important features, such as cosine similarity computing which would help us select our features more precisely. We could have thought of using deep learning models but the main problem is that we lack data to train it. It was actually this lack of data that stopped us from improving our model any further. 





