---
layout: post
title: "GSoC2014: Approximate nearest neighbor search using LSH"
category: GSoC
---

This project has been initiated as a Google summer of code project. [Python Software foundation](https://www.python.org/) serves as an "umbrella organization" to a variety of Python related open source projects. [Scikit-learn](http://scikit-learn.org/stable/index.html) is a machine learning module which operates under that umbrella. I'm instigating this project under that module. In the following sections, I will describe what this really is with help of the essence of my project proposal. This is just a head start of the journey and you will be able to obtain a clear picture as this proceeds.

## The struggle for nearest neighbor search
Nearest neighbor search is a well known problem which can be defined as follows: given a collection of n data points, create a data structure which, given any query point, reports the data points that is closest to the query. This problem holds a major importance in certain applications: data mining, databases, data analysis, pattern recognition, similarity search, machine learning, image and video processing, information retrieval and statistics. To perform nearest neighbor search, there are several efficient algorithms known for the case where the dimension is low. But those methods suffer from either space or query time that is exponential in dimension.

In order to address the “Curse of Dimensionality” in large data sets, recent researches had been lead based on approximating neighbor search. It has been proven that in many cases, approximate nearest neighbor is as almost good as the exact one[1]. Locality Sensitive Hashing is one of those approximating methods. The key idea of LSH is to hash data points using several hash functions to ensure that for each function the probability of collision is much higher for objects that are close to each other than for those that are far apart.

## What does this have to do with me?
In scikit-learn, currently exact nearest neighbor search is implemented, but when it comes to higher dimensions, it fails to perform efficiently[2]. So I'm taking an initiative to implement LSH based ANN for scikit-learn. In this project, several variants of LSH-ANN methods will be prototyped and evaluated. After identifying the most appropriate method for scikit-learn, it will be implemented in accordance with scikit-learn's API and documentation levels, which includes narrative documentation. Then with the results obtained from prototyping stage, storing and querying structure of ANN will be implemented. After that, ANN part will be integrated into `sklearn.neighbors` module. Next comes the application of this method. Most of clustering algorithms use nearest neighbor search, therefore this method will be adapted to use in those modules in order to improve their operational speed. As these activities proceed, testing, examples and documentation will be covered. Bench marking will be done to assess the implementation.

## Milestones of the project
1. Prototyping/Evaluating existing LSH based ANN methods (vanilla and others) in order to find the most appropriate method to have in scikit-learn. There is no point of having methods in scikit-learn which are impractical to use with real data.
2. Approximating neighbor search uses hashing algorithms of LSH family. These algorithms will be implemented.
3. Implementation of an efficient storing structure to retain trained/hashed data.
4. Integrating the ANN search into current implementation of neighbor search, so that this can be used with the existing API.
5. Improving speed of existing clustering models with the implemented ANN search.
6. Completing tests, examples, documentation and bench marking.

Well that's it for the moment. I will guide you through when the things are in motion. 

## Abbreviations
* LSH : Locality sensitive hashing
* ANN : Approximate nearest neighbor
* API : Application programming interface

## References
1. A. Andoni and P. Indyk,"Near-Optimal Hashing Algorithms for Approximate Nearest Neighbor in High Dimensions", Available[online]:[http://people.csail.mit.edu/indyk/p117-andoni.pdf](http://people.csail.mit.edu/indyk/p117-andoni.pdf)
2. R. Rehurek, "Performance Shootout of Nearest Neighbours: Contestants", Available[online]:[http://radimrehurek.com/2013/12/performance-shootout-of-nearest-neighbours-contestants/](http://radimrehurek.com/2013/12/performance-shootout-of-nearest-neighbours-contestants/)
