---
layout: post
title:  "Clustering : Gaussian Mixture Models and K-means clustering"
date:   2018-04-24 
categories: [Clustering, Gaussian Mixture Models, K-means, Python]
comments : true
---

<ul id="toc"></ul>

---


## Recap - Random Forest, Descision Tree, Regresssion tree

+ Discision trees, try to mimimize entropy. We need to make sure that the entropy is minimized in the splitting process.

+ Information gain = entropy(parent) - entropy(child)

+ Higher IG is better

+ RF - collection of descision trees. Then we need to aggregate the result. 
  
+ CART - used to build the descision trees 

+ Narrow ranges in the regression tree is over-fitting

## Gaussian Distribution

Also known as _Normal Distribution_, its represented as a "bell curve". Its a useful distribution in may contexts especially when the distribution is not known in advance. 
+ **Central Limit Theorum** : The more samples you have for a distribution, they more likely approach the normal distribution.

+ Given a PDF, we break it into _k_ Gaussian distribution. The sum of the contituent curves should add up to the original curve. 



### Using Spark MLlib for GMM

```
// Load and parse data

String path = "data/mllib/gmm_data.txt";
JavaRDD<String> data = sc.textFile(path);


```


## K-means 

+ **Initialization Step** assign initial coordinates to k centriods _{m<sup>1</sup>}
+ **Assignment Step**

### Selecting K : Within-set-sum-of-squares (WSSS)


### Spark MLlib K-Means
```
//load and parse data
