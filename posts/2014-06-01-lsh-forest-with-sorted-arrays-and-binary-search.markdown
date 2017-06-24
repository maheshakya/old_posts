---
layout: post
title: "LSH Forest with sorted arrays and binary search"
description: ""
category: GSoC
---
More on GSoC with [scikit-learn](http://scikit-learn.org/stable/index.html)! LSH forest is a promising, novel and alternative method introduced in order to alleviate the drawbacks from which vanilla LSH suffers. I assume you have a passable idea of what LSH means. If not, I suggest you to refer to this: [Locality-sensitive hashing](http://en.wikipedia.org/wiki/Locality-sensitive_hashing). LSH forest has a theoretical guarantee of its' suggested improvements. For more information, refer to the published paper: [LSH Forest: Self-Tuning Indexes for Similarity Search](http://ilpubs.stanford.edu:8090/678/1/2005-14.pdf)

In general, the data structure used to implement LSH forest is a [prefix tree](http://en.wikipedia.org/wiki/Trie)(trie). In this article, I will elaborate how to implement it with sorted arrays and binary search. This will reduce the complexity involved with a separate data structure(such as a tree). You can see the complete implementation in this [gist](https://gist.github.com/maheshakya/b22f640f67d7b574fd56).

## How it is done

This implementation follows every design aspect suggested in the LSH forest paper except the data structure. LSH_forest is a class which has the initialization method as follows:
{% highlight python %}
    def __init__(self, max_label_length = 32, number_of_trees = 5):
        self.max_label_length = max_label_length
        self.number_of_trees = number_of_trees
        self.random_state = np.random.RandomState(seed=1)
{% endhighlight %}
`numpy` has been used and imported as `np`. Variable names are as same as the names in the paper. The length of the hash is a fixed value. This value will be a small integer for almost all the applications.

In a normal LSH based nearest neighbor search, there are two main operations. 

1. Building index
2. Queries

### Building index

First stage of building index is hashing the data point in the data set passed into the function. [Random projection](http://en.wikipedia.org/wiki/Locality-sensitive_hashing#Random_projection) has been used as the hashing algorithm(It belongs to LSH family). In order to perform random projection, a set of random hyper-planes is required with the shape of \\(expected Hash Size \times dimension Of The Data Vector\\). It is done by the following function. 
{% highlight python %}
    def _get_random_hyperplanes(self, hash_size = None, dim = None):
        """ 
        Generates hyperplanes from standard normal distribution  and return 
        it as a 2D numpy array. This is g(p,x) for a particular tree.
        """
        return self.random_state.randn(hash_size, dim) 
{% endhighlight %}
Then, the random projection is performed. It is a simple operation as all it needs to do is get the dot product of the generated hyper-planes and the data vectors. Then it will create a binary string taking the sign of the hash into account:
{% highlight python %}
    def _hash(self, input_point = None, hash_function = None):
        """
        Does hash on the data point with the provided hash_function: g(p,x).
        """
        projections = np.dot(hash_function, input_point)             
        return "".join(['1' if i > 0 else '0' for i in projections])
{% endhighlight %}
After this a tree(a figurative tree) is build using by sorting those binary hashes. At this point, original indices are retained because it will be only way to refer to the original vectors from now on. It is done as follows:
{% highlight python %}
    def _create_tree(self, input_array = None, hash_function = None):
        """
        Builds a single tree (in this case creates a sorted array of 
        binary hashes).
        """
        number_of_points = input_array.shape[0]
        binary_hashes = []
        for i in range(number_of_points):
            binary_hashes.append(self._hash(input_array[i], hash_function))
        
        binary_hashes = np.array(binary_hashes)
        o_i = np.argsort(binary_hashes)
        return o_i, np.sort(binary_hashes)
{% endhighlight %}
This is the process which has to be done a single tree. But there are multiple number of trees. So this has to be done for each tree. The above function is called for each tree with the corresponding hash function which is \\(g(p)\\). Then hash functions, trees and original indices are stored as `numpy` arrays.

### Queries

This is the tricky part of this implementation. All the tree operations indicated in the paper have to be converted into range queries in order to work with sorted arrays and binary search. I will move step by step about how binary search has been used in this application. 

The first objective is: given a sort array of binary hashes, a binary query and a hash value `h`, retrieve an array of indices where the most significant `h` bits of the entries are as same as the most significant `h` bits of the query. In order to achieve this, I have re-implemented the `bisect` functions(which comes by default with Python) with a little essential modification.
{% highlight python %}
def bisect_left(a, x):
    lo = 0
    hi = len(a)
    while lo < hi:
        mid = (lo+hi)//2
        if a[mid] < x:
            lo = mid + 1
        else:
            hi = mid
    return lo
            
def bisect_right(a, x):
    lo = 0
    hi = len(a)
    while lo < hi:
        mid = (lo+hi)//2
        if x < a[mid] and not a[mid][:len(x)]==x:
            hi = mid
        else:
            lo = mid + 1
    return lo

#function which accepts an sorted array of bit strings, a query string
#This returns an array containing all indices which share the first h bits of the query
def simpleFunctionBisectReImplemented(sorted_array, item, h):
    left_index = bisect_left(sorted_array, item[:h])
    right_index = bisect_right(sorted_array, item[:h])
    return np.arange(left_index, right_index) 
{% endhighlight %}

Here I have considered a minor aspect about slicing and `startswith` in Python string. In place of `a[mid][:len(x)]==x`, I could have used `startswith` in-built function. But after a little research, it became obvious why the latter is not suitable here. `startswith` works efficiently with very long strings, but slicing has been optimized from C level for efficiency for small strings. In this application, hash strings do not have a requirement to be very long. You can read more about this from this [question](http://stackoverflow.com/questions/13270888/why-is-startswith-slower-than-slicing).

The time complexity of this method is as any binary search. The number of entries `n` is the length of the array of sorted binary hashes. There are two searches in this method, but after performing it, the overall complexity will be \\(O(log n)\\). (You can do the math and confirm)

There is another binary search. It is to find the longest prefix match for a binary query string in a sorted array of binary hashes.
{% highlight python %}
def find_longest_prefix_match(bit_string_list, query):
    hi = len(query)
    lo = 0
    
    if len(simpleFunctionBisectReImplemented(bit_string_list, query, hi)) > 0:
        return hi
    
    while lo < hi:
        mid = (lo+hi)//2        
        k = len(simpleFunctionBisectReImplemented(bit_string_list, query, mid))
        if k > 0:
            lo = mid + 1
            res = mid
        else:
            hi = mid            
        
    return res
{% endhighlight %}
Time complexity of this operation is a little trickier. Binary searches on two different parameters are involved in this. The outer binary search corresponds to the length of the query string: say `v` and the inner binary search corresponds to length of the sorted array of binary hashes: say `n`. The it has a complexity of \\(logv \times logn\\). So it will be approximately \\(O(logn^K)\\) where \\(K=logv\\).

That is all the basic setting required to implement LSH forest with sorted arrays and binary search. Now we can move on to the actual implementation of queries as indicated in the paper. There are two main phases described to perform queries.

1. Descending phase.
2. Synchronous ascending phase. 

In the descending phase, the longest matching hash length for a particular query is retrieved from all the tree. This step is quite straightforward as all is does is using the above described longest prefix match function on each tree. From this `max_depth` (`x` in the paper) is found.

The query function accept a value for \\(c\\) (refer to the paper) as well. This determines the number of candidates returned from the function. This is \\(M\\) which is equal to \\(c \times numberOfTrees\\). In asynchronous ascend phase, starting from `x`, every matching `x` long entry from each tree is collected(in a loop). Then `x` is decreased by one. Same is done repeatedly for each tree until the required number of candidates are retrieved. During the process, the length of candidate list may grow greater than required number of candidates. But the search does not end until the following condition is sufficed(As described in the synchronous ascend algorithm in the paper).

condition: \\(x>0\\) and \\((lenth(candidates) > c \\) or  \\(length(unique(candidates)) > m)\\)

\\(M >> m\\) where \\(m\\) is the actual number of neighbors required. So after selecting the candidates, a true distance measure will be used to determine the actual neighbors. This will be done later as the project proceeds. The current implementation will be used to perform the tasks in the [evaluation criteria]({% post_url 2014-05-25-performance-evaluation-of-approximate-nearest-neighbor-search-implementations---part-1 %}) that I have discussed in my earlier post. 

In my next post I will illustrate how the various versions of LSH forest performs and a comparison with other ANN implementations. 

### Acronyms

1. LSH : Locality Sensitive Hashing
