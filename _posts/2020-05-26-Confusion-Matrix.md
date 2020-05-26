---
title: Confusion Matrix and Derived Metrics
subtitle: Precisoin, Recall, F1 Score, TPR, FPR, ROC, PRC
layout: post
mathjax: true
author: Meyer
catalog: true
header-img: "img/Wallpaper-Polygons-Artwork-4K-Abstract.jpg"
tags: 
    - Machine Learning

---


### Confusion Matrix
In the field of machine learning and specifically the problem of statistical classification, a **confusion matrix**, also known as an error matrix, is a specific table layout that allows visualization of the performance of an algorithm, typically a supervised learning one. Each row of the matrix represents the instances in a predicted class while each column represents the instances in an actual class (or vice versa). The name stems from the fact that it makes it easy to see if the system is confusing two classes (i.e. commonly mislabeling one as another).

### Terminology and Derivations
* **condition positive (P)**: the number of real positive cases in the data
* **condition negative (N)**: the number of real negative cases in the data
* **true positive (TP)**: eqv. with hit
* **true negative (TN)**: eqv. with correct rejection
* **false positive (FP)**: eqv. with false alarm, Type I error
* **false negative (FN)**: eqv. with miss, Type II error
* **sensitivity, recall, hit rate, or true positive rate (TPR)**: 

$$ \text{Recall} = \text{TPR} = \frac{\text{TP}}{\text{TP}+\text{FN}} $$

* **specificity, selectivity or true negative rate (TNR)**:

$$ \text{Specificity} = \frac{\text{TN}}{\text{TN}+\text{FP}} $$

* **precision or positive predictive value (PPV)**: 

$$ \text{Precision} = \text{PPV} = \frac{\text{TP}}{\text{TP}+\text{FP}} $$

* **fall-out or false positive rate (FPR)**: 

$$ \text{FPR} = \frac{\text{FP}}{\text{FP}+\text{TN}} $$

* **false discovery rate (FDR)**: 

$$ \text{FDR} = \frac{\text{FP}}{\text{FP}+\text{TP}} = 1 - \text{PPV}  $$

* **accuracy (ACC)**:
  
$$ \text{ACC} = \frac{\text{TP}+\text{TN}}{\text{TP}+\text{TN}+\text{FP}+\text{FN}}  $$

* **F1 score**: the harmonic mean of precision and recall

$$ \text{F}_1 = \frac{2\cdot \text{Precision}\cdot \text{Recall}}{\text{Precision} + \text{Recall}} $$

The above covers some commonly used indicators. The complete version can be found [here](https://en.wikipedia.org/wiki/Confusion_matrix).

![](/img/in-post/confusion_matrix/confusion_matrix.jpg)

### Link with Hypothesis Testing

![](/img/in-post/confusion_matrix/error_types.jpg)

See [type I and ttype II errors](https://en.wikipedia.org/wiki/Type_I_and_type_II_errors) for details.

### ROC & PRC
##### ROC
A receiver operating characteristic curve, or **ROC curve**, is a graphical plot that illustrates the diagnostic ability of a binary classifier system as its discrimination threshold is varied. The ROC curve is created by plotting the true positive rate (TPR) against the false positive rate (FPR) at *various* threshold settings. Here is a [video](https://youtu.be/4jRBRDbJemM) illustrating this.

![](/img/in-post/confusion_matrix/roc1.jpg)

By moving the result cutoff value (vertical bar), the rate of false positives (FP) can be decreased, at the cost of raising the number of false negatives (FN), or vice-versa.

![](/img/in-post/confusion_matrix/roc2.jpg)

The further away from the diagonal means better performance. **Area Under the ROC Curve (AuROC)** is a common metric for comparing classification methods. ROC curve is problematic when we have an unbalanced dataset (e.g., for a rare disease, we have much more negative examples).

##### PRC
Similar to ROC curve, PRC curve use precision instead of false positive rate, which will not take into account the true negatives. Area Under the PRC (AuPRC) is useful when datasets are unbalanced.

![](/img/in-post/confusion_matrix/prc.jpg)

### Note on Multiclass Classification
When dealing with multiclass classification, we usually count the true positives, false negatives and false positives for each label. 
Consider the following example:

```python
y_true = [0, 1, 2, 0, 1, 2]
y_pred = [0, 2, 1, 0, 0, 1]
```

Let's take label 2 for example. When $y_{true}=y_{pred}=2$, it is counted as true positive. 
When $y_{true}\neq2$ and $y_{pred}=2$, it is counted as false positive. 
When $y_{true}=2$ and $y_{pred}\neq2$, it is counted as false negative.
We can summarize as follows: 

| Label | True Positive | False Positive | False Negative |   Precision   | Recall |
|:-----:|:-------------:|:--------------:|:--------------:|:-------------:|:------:|
|   0   |       2       |        1       |        0       | $\frac{2}{3}$ |    1   |
|   1   |       0       |        2       |        2       |       0       |    0   |
|   2   |       0       |        1       |        2       |       0       |    0   |
|  Sum  |       2       |        4       |        4       |               |        |


To compute the overall precision and recall, or F1 score, there are different ways to average:

* `macro`: Calculate metrics for each label, and find their unweighted mean. This does not take label imbalance into account.
* `micro`: Calculate metrics globally by counting the total true positives, false negatives and false positives.
* `weighted`: Calculate metrics for each label, and find their average weighted by support (the number of true instances for each label).

When using `macro`, the average precision and recall will be $\frac{2}{9}$ and $\frac{1}{3}$ respectively. 
When using `micro`, the average precision and recall are both $\frac{2}{2+4} = \frac{1}{3}$.
We then use the average precision and recall to calculate F1 score.

```python
from sklearn.metrics import f1_score
f1_score(y_true, y_pred, average='macro')     # 0.266666
f1_score(y_true, y_pred, average='micro')     # 0.333333
f1_score(y_true, y_pred, average='weighted')  # 0.266666
```

Also see the [documentation](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html) for `f1_score`.