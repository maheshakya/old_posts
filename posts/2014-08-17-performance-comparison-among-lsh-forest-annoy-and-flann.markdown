---
layout: post
title: "Performance comparison among LSH Forest, ANNOY and FLANN"
description: ""
category: GSoC
---
Finally, it is time to compare performance of Locality Sensitive Hashing Forest(approximate nearest neighbor search implementation in [scikit-learn](http://scikit-learn.org/stable/)), [Spotify Annoy](https://github.com/spotify/annoy) and [FLANN](http://www.cs.ubc.ca/research/flann/). 

## Criteria

Synthetic datasets of different sizes (varying `n_samples` and `n_features`) are used for this evalutation. For each data set, following measures were calculated.

1. Index building time of each ANN (Approximate Nearest Neighbor) implementation.
2. Accuracy of nearest neighbors queries with their query times.

Python code used for this evaluation can be found in this [Gist](https://gist.github.com/maheshakya/b7bcf915c9d5bab89d8d). Parameters of 
`LSHForest` (`n_estimators=10` and `n_candidates=50`) are kept fixed during this experiment. Accuracies can be raised by tuning these 
parameters.

## Results 

For each dataset, two graphs have been plotted according to the measures expressed in the above section.
![n_samples=1000, n_features=100](https://docs.google.com/drawings/d/1PqPDygNOpIIicm4YEdN5FwLgECzPSa2toAsGsczIUP0/pub?w=960&h=720)
![n_samples=1000, n_features=500](https://docs.google.com/drawings/d/1v7mtz_xC_9njA-d3rzUYYfbJrzKr0kX5-sBfVOj-yCs/pub?w=960&h=720)
![n_samples=6000, n_features=3000](https://docs.google.com/drawings/d/1gl65_N8mKJPGFardWwUt-8WXtqYbIeJM2jbAuk5qKJg/pub?w=960&h=720)
![n_samples=10000, n_features=100](https://docs.google.com/drawings/d/1GIIjyHOG03WxNOEHhF4p19uP_9JZ9ctyYRRJp2HvCp4/pub?w=960&h=720)
![n_samples=10000, n_features=500](https://docs.google.com/drawings/d/1DFA6i-s661Liplq_lysUyz0KvIH4CnByA5jsEuCw7ig/pub?w=960&h=720)
![n_samples=10000, n_features=1000](https://docs.google.com/drawings/d/14ta-LAR9KdPRaTDDJgKWYHpPsVhqvxCs9rjwHStbRz0/pub?w=960&h=720)
![n_samples=10000, n_features=6000](https://docs.google.com/drawings/d/1KoQ3wXMb-rm4ZGW8d2mMGCcsvxGPU1lgSJTCA0IbRec/pub?w=960&h=720)
![n_samples=50000, n_features=5000](https://docs.google.com/drawings/d/1RIr-jf818iQIIaOOi5C_sX87ynAR6cBVI1FF25Wbq9o/pub?w=960&h=720)
![n_samples=100000, n_features=1000](https://docs.google.com/drawings/d/1onzy44K6Yk4CcX4msTGLJK61UFNOqkkEzZLH3yZrKtY/pub?w=960&h=720)
It is evident that index building times of LSH Forest and FLANN are almost incomparable to that of Annoy for almost all the datasets. Moreover, for larger datasets, LSH Forest outperforms Annoy at large margins with respect to accuracy and query speed. Observations from these graphs prove that LSH Forest is competitive with FLANN for large datasets.