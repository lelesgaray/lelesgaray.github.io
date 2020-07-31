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


