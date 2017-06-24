---
layout: post
title: "Singular value decomposition to create a bench marking data set from MovieLens data"
description: ""
category: GSoC
---
This is the second article on my Google Summer of Code project and this follows from my [previous post]({% post_url 2014-05-04-approximate-nearest-neighbor-search-using-lsh %}) about the description about my project: Approximate nearest neighbor search using Locality sensitive hashing. Here, I will elaborate how I created my data set for prototyping, evaluating and bench marking purposes in the project. I have used [Singular value decomposition](http://en.wikipedia.org/wiki/Singular_value_decomposition) on the [MovieLens 1M](http://grouplens.org/datasets/movielens/) data to create this sample data set.

## MovieLens 1M data

[GroupLens Research](http://grouplens.org/) is and organization publishes research articles in conferences and journals primarily in the field of computer science, but also in other fields including psychology, sociology, and medicine. It has collected and made available rating data sets from the [MovieLens](http://movielens.org) web site. The data sets were collected over various periods of time, depending on the size of the set.

MovieLens 1M data set contains 1,000,209 anonymous ratings of approximately 3,900 movies made by 6,040 MovieLens users who joined MovieLens in 2000. After extracting the compressed content, there will be following files at your hand:

* ratings.dat : Contains user IDs, movie IDs, ratings on 5 star scale and time stamp.
* movies.dat  : Contains movie IDs, titles and genres.
* users.dat   : Contains user IDs, genders, ages, ocupations and zip-codes.

More information about this can be found in the [README](http://files.grouplens.org/datasets/movielens/ml-1m-README.txt). 

## A brief explanation about singular value decomposition and its' role in machine learning

Singular value decomposition is a matrix factorization method. The general equation can be expressed as follows.

$$X = USV^T$$

Suppose \\(X\\) has \\(n\\) rows and \\(d\\) columns. \\(U\\) is a matrix whose dimensions are \\(n \times n\\), \\(V\\) is another matrix whose dimensions are \\(d \times d\\), and \\(S\\) is a matrix whose dimensions are \\(n \times d\\), the same dimensions as \\(X\\). 
In addition, \\(U^T U = I _ n\\) and \\(V^T V = I _ d\\)

You can read and understand more about this decomposition method and how it works from this [article](http://en.wikibooks.org/wiki/Data_Mining_Algorithms_In_R/Dimensionality_Reduction/Singular_Value_Decomposition).

### What is the significance of the SVD(Singular Value Decomposition) in machine learning and what does it have to do with MovieLens data?

We can represent each movie from a dimension and each user corresponds to a data point in this high dimensional space. But we are not able to visualize more than three dimensions. This data can be represented by a matrix(The \\(X\\) in the above equation). A sample depiction of the matrix may look as follows. 
![user_movie_matrix](https://docs.google.com/drawings/d/1oBQ7iNf-c6GCYBvalyM7HXlcscX1ATz9lQsxzpHdCyQ/pub?w=960&h=720)
Because number of ratings for a movie by users is significantly low when considered with the number of users, this matrix contains a large number of empty entries. Therefore this matrix will be a very sparse matrix. Hence, approximating this matrix with a lower rank matrix is a worthwhile attempt.

Consider the following scenario:

If every user who likes "movie X" also likes "movie Y", then it is possible to group them together to form an agglomerative movie or feature. After forming new features in that way, two users can be compared by analyzing their ratings for different features rather than for individual movies.

In the same way different users may rate same movies similarly. So there can different types of similarities among user preferences.

According to this factorization method (you might want to read more about SVD at this point from the reference I have provided earlier) the matrix \\(S\\) is a diagonal matrix containing the singular values of the matrix \\(X\\). The number of singular values is exactly equal to the rank of the matrix \\(X\\). The rank of a matrix is the number of linearly independent rows or columns in the matrix. We know that two vectors are linearly independent if they cannot be written as the sum or scalar multiple of any other vectors in that vector space. You can notice that this linear independence somehow captures the notion of a feature or agglomerative item which we try to generate from in this approach. According to the above scenario, if every user who liked "Movie X" also liked "Movie Y", then those two movie vectors would be linearly dependent and would only contribute one to the rank.

So how are we to get rid of this redundant data. We can compare movies if most users who like one also like the other. In order to do that, we will keep the largest k singular values in \\(S\\). This will give us the best rank-k approximation to X. 

So the entire procedure can be boiled down to following three steps:

1. Compute the SVD: \\(X = U S V^T\\).
2. Form the matrix \\(S'\\) by keeping the k largest singular values and setting the others to zero.
3. Form the matrix \\(X _ lr\\) by \\(X _ lr = U S' V^T\\).

## Implementation

To perform SVD on MovieLens data set and recompose the matrix with a lower rank, I used scipy sparse matrix, numpy and pandas. It has been done in following steps.

1)  Import required packages.
{% highlight python %}
import pandas as pd
import numpy as np
import scipy.sparse as sp
from scipy.sparse.linalg import svds
import pickle
{% endhighlight %}

2)  Load data set into a pandas data frame.
{% highlight python %}
data_file = pd.read_table(r'ratings.dat', sep = '::', header=None)
{% endhighlight %}

Here,I have assumed that the `ratings.dat` file from MovieLens 1M data will be in the working directory. Only reason I am using pandas data frame is its' convenience of usage. You can directly open the file and proceed. But then you will have to change following steps to adapt to that method.

3)  Extract required meta information from the data set.
{% highlight python %}
users = np.unique(data_file[0])
movies = np.unique(data_file[1])
 
number_of_rows = len(users)
number_of_columns = len(movies)

movie_indices, user_indices = {}, {}
 
for i in range(len(movies)):
    movie_indices[movies[i]] = i
    
for i in range(len(users)):
    user_indices[users[i]] = i
{% endhighlight %}

As the user IDs and movie IDs are not continueous integers(there are missing numbers inbetween), a proper mapping is required. It will be used when inserting data into the matrix. At this point, you can delete the loaded data frame in order to save memory. But it is optional.

4)  Creating the sparse matrix and inserting data.
{% highlight python %}
#scipy sparse matrix to store the 1M matrix
V = sp.lil_matrix((number_of_rows, number_of_columns))

#adds data into the sparse matrix
for line in data_file.values:
    u, i , r , gona = map(int,line)
    V[user_indices[u], movie_indices[i]] = r
{% endhighlight %}

You can save sparse matrix `V` using `pickle` if you are willing to use it later. 
{% highlight python %}
#as these operations consume a lot of time, it's better to save processed data 
with open('movielens_1M.pickle', 'wb') as handle:
    pickle.dump(V, handle)
{% endhighlight %}

5)  Perform SVD.
{% highlight python %}
#as these operations consume a lot of time, it's better to save processed data 
#gets SVD components from 10M matrix
u,s, vt = svds(V, k = 500)
 
with open('movielens_1M_svd_u.pickle', 'wb') as handle:
    pickle.dump(u, handle)
with open('movielens_1M_svd_s.pickle', 'wb') as handle:
    pickle.dump(s, handle)
with open('movielens_1M_svd_vt.pickle', 'wb') as handle:
    pickle.dump(vt, handle)
{% endhighlight %}

The `svds` method performs the SVD. Parameter `k` is the number of singular values we want to retain. Here also I have save the intermediate data using `pickle`

After this decomposition you will get `u`, `s` and `vt`. They have (`number of users`, `k`), (`k`, ) and (`k`, `number of movies`) shapes respectively.

6)  Recomposing the lower rank matrix.

As `s` is a vector, we need to create a diagonal matrix form that with the diagonal containing the values of that vector. It is done as follows:
{% highlight python %}
s_diag_matrix = np.zeros((s.shape[0], s.shape[0]))

for i in range(s.shape[0]):
    s_diag_matrix[i,i] = s[i]
{% endhighlight %}

This will create a diagonal matrix. After that, all you have to do is get the matrix product of `u`, `s_diag_matix` and `vt` in that order.
{% highlight python %}
X_lr = np.dot(np.dot(u, s_diag_matrix), vt)
{% endhighlight %}

Now we have the lower rank approximation for \\(X\\) as \\(X _ lr = U S' V^T\\). Now this matrix can be used as a bench marking data set for the application.

## References
1. Dimensionality Reduction and the Singular Value Decomposition, Available[online]: [http://www.cs.carleton.edu/cs_comps/0607/recommend/recommender/svd.html](http://www.cs.carleton.edu/cs_comps/0607/recommend/recommender/svd.html)
2. Data Mining Algorithms In R/Dimensionality Reduction/Singular Value Decomposition, Available[online]: [http://en.wikibooks.org/wiki/Data_Mining_Algorithms_In_R/Dimensionality_Reduction/Singular_Value_Decomposition](http://en.wikibooks.org/wiki/Data_Mining_Algorithms_In_R/Dimensionality_Reduction/Singular_Value_Decomposition)

