---
layout: post
title: "Performance evaluation of Approximate Nearest Neighbor search implementations - Part 1"
category: GSoC
---
This typifies the official instigation of my GSoC project. In my [previous post]({% post_url 2014-05-18-preparing-a-bench-marking-data-set-using-singula-value-decomposition-on-movielens-data %}), I have discussed how to create the bench marking data set which will be used from here on. 
I will discuss how the evaluation framework is designed to evaluate the performance of the existing approximate nearest neighbor search implementations. For this evaluation, I have chosen the following implementations:

* Spotify [ANNOY](https://github.com/spotify/annoy)
* [FLANN](http://www.cs.ubc.ca/research/flann/) 
* [KGraph](http://www.kgraph.org/)
* [nearpy](http://nearpy.io/)
* [lshash](https://pypi.python.org/pypi/lshash/0.0.4dev)


## Evaluation framework

There are three main considerations in this evaluation framework. 

1. Memory consumption
2. Precision
3. Query speed

For each of these aspects, there are separated tests which I will explain in the upcoming sections. In addition to these, from [scikit-learn](http://scikit-learn.org/stable/) community, another requirement emerged to consider the index building time in order to assist with incremental learning. This is an optimization which has to be done in the ANN version that will be implemented in [scikit-learn](http://scikit-learn.org/stable/). 

Note: The evaluation framework will be run on a system with following configurations:

* Memory : 16 GB (1600 MHz)
* CPU    : Intel® Core™ i7-4700MQ CPU @ 2.40GHz × 8 

In this post, I will discuss the evaluation done for memory consumption.

### Memory consumption of existing ANN methods

Memory consumption corresponds to the index building step in a nearest neighbor search data structure since it is the process which stores the data in the data structure accordingly. In this framework, there are two main aspects taken into account to express the memory consumption of an index building process. 

1. **Peak memory consumption** : While the index building process takes place, there is a maximum amount of memory used. No amount of memory beyond this peak memory will be consumed during this process.
2. **Overall increment** : This is the actual memory used by the data structure after the index building process. This may be less than or equal to the peak memory consumption.

These two aspects of the above mentioned ANN implementations were measured. The results are as follows.

![Memory_usage_table](https://docs.google.com/drawings/d/1gCLDnk_UJ-kk5bkY-g-jp1hnqO5GcDqusQch2OHw0VI/pub?w=668&h=468)

Following graph illustrates the peak memory consumptions in log scale.
![Peak_memory_usage](https://docs.google.com/drawings/d/1j7BjozhffmhVMbJHtymDYtlKFvxo1LQRQnHY2PHDIQU/pub?w=804&h=614)

Following graph illustrates the overall memory increments in log scale.
![Overall_memory_increment](https://docs.google.com/drawings/d/1EhBe1c45BIn5tEs6hzqF8MNMzrYs_NZEivii8Wqs0A0/pub?w=808&h=614)

In the upcoming posts, I will discuss the other two aspects in the evaluation framework in detail and the performance of LSH-Forest implementation as well. 

Cheers!
