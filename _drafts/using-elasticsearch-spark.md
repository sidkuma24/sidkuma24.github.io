---
layout: post
title:  "Movie Recommendation System using Spark MLlib"
date:   2018-04-28
categories: [Deep Learning, Object-detection, Python]
comments : false
---

<ul id="toc"></ul>

---

## Introduction

There are too many choice of movies, music and vidoes avalilable on the internet and its very hard to choose. That is why we need recommendation systems, companies like Amazon, Netflix, Google rely heavily on recommendation systems to provide relevant cand interesting content to their users. Here I will be talking about a movie recommendation sytem I recently worked on. The core system relies on the Alternating least squares (ALS) algorithm, used to learn the latent factors for a user-association matrix, that can be used to predict missing values. I used the Apache Spark MLlib implementation of ALS, build the recommendation engine. The ALS model is trained on the Movie-lens dataset. We will use elasticsearch to search the data for movies. This search will allow a new user to rate some movie and get new recommendations.


## Dataset : MovieLens




