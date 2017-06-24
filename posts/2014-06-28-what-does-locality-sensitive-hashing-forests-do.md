---
layout: post
title: "An illustration of the functionality of the LSH Forest"
description: ""
category: GSoC
---
Before digging into more technical detail on the implementation of LSH forest, I thought it would be useful to provide an insightful illustration on how the best candidates are selected from the fitted data points.

For this task, I decided to use a randomly generated dataset of the sample size of 10000 in the two dimensional space. The reason of using two dimensions is that it is convenient to depict the vectors in 2D space and the a normal human being can easily grasp the idea of convergence in a 2D space. 

Following configuration of the LSH forest has been selected for this illustration. 

* Number of trees = 1
* Hash length= 32
* c = 1
* Lower bound of hash length at termination = 4
* Expecting number of neighbors = 10

You can get an idea of these parameters from my [previous article on the LSH forest]({% post_url 2014-06-01-lsh-forest-with-sorted-arrays-and-binary-search %})

The following illustrations show the convergence of data points towards the query point as the considered hash length is increased. The important fact I want to emphasize here is that candidates chosen by matching the most significant hash bits converge into the actual data point we are interested in. This happens because of the [amplification property](http://en.wikipedia.org/wiki/Locality-sensitive_hashing#Amplification) of Locality sensitive hashing algorithm. 

(Beware! The query point is in RED)
![hash length = 0](https://docs.google.com/drawings/d/1R8cajY5tZMxy9Q2_JlPCB2UV3FX8Uifw6tY57kY14Uk/pub?w=960&h=720)
![hash length = 1](https://docs.google.com/drawings/d/1xJfWym3OXfWMx9BZzLLX7iweWC4NVj7vqgxoijhEDlI/pub?w=960&h=720)
![hash length = 3](https://docs.google.com/drawings/d/1IOjYl-JsUxTzegKdCYK4C8jvGLV77d0FAhZAXLaj9Jk/pub?w=960&h=720)
![hash length = 4](https://docs.google.com/drawings/d/1lGJrddMp54dOk6pC6_miJUxjlwEGm8wfbF5Xj7x7N6w/pub?w=960&h=720)
![hash length = 5](https://docs.google.com/drawings/d/1CwUGIY4iiBEcyQhuV_zguvNQZx1LBR1Gg734Gx2nw7k/pub?w=960&h=720)
![hash length = 7](https://docs.google.com/drawings/d/1_2NU__OJ_5dio6KWDAb8FZoMHJqM-7XyQaw8-RvvH3A/pub?w=960&h=720)
![hash length = 8](https://docs.google.com/drawings/d/1IhoEw-k66h4EXa1g07_ywvmyrrbGsVyby4mnhRrmydk/pub?w=960&h=720)
![hash length = 24](https://docs.google.com/drawings/d/1oII5NtKCH3WYThoQuOVfx4JNrRbOLANGf5GGXSEmvt0/pub?w=960&h=720)
![hash length = 32](https://docs.google.com/drawings/d/1rxYddtwfE1ZGmTBLBJp1xhTCclYRF-KsLrWjFsesRHY/pub?w=960&h=720)

Only if the required number of candidates are not reached during the early sweeps, the algorithm will search for more candidates in smaller matching hash lengths. The the best neighbors are selected from that set of candidates by calculating the actual distance. 

In my next post, I will enlighten you about the subtle optimizations done on the LSH forest data structure to find the best candidates at a maximum speed.
