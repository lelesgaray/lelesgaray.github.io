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

