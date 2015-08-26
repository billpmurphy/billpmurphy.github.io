---
layout: post
title: Which scikit-learn tools are the most popular?
---

The [scikit-learn](http://scikit-learn.org/stable/) library is the most popular
machine learning framework in Python--it's an ideal choice if you just need to
hammer out a solution to a supervised learning problem. But which classifiers
are the most widely used?

Fortunately, it's trivial to find out using GitHub and StackOverflow's search
features. The table below shows the number of repositories and StackOverflow
posts containing each classifier:


| Model | GitHub Repos | StackOverflow Posts |
| ----- | ------------ | ------------------- |
| `ensemble.AdaBoostClassifier` | 2,171 | 403 |
| `ensemble.BaggingClassifier` | 527 | 124 |
| `ensemble.GradientBoostingClassifier` | 2,694 | 456 |
| `ensemble.RandomForestClassifier` | 8,584 | 783 |
| `lda.LDA` | 2,557 | 1,210 |
| `linear_model.Lasso` | 5,517 | 1,010 |
| `linear_model.LinearRegression` | 4,513 | 1,110 |
| `linear_model.Perceptron` | 1,542 | 602 |
| `linear_model.RidgeClassifier` | 804 | 72 |
| `linear_model.SGDClassifier` | 3,353 | 785 |
| `naive_bayes.GaussianNB` | 1,176 | 256 |
| `naive_bayes.MultinomialNB` | 1,769 | 592 |
| `neighbors.KNeighborsClassifier` | 1,838 | 1,930 |
| `neural_network.BernoulliRBM` | 492 | 83 |
| `qda.QDA` | 1,039 | 128 |
| `svm.LinearSVC` | 5,930 | 550 |
| `svm.SVC` | 16,531 | 2,690 |


As it turns out, people _really_ love Support Vector Machines and Random
Forests; SVMs (including `SVC` and `LinearSVC`) are twice as popular as any
other algorithm. It's also interesting that `KNeighborsClassifier` isn't all that popular on
GitHub, but is very popular on StackOverflow--this may be because it's commonly
used in introductory lessons.

In parameter selection, grid search stands out as the clear winner:


| Model | GitHub Repos | StackOverflow Posts |
| ----- | ------------ | ------------------- |
| `grid_search.GridSearchCV` | 5,920 | 1,620 |
| `grid_search.RandomizedSearchCV` | 941 | 162 |


Finally, in [performance metrics](http://scikit-learn.org/stable/modules/classes.html#module-sklearn.metrics), we see one interesting surprise:


| Metric | GitHub Repos |
| ------ | ------------ |
| `accuracy_score` | 4,554 |
| `auc` | 4,675 |
| `average_precision_score` | 818 |
| `brier_score_loss` | 134 |
| `confusion_matrix` | 4,603 |
| `f1_score` | 3,098 |
| `fbeta_score` | 629 |
| `hamming_loss` | 377 |
| `hinge_loss` | 632 |
| `log_loss` | 1,125 |
| `matthews_corrcoef` | 478 |
| `precision_recall_curve` | 1,044 |
| `precision_recall_fscore_support` | 801 |
| `precision_score` | 2,133 |
| `recall_score` | 1,944 |
| `roc_auc_score` | 3,233 |
| `roc_curve` | 2,510 |
| `zero_one_loss` | 708 |


ROC area appears in more codebases than `accuracy_score`, which is strange
given that accuracy is a much simpler metric to understand for someone just
starting out in machine learning. However, this result is also pretty
inspiring--ROC area is a _much_ better metric than accuracy, especially when
dealing with unbalanced datasets.
