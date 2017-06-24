---
layout: post
title: "Optimizations on Locality sensitive hashing forest data structure"
description: ""
category: GSoC 
---
In order to get LSH forest approximate nearest neighbor search method into a competitive and useful state, **optimization** was a necessity. These optimizations were based on the portions of the LSH forest algorithm and implementation of that algorithm. There are two general cases of bottlenecks where requirement for optimization arises.

1. Memory usage of the data structure.
2. Speed of queries.

Let's discuss these two cases separately in detail. Remember that there will always be a trade off between memory and speed.

## Optimizations on memory usage

In the [previous post about the implementation of the LSH forest]({% post_url 2014-06-01-lsh-forest-with-sorted-arrays-and-binary-search %}), I have explained how the basic data structure and the functions. Some of the optimizations caused obvious changes in those functions. First of all, I need to mention that this data structure stores the entire data set fitted because it will be required to calculate the actual distances of the candidates. So the memory consumption is usually predominated by the size of the data set. LSH Forest data structure does not have control over this. Only thing that can be controlled from the data structure is the set of hashes of the data points. 

Current data structure maintains a fixed length hash(but user can define that length at the time of initialization). Hashes are comprised of binary values (`1`s and `0`s). At the moment, these hashes are stored as binary strings. 

One of the optimizations which can be done to maintain a more compact index for hashes is using the equivalent integer of the binary strings. For an example, string `001110` can be represented as `14`. For very large data sets with very high dimensions, this technique will hold no effect as the improvement on the memory usage of hash indices is insignificant when compared to the memory required to store the data set. 

As I have mentioned earlier, there is always a cost for every optimization. Here we have to trade off speed for improved memory consumption. The LSH algorithm is applied to a single data point up to the number hash length required. In the default case, there will be `32` hashes per a single data point in a single tree and those hashed values have to be concatenated in order to create \\(g(p)\\)(refer to the [LSH Forest paper](http://ilpubs.stanford.edu:8090/678/1/2005-14.pdf)). Because the hashing is done separately for individual elements of  \\(g(p)\\), they are stored in an array in the first place. In order to create a single hash from these values in the array, they are first concatenated as a string. This is the point where memory speed dilemma occurs. These string hashes can be converted into integer hashes for compact indices but it will cost extra time. 

In the next section, I will explain how this extra overhead can be minimized using a trick, but the fact that "you cannot eliminate the memory speed trade-off completely" holds.

## Optimizations on query speeds

(I strongly recommend you to go through the [LSH forest paper](http://ilpubs.stanford.edu:8090/678/1/2005-14.pdf) before reading the following section because the description involves terms directly taken from the paper)

I was hapless at the beginning, because the query speeds of the implemented LSH forest structure were not at a competitive state to challenge the other approximate nearest neighbor search implementations. But with the help of my project mentors, I was able to get over with this issue.

### Optimizations on the algorithm

In the initial implementation, binary hash: \\(g(p)\\) for each tree is computed during the **synchronous ascending** phase when it was required. This causes a great overhead because each time it is computed, the LSH function has to be performed on the query vector. For _Random projections_, it is a dot product and it is an expensive operation for large vectors. 
![descending phase for a single tree](https://docs.google.com/drawings/d/1DCy4UsrJo1FXigeZvXeDzPEs_xsZ4AYgZfqERCjBujA/pub?w=960&h=720)
Computing the binary hashes for each tree in advance and storing them is a reliable solution for this issue. The algorithm descend needs to be performed on each tree to find the longest matching hash length. So the above algorithm will iterate over the number of trees in the forest. For each iteration, the binary hash of the query is calculated and stored as follows. This does not consume much memory because the number of trees is small(typically 10). 

{% highlight python %}
def descend(query, hash_functions, trees):
    bin_queries = []
    max_depth = 0
    for i in range(number_of_trees):
        binary_query = do_hash(query, hash_functions[i]
        k = find_longest_prefix_match(trees[i], binary_query)
        if k > max_depth:
            max_depth = k
        bin_queries.append(bin_query)
    return max_depth, binary_queries
{% endhighlight %}

It is an obvious fact that as the number of candidates for the nearest neighbors grows the number of actual distance calculations grows proportionally. This computation is also an expensive operation when compared with the other operations in the LSH forest structure. So limiting the number of candidates is also a passable optimization which can be done with respect to the algorithm.
![synchronous ascend phase](https://docs.google.com/drawings/d/1Q9ocBPxZzdvTEjP-Byw-OwXbBfPKwpBt-XV3_Fsmsqw/pub?w=946&h=386)
In the above function, one of the terminating conditions of the candidate search is maximum depth reaching 0 (The while loop runs until `x > 0`). But it should not necessarily run until maximum depth reaches 0 because we are looking for most viable candidates. It is known fact that for smaller matching hash lengths, it is unlikely to have a large similarity between two data points. Therefore, as the considered hash length is decreased, eligibility of the candidates we get is also decreased. Thus having a lower bound on the maximum depth will set a bound to candidates we collect. It decreases the probability of having irrelevant candidates. 
So this can be done by setting `x > lower_bound` in the while loop for some value of `lower_bound > 0`. But there is a risk of not retrieving the required number of neighbors for very small data sets because the the candidate search may terminate before it acquires the required number of candidates. Therefore user should be aware of a suitable `lower_bound` for the queries at the time of initialization.  

### Optimizations on the implementation

I have indicated that `bisect` operations comes with Python are faster for small lists but as the size of the list grow numpy `searchsorted` functions becomes faster. In my previous implementation, I have used an alternative version of `bisect_right` function as it does not fulfill the requirement of finding the right most index for a particular hash length in a sorted array(Please refer to my [previous post]({% post_url 2014-06-01-lsh-forest-with-sorted-arrays-and-binary-search %})if things are not clear). But we cannot create an alternative version of numpy `searchsorted` function, therefore a suitable transformation is required on the hash itself.

Suppose we have a binary hash `item = 001110`. What we need is the largest number with the first 4 bits being the same as `item`. `001111` suffices this requirement. So the transformation needed is replacing the last 2 bits with 1s. 
{% highlight python %}
def transform_right(item, h, hash_size):
    transformed_item = item[:h] + "".join(['1' for i in range(hash_size - h)])
    return transformed_right
{% endhighlight %}

The transformation needed to get the left most index is simple. This is `001100` which is last two bits replaced by 0s. This is same as having `0011`. Therefore only a string slicing operation `item[:4]` will do the the job. 

It gets more complicated when it comes to integer hashes. Integer has to be converted to the string, transformed and re-converted to integer. 
{% highlight python %}
def integer_transform(int_item, hash_size, h):
    str_item = ('{0:0'+str(hash_size)+'b}').format(int_item)
    transformed_str = transform_right(str_item, h, hash_size):
    transformed_int = int(transformed_str, 2)
    return transformed_int
{% endhighlight %}
But this is a very expensive operation for a query. For indexing, only `int(binary_hash, 2)` is required but this wont make much effect because the LSH algorithms on the data set dominates that operation completely. But for a query this is a significant overhead. Therefore we need an alternative.

Required integer representations for left and right operations can be obtained by performing the bit wise \\(AND\\) and \\(OR\\) operations with a suitable mask. Masks are generated by the following function.
{% highlight python %}
def _generate_masks(hash_size):
    left_masks, right_masks = [], []        
    for length in range(hash_size+1):
        left_mask  = int("".join(['1' for i in range(length)])
                         + "".join(['0' for i in range(hash_size-length)]), 2)
        left_masks.append(left_mask)
        right_mask = int("".join(['0' for i in range(length)])
                         + "".join(['1' for i in range(hash_size-length)]), 2)
        right_masks.append(right_mask)
    return left_masks, right_masks
{% endhighlight %}
These masks will be generated at the indexing time. Then the masks will be applied with the integer hashes.
{% highlight python %}
def apply_masks(item, left_masks, right_masks, h):
    item_left = item & left_masks[h]
    item_right = item | right_masks[h]
    return item_left, item_right
{% endhighlight %}
The left most and right most indices of a sorted array can be obtained in the following fashion.
{% highlight python %}
import numpy as np
def find_matching_indices(sorted_array, item_left, item_right):
    left_index = np.searchsorted(sorted_array, item_left)
    right_index = np.searchsorted(sorted_array, item_right, side = 'right')
    return np.arange(left_index, right_index)
{% endhighlight %}

As the masks are precomputed, the speed overhead at the query time is minimum in this approach. But still there is a little overhead in this approach because original hashed binary number are stored in an array and those numbers need to be concatenated and converted to obtain the corresponding integers. If the integers are cached this overhead will be eliminated.

### Cached hash

This is method which guarantees a significant speed up in the queries with expense of index building speed. At the indexing time, we can create a dictionary with key being arrays of hashed bits and the values being the corresponding integers. The number of items in the dictionary is the number of combinations for a particular hash length.

Example:

Suppose the hash length is 3. Then the bit combinations are:
![hash_bit_table](https://docs.google.com/drawings/d/1hiUf22xhydYwCedrn0QChcB8yGcv7C1puW0Y1a_n8ZI/pub?w=882&h=384)

Then it will be only a matter of a dictionary look-up. Once an integer is required for a particular hash bit array, it can be retrieved directly from the dictionary. 

The implementation of this type of hash is a bit tricky. Typically in LSH forests, the maximum hash length will be 32. Then the size of the dictionary will be \\(2^n = 4294967296\\) where \\(n = 32\\). This is an extremely infeasible size to hold in the memory(May require tens of Gigabytes). But when \\(n = 16\\), the size becomes \\(65536\\). This is a very normal size which can be easily stored in the memory. Therefore we use two caches of size 16. 

First a list for digits `0` and `1` is created.
{% highlight python %}
digits = ['0', '1']
{% endhighlight %}
The the cache is created as follows.
{% highlight python %}
import itertools

cache_N = 32 / 2
c = 2 ** cache_N # compute once

cache = {
   tuple(x): int("".join([digits[y] for y in x]),2)
   for x in itertools.product((0,1), repeat=cache_N)
}
{% endhighlight %}

Suppose `item` is a list of length 32 with binary values(0s and 1s). Then we can obtain the corresponding integer for that `item` as follows:
{% highlight python %}
xx = tuple(item)
int_item = cache[xx[:16]] * c + cache[xx[16:]]
{% endhighlight %}

This technique can be used in queries to obtain a significant improvement in speed assuming that the user is ready to sacrifice a portion of the memory.

Performance of all these techniques were measured using Pythons' `line_profiler` and `memory_profiler`. In my next post, I will explain concisely how these tools have been used to evaluate these implementations.