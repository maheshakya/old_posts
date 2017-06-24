---
layout: post
title: "A demonstration of the usage of Locality Sensitive Hashing Forest in approximate nearest neighbor search"
description: ""
category: GSoC
---
This is a demonstration to explain how to use the approximate nearest neighbor search implementation using locality sensitive hashing in [scikit-learn](http://scikit-learn.org/stable/) and to illustrate the behavior of the nearest neighbor queries as the parameters vary. This implementation has an API which is essentially as same as the [NearestNeighbors](http://scikit-learn.org/stable/modules/generated/sklearn.neighbors.NearestNeighbors.html#sklearn.neighbors.NearestNeighbors) module as approximate nearest neighbor search is used to speed up the queries at the cost of accuracy when the database is very large.

Before beginning the demonstration, background has to be set. First, the required modules are loaded and a synthetic dataset is created for testing.
{% highlight python %}
import time
import numpy as np
from sklearn.datasets.samples_generator import make_blobs
from sklearn.neighbors import LSHForest
from sklearn.neighbors import NearestNeighbors

# Initialize size of the database, iterations and required neighbors.
n_samples = 10000
n_features = 100
n_iter = 30
n_neighbors = 100
rng = np.random.RandomState(42)

# Generate sample data
X, _ = make_blobs(n_samples=n_samples, n_features=n_features,
                  centers=10, cluster_std=5, random_state=0)
{% endhighlight %}

There are two main parameters which affect queries in the LSH Forest implementation. 

1. `n_estimators` : Number of trees in the LSH Forest.
2. `n_candidates` : Number of candidates chosen from each tree for distance calculation.

In the first experiment, average accuracies are measured as the value of `n_estimators` vary. `n_candidates` is kept fixed. `slearn.neighbors.NearestNeighbors` used to obtain the true neighbors so that the returned approximate neighbors can be compared against.
{% highlight python %}
# Set `n_estimators` values
n_estimators_values = np.linspace(1, 30, 5).astype(np.int)
accuracies_trees = np.zeros(n_estimators_values.shape[0], dtype=float)

# Calculate average accuracy for each value of `n_estimators`
for i, n_estimators in enumerate(n_estimators_values):
    lshf = LSHForest(n_candidates=500, n_estimators=n_estimators,
                     n_neighbors=n_neighbors)
    nbrs = NearestNeighbors(n_neighbors=n_neighbors, algorithm='brute')

    lshf.fit(X)
    nbrs.fit(X)
    for j in range(n_iter):
        query = X[rng.randint(0, n_samples)]
        neighbors_approx = lshf.kneighbors(query, return_distance=False)
        neighbors_exact = nbrs.kneighbors(query, return_distance=False)

        intersection = np.intersect1d(neighbors_approx,
                                      neighbors_exact).shape[0]
        ratio = intersection/float(n_neighbors)
        accuracies_trees[i] += ratio

    accuracies_trees[i] = accuracies_trees[i]/float(n_iter)
{% endhighlight %}
Similarly, average accuracy vs `n_candidates` is also measured.
{% highlight python %}
# Set `n_candidate` values
n_candidates_values = np.linspace(10, 500, 5).astype(np.int)
accuracies_c = np.zeros(n_candidates_values.shape[0], dtype=float)

# Calculate average accuracy for each value of `n_candidates`
for i, n_candidates in enumerate(n_candidates_values):
    lshf = LSHForest(n_candidates=n_candidates, n_neighbors=n_neighbors)
    nbrs = NearestNeighbors(n_neighbors=n_neighbors, algorithm='brute')
    # Fit the Nearest neighbor models
    lshf.fit(X)
    nbrs.fit(X)
    for j in range(n_iter):
        query = X[rng.randint(0, n_samples)]
        # Get neighbors
        neighbors_approx = lshf.kneighbors(query, return_distance=False)
        neighbors_exact = nbrs.kneighbors(query, return_distance=False)

        intersection = np.intersect1d(neighbors_approx,
                                      neighbors_exact).shape[0]
        ratio = intersection/float(n_neighbors)
        accuracies_c[i] += ratio

    accuracies_c[i] = accuracies_c[i]/float(n_iter)
{% endhighlight %}
You can get a clear view of the behavior of queries from the following plots.
![accuracies_c_l](https://docs.google.com/drawings/d/1NLa73eY1JDspoYBWW5bYl0JPoXTyA64JZWunQ4JYVO8/pub?w=960&h=720)

The next experiment demonstrates the behavior of queries for different database sizes (`n_samples`).
{% highlight python %}
# Initialize the range of `n_samples`
n_samples_values = [10, 100, 1000, 10000, 100000]
average_times = []
# Calculate the average query time
for n_samples in n_samples_values:
    X, labels_true = make_blobs(n_samples=n_samples, n_features=10,
                                centers=10, cluster_std=5,
                                random_state=0)
    # Initialize LSHForest for queries of a single neighbor
    lshf = LSHForest(n_candidates=1000, n_neighbors=1)
    lshf.fit(X)

    average_time = 0

    for i in range(n_iter):
        query = X[rng.randint(0, n_samples)]
        t0 = time.time()
        approx_neighbors = lshf.kneighbors(query,
                                           return_distance=False)[0]
        T = time.time() - t0
        average_time = average_time + T

    average_time = average_time/float(n_iter)
    average_times.append(average_time)
{% endhighlight %}
`n_samples` space is defined as [10, 100, 1000, 10000, 100000]. Query time for a single neighbor is measure for these different values of `n_samples`.
![query_time_vs_n_samples](https://docs.google.com/drawings/d/1KZwjo9Cx33kK-kTNayAT-eb79TPCbvX-kidyoPO7VJA/pub?w=960&h=720)
