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
{% include figure image_path="assets/images/scorecard_heatmap.png" alt="this is a placeholder image" caption="" %}

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
Now it's time to use the dictionary of WOE-rules and apply them to the original variables in train/test.
```python
bins_adj = sc.woebin(df, 'BAD', breaks_list= breaks, positive = 'bad|0') # change positive to adjust WOE to ln(GOOD / BAD)
# converting train and test into woe values
train_woe = sc.woebin_ply(train, bins_adj)
test_woe = sc.woebin_ply(test, bins_adj)

# Merge by index
train_final = train.merge(train_woe, how = 'left', left_index=True, right_index=True)
test_final = test.merge(test_woe, how = 'left', left_index=True, right_index=True)
```
