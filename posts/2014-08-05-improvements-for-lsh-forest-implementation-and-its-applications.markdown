---
layout: post
title: "Improvements for LSH Forest implementation and its applications"
description: ""
category: GSoC
---

GSoC 2014 is coming to an end. But LSH Forest implementation requires a little more work to be completed.  Following are the list of tasks to be done. They will be completed during the next two weeks.

## 1. Improving LSH Forest implementation

I have got a lot of feedback from [scikit-learn](http://scikit-learn.org/stable/) community about my implementation of LSH Forest. Many of them are about the possible optimizations. Making those optimizations happen will cause a significant improvement in the performance.

## 2. Applying LSH Forest in [DBSCAN](http://en.wikipedia.org/wiki/DBSCAN)

The idea of this is to speed up the clustering method using approximate neighbor search, rather than spending much time on exhaustive exact nearest neighbor search. DBSCAN requires the neighbors of which the distance from a data point is less than a given radius. We use the term `radius neighbors` for this. As LSH Forest is implemented to adhere with the scikit-learn API, we have a `radius_neighbors` function in LSH Forest(`NearestNeighbors` in scikit-learn has `radius_neighbors` function). Therefore, LSH Forest can be directly applied in place of exact nearest neighbor search. 

After this application, it will be benchmarked to analyze the performance. Approximate neighbors are more useful when the size of the database (often the number of features) is very large. So the benchmark will be based on following facts. What are the sample sizes and number of features, where approximate neighbor search reasonably outperforms the exact neighbor search.

## 3. Documentation and wrapping up work

After completing the implementation and benchmarking, it will be documented with the scikit-learn documentation standards.
