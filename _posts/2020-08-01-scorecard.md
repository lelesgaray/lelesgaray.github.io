---
title: "How to develop a Credit Scorecard in Python"
categories:
  - Blog
tags:
  - Python
  - Logistic Regression
  - Trees
  - Finance
classes: wide
header: 
  teaser: "/assets/images/credit_score.jpg"
gallery:
  - url: /assets/images/scorecar_perf_1.png
    image_path: /assets/images/scorecar_perf_1.png
    alt: "Performance 1"
  - url: /assets/images/scorecard_perf_2.png
    image_path: /assets/images/scorecard_perf_2.png
    alt: "Performance 2"
---

The company I work for is updating the modelling process and migrating all the scripts to Python and R. Given that a broad portion of the credit models deployed involve a binary classification, there is a need to transform variables to compute their Weight of Evidence (WOE). During my research I couldn't find a widely-adopted framework to do the whole model from scratch, so I decided to use different tools to create one. In this post I will review some of the advantages and pitfalls I encountered during the process.

Let's start by importing the libraries and the [HMEQ dataset](https://www.kaggle.com/ajay1735/hmeq-data), that contains baseline and loan performance information for 5,960 recent home equity loans and a binary target variable. 
```python
import numpy as np
import pandas as pd
! pip install sidetable
import sidetable
import matplotlib.pyplot as plt
%matplotlib inline
import seaborn as sns

# load data
df = pd.read_csv('https://raw.githubusercontent.com/Carl-Lejerskar/HMEQ/master/hmeq.csv')
df.head()
```
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>BAD</th>
      <th>LOAN</th>
      <th>MORTDUE</th>
      <th>VALUE</th>
      <th>REASON</th>
      <th>JOB</th>
      <th>YOJ</th>
      <th>DEROG</th>
      <th>DELINQ</th>
      <th>CLAGE</th>
      <th>NINQ</th>
      <th>CLNO</th>
      <th>DEBTINC</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>1100</td>
      <td>25860.0</td>
      <td>39025.0</td>
      <td>HomeImp</td>
      <td>Other</td>
      <td>10.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>94.366667</td>
      <td>1.0</td>
      <td>9.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1300</td>
      <td>70053.0</td>
      <td>68400.0</td>
      <td>HomeImp</td>
      <td>Other</td>
      <td>7.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>121.833333</td>
      <td>0.0</td>
      <td>14.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>1500</td>
      <td>13500.0</td>
      <td>16700.0</td>
      <td>HomeImp</td>
      <td>Other</td>
      <td>4.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>149.466667</td>
      <td>1.0</td>
      <td>10.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>1500</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>1700</td>
      <td>97800.0</td>
      <td>112000.0</td>
      <td>HomeImp</td>
      <td>Office</td>
      <td>3.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>93.333333</td>
      <td>0.0</td>
      <td>14.0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

As we can see from the table there are quite a few missing for several variables. To continue, let's check the class balance of the target variable.
<div class="output_html rendered_html output_subarea output_execute_result">
<style  type="text/css" >
</style><table id="T_8091084a_cde6_11ea_ace1_5cea1d47248e" ><thead>    <tr>        <th class="blank level0" ></th>        <th class="col_heading level0 col0" >BAD</th>        <th class="col_heading level0 col1" >count</th>        <th class="col_heading level0 col2" >percent</th>        <th class="col_heading level0 col3" >cumulative_count</th>        <th class="col_heading level0 col4" >cumulative_percent</th>    </tr></thead><tbody>
                <tr>
                        <th id="T_8091084a_cde6_11ea_ace1_5cea1d47248elevel0_row0" class="row_heading level0 row0" >0</th>
                        <td id="T_8091084a_cde6_11ea_ace1_5cea1d47248erow0_col0" class="data row0 col0" >0</td>
                        <td id="T_8091084a_cde6_11ea_ace1_5cea1d47248erow0_col1" class="data row0 col1" >4,771</td>
                        <td id="T_8091084a_cde6_11ea_ace1_5cea1d47248erow0_col2" class="data row0 col2" >80.05%</td>
                        <td id="T_8091084a_cde6_11ea_ace1_5cea1d47248erow0_col3" class="data row0 col3" >4,771</td>
                        <td id="T_8091084a_cde6_11ea_ace1_5cea1d47248erow0_col4" class="data row0 col4" >80.05%</td>
            </tr>
            <tr>
                        <th id="T_8091084a_cde6_11ea_ace1_5cea1d47248elevel0_row1" class="row_heading level0 row1" >1</th>
                        <td id="T_8091084a_cde6_11ea_ace1_5cea1d47248erow1_col0" class="data row1 col0" >1</td>
                        <td id="T_8091084a_cde6_11ea_ace1_5cea1d47248erow1_col1" class="data row1 col1" >1,189</td>
                        <td id="T_8091084a_cde6_11ea_ace1_5cea1d47248erow1_col2" class="data row1 col2" >19.95%</td>
                        <td id="T_8091084a_cde6_11ea_ace1_5cea1d47248erow1_col3" class="data row1 col3" >5,960</td>
                        <td id="T_8091084a_cde6_11ea_ace1_5cea1d47248erow1_col4" class="data row1 col4" >100.00%</td>
            </tr>
    </tbody></table>
</div>
<br>
We can check correlation with the target variable using a simple Heatmap.
```python
f, ax = plt.subplots(figsize=(8, 8))
ax = sns.heatmap(df.corr(),
            cmap = 'coolwarm', 
            annot = True)
```

<style>
.centerImage
{
 text-align:center;
 display:block;
}
</style>
<div class="centerImage">
<img src="/assets/images/scorecard_heatmap.png" alt="this is a placeholder image" style="width: 50%; height: 50%"/>
</div>

## WOE transformation

Next we will transform our variables into WOEs. To do that, we will use 2 python libraries: [scorecardpy](https://pypi.org/project/scorecardpy/) and [Monotonic WOE Binning](https://github.com/jstephenj14/Monotonic-WOE-Binning-Algorithm). The reason to use both packages is that, while the former will perform the whole sequence of transformation-estimation-performance analysis, the latter will assure the [monotonicity property of WOEs](https://en.wikipedia.org/wiki/Monotonic_function).

Let's go ahead and import the libraries
```python
#!pip install scorecardpy
#!pip install monotonic-binning
import scorecardpy as sc
from monotonic_binning.monotonic_woe_binning import Binning
```
#### Data Split and WOE computation of numeric variables
We will define a function to compute numeric variables with the `monotonic_binning` package.
```python
# Perform a 70 / 30 split of data
train, test = sc.split_df(df, 'BAD', ratio = 0.7, seed = 999).values()

# Function to compute WOEs
var = train.drop(['BAD', 'REASON', 'JOB'], axis = 1).columns
y_var = train['BAD']

def woe_num(x, y):
  bin_object = Binning(y, n_threshold = 50, y_threshold = 10, p_threshold = 0.35, sign=False)
  global breaks 
  breaks = {}
  for i in x:
    bin_object.fit(train[[y, i]])
    breaks[i] = (bin_object.bins[1:-1].tolist())
  return breaks
  
woe_num(var, 'BAD')
```
This will return a dictionary that we will pass as argument to the `scorecard` package. But before that, we need to compute the WOEs for cathegorical variables.
```python
# Check categorical variables names
bins = sc.woebin(train, y = 'BAD', x = ['JOB', 'REASON'], save_breaks_list = 'cat_breaks')
# import dictionary
from cat_breaks_20200724_164925 import breaks_list
breaks_list

# merge
breaks.update(breaks_list)
print(breaks)
```
#### WOE transformation
Finally it's time to use the dictionary of WOE-rules and apply them to the original variables in train/test.
```python
bins_adj = sc.woebin(df, 'BAD', breaks_list= breaks, positive = 'bad|0') # change positive to adjust WOE to ln(GOOD / BAD)
# converting train and test into woe values
train_woe = sc.woebin_ply(train, bins_adj)
test_woe = sc.woebin_ply(test, bins_adj)

# Merge by index
train_final = train.merge(train_woe, how = 'left', left_index=True, right_index=True)
test_final = test.merge(test_woe, how = 'left', left_index=True, right_index=True)
```
And now we are all set to estimate a model! Before that, some useful tips:<br>
- Notice that we are computing WOE = ln(good/bad), by changing the `positive` parameter of the `woebin` function. 
- Take into account that we need to fill the missing values if we decide to keep the original variables (as well as the transformed ones).
<br>
- You can manually adjust the cut-offs by calling the `woebin_adj` method, and you can visually inspect the new variables with `woebin_plot`. An example of this plot is presented below.
{: .notice}

<style>
.centerImage
{
 text-align:center;
 display:block;
}
</style>
<div class="centerImage">
<img src="/assets/images/woe_plot.png" alt="this is a placeholder image" style="width: 50%; height: 50%"/>
</div>

## Logistic Regression

#### 1. Data split and model fit
Let's fit a Logistic Regression for the database we constructed.
```python
# Data split
y_train = train_final.loc[:,'vd']
X_train = train_final.loc[:,train_final.columns != 'vd']
y_test = test_final.loc[:,'vd']
X_test = test_final.loc[:,train_final.columns != 'vd']

# LR fit
# logistic regression ------
from sklearn.linear_model import LogisticRegression
lr = LogisticRegression(penalty = 'l1', C= 0.9)
lr.fit(X_train, y_train)
print(lr.coef_)
```

#### 2. Performance
The `scorecard` package has some in-built methods to analyze performance
```python
# predicted proability
train_pred = lr.predict_proba(X_train)[:,1]
test_pred = lr.predict_proba(X_test)[:,1]

# performance ks & roc ------
train_perf = sc.perf_eva(y_train, train_pred, title = "train")
test_perf = sc.perf_eva(y_test, test_pred, title = "test")
```

{% include gallery id="gallery" caption="Performance Scores Train / Test" %}

```python
print(classification_report(y_test,predictions))
```
<div class="output_area">

    <div class="prompt"></div>

Finally we can check Precision, Recall and the Confusion Matrix

<div class="output_subarea output_stream output_stdout output_text">
<pre>              precision    recall  f1-score   support

           0       0.92      0.96      0.94      1431
           1       0.79      0.66      0.72       357

    accuracy                           0.90      1788
   macro avg       0.85      0.81      0.83      1788
weighted avg       0.89      0.90      0.89      1788

</pre>
</div>
</div>

```python
conf_log2 = confusion_matrix(y_test,predictions)
sns.heatmap(data=conf_log2, annot=True, linewidth=0.7, linecolor='k', fmt='.0f', cmap='magma')
plt.xlabel('Predicted Values')
plt.ylabel('True Values')
plt.title('Confusion Matrix - Logistic Regression');
```

<style>
.centerImage
{
 text-align:center;
 display:block;
}
</style>
<div class="centerImage">
<img src="/assets/images/scoreacard_conf_matrix.png" alt="this is a placeholder image" style="width: 50%; height: 50%"/>
</div>

## Wrap up
This entry presented an easy way to calculate WOEs and fit a simple model for Finance and Credit analysis. I'm aware that there are many things missing from the analysis (variables selection by IV, hyperparameters tuning, among others) but I wanted to focus only on the key steps to increase performance by transforming variables to WOEs. If you have a better way to deal with these issues, or you've been implementing a better solution, please contact me so that I can learn from your progress.

## Extras
- [Jupyter notebook](https://github.com/lelesgaray/scorecard) of the project.
- An [open repo](https://github.com/Sundar0989/WOE-and-IV) that deals with WOE transformation through a series of functions.
