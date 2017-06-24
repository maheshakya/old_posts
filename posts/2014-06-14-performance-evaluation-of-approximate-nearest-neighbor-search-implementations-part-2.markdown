---
layout: post
title: "Performance evaluation of Approximate Nearest Neighbor search implementations - Part 2"
category: GSoC
---
This continues from the post [Performance evaluation of Approximate Nearest Neighbor search implementations - Part 1]({% post_url 2014-05-25-performance-evaluation-of-approximate-nearest-neighbor-search-implementations---part-1 %}). The evaluation about the memory consumption is already completed in that post. In this post, the next two aspects of the evaluation framework, precision and query speed will be discussed. 

When measuring the performance of Approximate nearest neighbor search methods, expressing precision and the query speed independently is less useful since the entire idea of approximating is to obtain the desired precision within a better time period. In order to evaluate precision, an ANN implementation should be able to provide a multiple number of neighbors(rather than just the nearest neighbor). After obtaining all data points in the data set from the ANN method, first few entires in the that neighbors list(ten neighbors in my evaluation tests) are taken as the neighbors of ground truth of that particular ANN method. These set of data points are compared against neighbors retrieved when the number of queried neighbors is varied. Precision tests are adapted from the [tests performed for ANNOY](https://github.com/spotify/annoy/blob/master/examples/precision_test.py).

This precision measure eliminates some of our candidate ANN implementations because those are not capable of producing a multiple number of neighbors. Obtaining multiple neighbors is an essential requirement of for precision tests described above as well the general applications of nearest neighbor search. Therefore, for the precision tests, only [ANNOY](https://github.com/spotify/annoy), [FLANN](http://www.cs.ubc.ca/research/flann/), [KGraph](http://www.kgraph.org/) and LSH Forest are taken into consideration. All evaluation tests, LSH forest implementation and the plots can be found in [this Github repository](https://github.com/maheshakya/Performance_evaluations_ANN).

Before jumping into comparisons, I thought it is imperative to get a notion on the characteristics of the LSH forest implementation. Unlike other ANN implementations, LSH forest provides some user controlled parameters to tune the forest and queries in different scenarios.  

For the entire forest: 

* Number of trees
* Maximum hash length

For queries:

* c : A value which determines the number of candidates chosen into the neighbors set. This acts with the number of trees.
* A lower bound for the maximum depth of hashes considered.

In these precision tests, all the those factors but `c` are fixed to constant values as follows:

* Number of trees = 10
* Maximum hash length = 32
* Lower bound = 4

The same data set which has been prepared using [singular value decomposition on movielens data]({% post_url 2014-05-18-preparing-a-bench-marking-data-set-using-singula-value-decomposition-on-movielens-data %}) is used in all of these tests. Following are the resulting graphs of the performance of LSH forest. Time is measured in seconds.
![precision vs c LSHF](https://docs.google.com/drawings/d/14CLx4l4VNxJzINJUurlSsrXGl9iWH9fjVH1OideeVO0/pub?w=960&h=720)
![precision vs time LSHF](https://docs.google.com/drawings/d/1Qr0bHs9Q9pnoszRn-PgGEC5mFMNhwQzqkJ4K4vHYjCw/pub?w=960&h=720)
![precision vs number of candidates LSHF](https://docs.google.com/drawings/d/1EPkeWOfMt7y6_nKk0StNri71xkrwEtoX-8TySqmM8tk/pub?w=960&h=720)
![number of candidates vs c LSHF](https://docs.google.com/drawings/d/1klhtpde7N5YLHuCZHX6ekTEuD54IPVM5Tgltj5Xukqk/pub?w=960&h=720)

The next section of this post illustrates the performance comparisons among ANNOY, FLANN, LSH forest and KGraph. Precision vs. time graphs are used for this comparison. 
![precision vs time LSHF and ANNOY](https://docs.google.com/drawings/d/1Lx8jRYyGbHBD8JzCG_0-HpO14fXyu-pMcto26gsa5zw/pub?w=960&h=720)
![precision vs time FLANN & LSHF](https://docs.google.com/drawings/d/1kfUAr2W6WP_OL4l_vGZ-zaEXirG0jstFkk-r2TAH6rU/pub?w=960&h=720)
Comparing ANNOY, FLANN and LSHF in one place:
![precision vs time LSHF, ANNOY & FLANN](https://docs.google.com/drawings/d/1EQTMURuWB7hjoi9r0sGS2Z-IqL_Z_4kg7K5hC6sSt-g/pub?w=960&h=720)

KGraph has a significantly higher precision rate than other ANN implementations.(Rather than approximating, it gives the actual nearest neighbors according the KGraph documentation )
![precision vs time LSHF & KGraph](https://docs.google.com/drawings/d/1HRED65X5AlRuYIo1YaeU6D6ouVufdD8N3v1FFldNESg/pub?w=960&h=720) 

One of the main considerations of these evaluations is the maintainability of the code. Therefore, any implementation which goes into [scikit-learn](http://scikit-learn.org/stable/) should have reasonably less complex code. Both FLANN and KGraph use complex data structures and algorithms to achieve higher speeds. ANNOY has a reasonably passable precision-query speed combination with a less complex implementation. Our current implementation of LSH forest has been able to beat ANNOY in precision-query speed comparison. 

## Indexing speeds of ANN implementations

In addition to precision and query speed, a measure of indexing speed has a major importance. Therefore as a final step for this evaluation, I will provide you a description on indexing speed for the same data set used above for each ANN implementation.

* KGraph                   : 65.1286380291 seconds
* ANNOY (metric='Angular') : 47.5299789906 seconds
* FLANN                    : 0.314314842224 seconds
* LSHF                     : 3.73900985718 seconds

In my next post, I will discuss about the implementation of LSH forest in detail and how ANN methods will be implemented in scikit-learn.

### Acronyms:

1. ANN : Approximate Nearest Neighbors
2. LSH : Locality Sensitive Hashing
