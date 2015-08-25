---
layout: post
title: Which scikit-learn classifier is the most popular?
---

[scikit-learn](http://scikit-learn.org/stable/) is the most popular machine
learning framework in Python--it's an ideal choice if you just need to hammer
out a solution to a supervised learning problem. But which classifiers are the
most widely used?

Fortunately, it's trivial to find out using GitHub and StackOverflow's search
features. The table below shows the number of repositories and StackOverflow
posts containing each classifier:


| Model | GitHub Repos | StackOverflow Posts |
| ----- | ------------ | ------------------- |
| `ensemble.AdaBoostClassifier` | 2,992 | 403
| `ensemble.BaggingClassifier` | 808 | 124
| `ensemble.GradientBoostingClassifier` | 3,692 | 456
| `ensemble.RandomForestClassifier` | 12,273 | 783
| `lda.LDA` | 5,261 | 1,210
| `linear_model.Lasso` | 5,515 | 1,010
| `linear_model.LinearRegression` | 7,536 | 1,110
| `linear_model.Perceptron` | 2,231 | 602
| `linear_model.RidgeClassifier` | 1,110 | 72
| `linear_model.SGDClassifier` | 4,989 | 785
| `naive_bayes.GaussianNB` | 1,868 | 256
| `naive_bayes.MultinomialNB` | 2,628 | 592
| `neighbors.KNeighborsClassifier` | 3,105 | 1,930
| `qda.QDA` | 2,634 | 128
| `svm.LinearSVC` | 7,970 | 550
| `svm.SVC` | 22,998 | 2,690


As it turns out, people _really_ love Support Vector Machines and Random
Forests; SVMs (including `SVC` and `LinearSVC`) are twice as popular as any
other algorithm.

It's also interesting that `KNeighborsClassifier` isn't all that popular on
GitHub, but is very popular on StackOverflow--is this because it's commonly
used in introductory lessons?
