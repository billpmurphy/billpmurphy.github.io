---
layout: post
title: Are all horoscopes the same? (an intro to text mining)
---


In this post, we'll walk through some basic text mining recipes while playing
around with a dataset of horoscopes. Let's analyze the following hypothesis:

> Is there any detectable difference between horoscopes for each of the 12
different signs, or do they all just contain generic advice?

To take a stab at answering this question, we will use 36,365 horoscopes
scraped from [Tarot.com](http://www.tarot.com/daily-horoscope). If you want to
play with the data yourself, you can run
[horoscraper.py](http://github.com/billpmurphy/horoscopes).

Once you've got the dataset, you can load it up and start hacking.

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

data = pd.read_csv("horocopes.csv")
```

To get things started, let's take a look at the data:


```python
>>> data.keys()
Index([u'date', u'full_text', u'keywords', u'stars', u'sign', u'sms_summary', u'subject_line'], dtype='object')

>>> data.head()
date                                          full_text  \
1  2008-01-01 00:00:00  Being caught in the middle of someone else's d...
2  2008-01-01 00:00:00  You may be feeling simultaneously nostalgic an...
3  2008-01-01 00:00:00  Although today is a holiday, it may be a chore...
4  2008-01-01 00:00:00  You may think that your opportunity has passed...
5  2008-01-01 00:00:00  Even if you're getting together with friends o...

keywords  stars    sign  \
1    sincerity      2   aries
2     routines      3  taurus
3   resistance      2  gemini
4  opportunity      3  cancer
5  socializing      3     leo

sms_summary  \
1  You may not be able to make everyone happy, fo...
2  You may simultaneously feel nostalgic and anxi...
3  It may be a chore to follow through with plans...
4  You may think that an opportunity has passed, ...
5  Socializing may seem like work. Don't let rese...

subject_line
1          Take care of your own needs first
2      Your routines will return soon enough
3      Deal with resistance when it comes up
4  You'll have another shot at what you want
5         Socializing may seem like work now
```

Each horoscope has a date, designated sign, short description, and associated
keyword. For the moment, we'll restrict our analysis to just the `full_text` of
each horoscope. We'll also remove "general" horoscopes (horoscopes that are not
sign-specific) and also get rid of null values.

```python
data = pd.read_csv("alldata.csv")
data = data[data["sign"] != "general"]
data = data.dropna()

signs = set(data["sign"].values)
```


Let's start simple, and just look at the length of each horoscope. Do some
signs have longer horoscopes on average?

```python
word_count = data.full_text.map(lambda x: len(str(x).split(" ")))
word_count.hist(bins=30, by=data["sign"])
```

![Figure
1](https://rawgit.com/billpmurphy/billpmurphy.github.io/master/_assets/figure_1.png)

As it turns out, the word count is pretty normally distributed and doesn't
differ between signs. So what about textual similarity between horoscopes of
different signs?

A quick and easy way to measure this is using
[Ratcliff-Obership](http://xlinux.nist.gov/dads/HTML/ratcliffObershelp.html)
distance, which is available in Python's `difflib`. R.O. distance is just
[Largest Common
Substring](https://en.wikipedia.org/wiki/Longest_common_substring_problem)
normalized for the length of the strings. We can write helper functions to find
the average R.O. distance between horoscopes and sets of horoscopes:

```python
import random
import itertools
import difflib


def RO_diff(text1, text2):
    """Ratcliff-Obershelp distance between two pieces of text"""
    return difflib.SequenceMatcher(None, str(text1), str(text2)).ratio()


def avg_RO_diff(corpus, sample=None):
    """Average RO distance between all pieces of text in a corpus"""
    combinations = itertools.combinations(corpus, r=2)
    if sample is not None:
        combinations = random.sample(list(combinations), sample)
    return np.mean([RO_diff(*c) for c in combinations])


def avg_corpus_RO_diff(corpus1, corpus2, sample=None):
    """Average RO distance between two corpuses"""
    pairs = itertools.product(corpus1, corpus2)
    if sample is not None:
        pairs = random.sample(list(pairs), sample)
    return np.mean([RO_diff(*p) for p in pairs])
```

First, let's look at textual similarity of horoscopes _within_ each sign, e.g.,
how similar are all horoscopes for Virgo? It would be too computational
expensive to look at every pairwise comparison for every sign, so we'll use a
large sample.

```python
for s in signs:
    print s, avg_RO_diff(data[data["sign"] == s].full_text.values, sample=10000)
```

This gives us the following:
```
libra        0.030677
picies       0.029838
cancer       0.030831
scorpio      0.029826
aquarius     0.029852
taurus       0.031108
leo          0.030562
virgo        0.030640
capricorn    0.030369
gemini       0.030707
saggitarius  0.030060
aries        0.031078
```

The mean of these inter-group comparisons is 0.030462. Now we can compare this
to textual similarity _between_ signs, e.g., the similarity between Libra and
Leo.

```python
for c in itertools.combinations(signs, r=2):
    print c, avg_corpus_RO_diff(data[data["sign"] == c[0]].full_text.values,
                                data[data["sign"] == c[1]].full_text.values,
                                sample=10000)
```

This gives us:

```
('libra', 'picies') 0.0290560773124
('libra', 'cancer') 0.0298186814257
('libra', 'scorpio') 0.0292116902737
('libra', 'aquarius') 0.0293821099756
('libra', 'taurus') 0.0294227812173
('libra', 'leo') 0.0294783714283
...
('capricorn', 'aries') 0.0295701794942
('gemini', 'saggitarius') 0.0291550256845
('gemini', 'aries') 0.0303332516407
```

The mean of these intra-group comparisons is 0.029525. This is only 1% lower
than the inter-group comparisons, so we can say that it is very _very_ weak
evidence in favor of the hypothesis that different signs receive noticably
different horoscopes.


**Step 4: Classification task**

Another way of seeing if horoscopes are similar is to build a classifier that
attempts to distinguish between types of horoscope, and then looking at the
classifier's performance. If it performs well, this suggests that there is a
significant difference between horoscope types.

First, we need to split our dataset into classes and convert our horoscopes
into a feature vector that we can train a classifier on. To keep the classes
equally sized, we'll try to classify [positve-polarity signs vs.
negative-polarity
signs](https://en.wikipedia.org/wiki/Astrological_sign#Polarity_and_the_four_elements).

```python
# Divide the dataset into classes and encode them
pos_polarity = ["aries", "leo", "saggitarius", "gemini", "libra", "aquarius"]
y = data["sign"].apply(lambda x: 1 if x in pos_polarity else 0).values
```

Now we can extract our features. To keep things simple, we'll use scikit-learn
`TfidfVectorizer`, which turns an array of text documents into a TF-IDF
weighted document-term matrix.

```python
# Convert the text to bag of words representation
vec = TfidfVectorizer(min_df=0.005)
X = vec.fit_transform(data["full_text"].values).toarray()
```

Now we're ready to do some machine learning!  The standard approach is to use
k-fold cross validation, but we'll use an even more conservative method: We'll
split our dataset into a training set and a test set, train a model on the
training set with the parameters optimized via grid search.

```python
# Split the data into train and test
X_train, X_test, y_train, y_test = \
        train_test_split(X, y, test_size=0.3, random_state=0)

# Perform 5-fold CV on the training set to optimize the model params
cross_validation = StratifiedKFold(y_train, n_folds=5)

rf = RandomForestClassifier()
param_grid = {'criterion': ['gini'],
              'n_estimators': [10, 15, 20],
              'max_depth': [1, 2, 3, 4, 5],
              'max_features': [1, 3, 5, 'log2']}

grid_search = GridSearchCV(rf, param_grid = param_grid, cv=cross_validation,
                           scoring="f1")
grid_search.fit(X_train, y_train)
```

Now we can take the best model and apply it to the test set.

```python
# Use the best model and see how it does on the test set
best_rf = grid_search.best_estimator_
best_rf.fit(X_train, y_train)
y_test_pred = best_rf.predict(X_test)

# Print the result
print classification_report(y_test, y_test_pred)
print "\n"
print pd.crosstab(y_test, y_test_pred,
                  rownames=["True"], colnames=["Predicted"],
                  margins=True)
print "\nBest classifier:"
print best_rf
```

The results:

```python
precision    recall  f1-score   support

0       0.52      0.73      0.61      5014
1       0.55      0.33      0.42      5051

avg / total       0.54      0.53      0.51     10065



Predicted     0     1    All
True
0          3636  1378   5014
1          3361  1690   5051
All        6997  3068  10065

Best classifier:
RandomForestClassifier(bootstrap=True, compute_importances=None,
                        criterion=gini, max_depth=5, max_features=5, min_density=None,
                        min_samples_leaf=1, min_samples_split=2, n_estimators=10,
                        n_jobs=1, oob_score=False, random_state=None, verbose=0)

Accuracy Score:
0.52916045702930947
```

Uh-oh, the results aren't looking good for astrology. Our classifier is 53%
accurate, which is just barely better than random guessing.

Of course, this is a very basic first stab at this classification problem--in
future posts, we'll delve into NLP and look at some ways we could potentially
classify this dataset with much higher accuracy.
