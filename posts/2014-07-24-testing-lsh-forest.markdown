---
layout: post
title: "Testing LSH Forest"
description: ""
category: GSoC
---
There are two types of tests to perform in order to ensure the correct functionality the LSH Forest.

1. Tests for individual functions of the LSHForest class.
2. Tests for accuracy variation with parameters of implementation.

scikit-learn provides a nice set testing tools for this task. It is elaborated in the [utilities for developers](http://scikit-learn.org/stable/developers/utilities.html) section. I have used following assertions which were imported from `sklearn.utils.testing`. Note that `numpy` as imported as `np`.

1. `assert_array_equal` - Compares each element in an array.
2. `assert_equal` - Compares two values.
3. `assert_raises` - Checks whether the given type of error is raised.

## Testing individual functions

### Testing `fit` function

Requirement of this test is to ensure that the fit function does not work without the necessary arguments provision and it produces correct attributes in the class object(in the sense of dimensions of arrays).

Suppose we initialize a LSHForest as `lshf = LSHForest()`

If the estimator is not fitted with a proper data, it will produce a value error and it is testes as follows:
{% highlight python %}
    X = np.random.rand(samples, dim)
    lshf = LSHForest(n_trees=n_trees)
    lshf.fit(X)
{% endhighlight %}
We define the sample size and the dimension of the dataset as `samples` and `dim` respectively and the number of trees in the LSH forest as `n_trees`.
{% highlight python %}
    # Test whether a value error is raised when X=None
    assert_raises(ValueError, lshf.fit, None)
{% endhighlight %}
Then after fitting the estimator, following assertions should hold true.
{% highlight python %}
    # _input_array = X
    assert_array_equal(X, lshf._input_array)
    # A hash function g(p) for each tree
    assert_equal(n_trees, lshf.hash_functions_.shape[0])
    # Hash length = 32
    assert_equal(32, lshf.hash_functions_.shape[1])
    # Number of trees in the forest
    assert_equal(n_trees, lshf._trees.shape[0])
    # Each tree has entries for every data point
    assert_equal(samples, lshf._trees.shape[1])
    # Original indices after sorting the hashes
    assert_equal(n_trees, lshf._original_indices.shape[0])
    # Each set of original indices in a tree has entries for every data point
    assert_equal(samples, lshf._original_indices.shape[1])
{% endhighlight %}

All the other tests for functions also contain the tests for valid arguments, therefore I am not going to describe them in those sections.

### Testing `kneighbors` function

`kneighbors` tests are based on the number of neighbors returned, neighbors for a single data point and multiple data points.

We define the required number of neighbors as `n_neighbors` and crate a LSHForest.
{% highlight python %}
    n_neighbors = np.random.randint(0, samples)
    point = X[np.random.randint(0, samples)]
    neighbors = lshf.kneighbors(point, n_neighbors=n_neighbors,
                                return_distance=False)
    # Desired number of neighbors should be returned.
    assert_equal(neighbors.shape[1], n_neighbors)
{% endhighlight %}
For multiple data points, we define number of points as `n_points`:
{% highlight python %}
    points = X[np.random.randint(0, samples, n_points)]
    neighbors = lshf.kneighbors(points, n_neighbors=1,
                                return_distance=False)
    assert_equal(neighbors.shape[0], n_points)
{% endhighlight %}
The above tests ensures that the maximum hash length is also exhausted because the data points in the same data set are used in queries. But to ensure that hash lengths less than the maximum hash length also get involved, there is another test. 
{% highlight python %}
    # Test random point(not in the data set)
    point = np.random.randn(dim)
    lshf.kneighbors(point, n_neighbors=1,
                    return_distance=False)
{% endhighlight %}

### Testing distances of the `kneighbors` function

Returned distances should be in sorted order, therefore it is tested as follows:
Suppose `distances` is the returned distances from the `kneighbors` function when the `return_distances` parameter is set to `True`.
{% highlight python %}
    assert_array_equal(distances[0], np.sort(distances[0]))
{% endhighlight %}

### Testing `insert` function

Testing `insert` is somewhat similar to testing `fit` because what we have to ensure are dimensions and sample sizes. Following assertions should hold true after fitting the LSHFores.
{% highlight python %}
    # Insert wrong dimension
    assert_raises(ValueError, lshf.insert,
                  np.random.randn(dim-1))
    # Insert 2D array
    assert_raises(ValueError, lshf.insert,
                  np.random.randn(dim, 2))

    lshf.insert(np.random.randn(dim))

    # size of _input_array = samples + 1 after insertion
    assert_equal(lshf._input_array.shape[0], samples+1)
    # size of _original_indices[1] = samples + 1
    assert_equal(lshf._original_indices.shape[1], samples+1)
    # size of _trees[1] = samples + 1
    assert_equal(lshf._trees.shape[1], samples+1)
{% endhighlight %}

## Testing accuracy variation with parameters

The accuracy of the results obtained from the queries depends on two major parameters.

1. c value - `c`.
2. number of trees - `n_trees`.

Separate tests have been written to ensure that the accuracy variation is correct with these parameter variation.

### Testing accuracy against `c` variation

Accuracy should be in an increasing order as the value of `c` is raised. Here, the average accuracy is calculated for different `c` values. 
{% highlight python %}
    c_values = np.array([10, 50, 250])
{% endhighlight %}
Then the calculated accuracy values of each `c` value is stored in an array. Following assertion should hold true in order to make sure that, higher the `c` value, higher the accuracy.
{% highlight python %}
    # Sorted accuracies should be equal to original accuracies
    assert_array_equal(accuracies, np.sort(accuracies))
{% endhighlight %}

### Testing accuracy against `n_trees` variation

This is as almost same as the above test. First, `n_trees` are stored in the ascending order.
{% highlight python %}
    n_trees = np.array([1, 10, 100])
{% endhighlight %}
After calculating average accuracies, the assertion is performed.
{% highlight python %}
    # Sorted accuracies should be equal to original accuracies
    assert_array_equal(accuracies, np.sort(accuracies))
{% endhighlight %}

## What is left?

In addition to the above tests, precision should also be tested against `c` values and `n_trees`. But it has already been tested in the prototyping stage and those tests consume a reasonably large amount of time which makes them unable to be performed in the scikit-learn test suit. Therefore, a separate benchmark will be done on this topic.

## About Nose

As provided in the guidelines in scikit-learn [contributors' page](http://scikit-learn.org/stable/developers/), [Nose](http://nose.readthedocs.org/en/latest/index.html) testing framework has been used to automate the testing process.
