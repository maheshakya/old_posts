---
layout: post
title:  "A quick guide to plotting with python and matplotlib - part 1"
category: miscellaneous
---
Representation of information and data visualization can be often considered as one of the most indispensable feats that requires subtlest attention in the present community of science and technology. So that's that. Now let's hop into plotting!

Python has this amazing library called [**matplotlib**](http://matplotlib.org/) where you can create plots of almost everything you  can ever wish of (yes, it supports almost all sorts of plots that can be drawn with [**R**](http://www.r-project.org/) now). But for a novice, this could be a little tricky at the beginning. Figuring out how to draw exactly what you might need is kind of a headache, since you don't have much experience in manipulating the resources packeged with this library. The documentation indeed provides a nice overview of what this library is capable of, but still one might want to create simple, yet the weirdest plot that no documentation or [*Stack Overflow*](http://stackoverflow.com/) answer could ever help (I guess I'm one of them and there are many others I know as well). So, let's try out some fundamental techniques first and then heed the deeper ones. These are some of the graphs I wanted to plot in many differenct circumstances. I assume these would provide you at least some assistance in creating your own plots. I'll be using [**numpy**](http://www.numpy.org/) library as well to create required data in these demonstrations. *Note that **matplotlib** and **numpy** are imported in advance.*

{% highlight python %}
import matplotlib.pyplot as plt
import numpy as np
{% endhighlight %}

# Lines connecting scatter points with different marker for each line

The term `marker` refers to a symbol that represents a point in a graph. There are numerous markers in **matplotlib**, so we will choose a few of them to demonstrate this. Typical syntax for scatter plot is `plt.scatter(x, y, marker=m)` where `x` is the set of the x-co-ordinates, `y` is the set of y-co-ordinates (these are compulsory arguments for `scatter` function) and `m` is the marker we are going to use. Here are some example markers:

- 'o'
- '+'
- 'v'
- '*'

Let's plot now.

{% highlight python %}
# There are 3 lines
num_of_lines = 3

# Defines a coluor for each line
colours = ['c', 'crimson', 'chartreuse'] 

# Defines a marker for each line
markers = ['o', 'v', '*']

# Creates x array with numbers ranged from 0 to 10(exclusive)
# Creates an empty list for y co-ordinates in each line
x = np.arange(10)
y = []

# For each line
for i in range(num_of_lines):
    # Adds to y according to y=ix+1 function
    y.append(x*i+1)

# This is where plotting happens!!!
# For each line
for i in range(num_of_lines):
    # Scatter plot with point_size^2 = 75, and with respective colors
    plt.scatter(x, y[i], marker=markers[i], s=75, c=colours[i])
    # Connects points with lines, and with respective colours
    plt.plot(x, y[i], c=colours[i])

# Show grid in the plot
plt.grid()
# Finally, display the plot
plt.show()
{% endhighlight %}

![plot_1](https://docs.google.com/drawings/d/1f4e4RIevPIcHI-ZVTzPRC3fh6gsG6qz1hGMeJ9Y_ajk/pub?w=960&h=720)

Fabulas, isn't it? Now we shall add a simple **legend** to this plot. This is what we are going to do. The upper left corner seems like an open space. So, let's add the legend there. A `Rectangle` is used to represent an entry in the legend.

{% highlight python %}
# Creates 3 Rectangles
p1 = plt.Rectangle((0, 0), 0.1, 0.1, fc=colours[0])
p2 = plt.Rectangle((0, 0), 0.1, 0.1, fc=colours[1])
p3 = plt.Rectangle((0, 0), 0.1, 0.1, fc=colours[2])

# Adds the legend into plot
plt.legend((p1, p2, p3), ('line1', 'line2', 'line3'), loc='best')
{% endhighlight %}

In the `legend` function, in addition to rectangles and the names of the entries, it is possible to specify the location of the legend as well. **'best'** gives best position for the legend in strict sense of word (which is the upper left corner). The other locations are as follows:

|Position         | #  |
|:---------------:|:--:|
|'best'           | 0  |
|'upper right'    | 1  |
|'upper left'     | 2  |
|'lower left'     | 3  |
|'lower right'    | 4  |
|'right'          | 5  |
|'center left'    | 6  |
|'center right'   | 7  |
|'lower center'   | 8  |
|'upper center'   | 9  |
|'center'         | 10 |

*Note that you can even use the corresponding number to specify the location.*

Now, simply add the legend code segment just before the `plt.show()` in the first code. You will see that there is a nice legend at the upper left corner.

![plot_2](https://docs.google.com/drawings/d/1TKLhYlSUnMBKrMvQOIbkwR968EbDWX6MpNI_PFJUSuQ/pub?w=960&h=720)

Only a little work is left to do... What? Naming the axes and entitling the plot. It takes only \\(3\\) lines of code. Add these lines just above the `plt.show()` function.

{% highlight python %}
# Sets x-axis
plt.xlabel('This is my x-axis')

# Sets y-axis
plt.ylabel('This is your y-axis')

# Sets title
plt.title("This is our title")
{% endhighlight %}

![plot_3](https://docs.google.com/drawings/d/1uQmm-dhvkYS65xjugbT35EHcas5ywsawx0_2eFTC19c/pub?w=960&h=720)

Now you know how to create the basic components of a plot. This will be the end of the first part of this guide. More interesting stuff will follow in the next parts.