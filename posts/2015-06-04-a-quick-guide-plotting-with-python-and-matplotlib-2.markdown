---
layout: post
title:  "A quick guide to plotting with python and matplotlib - part 2"
category: miscellaneous
---
In the [previous part]({% post_url 2015-06-04-a-quick-guide-plotting-with-python-and-matplotlib %}) of this guide, we have discussed on how to create basic components of a plot. It's time to move into some advanced material. This is a continuation of the [part 1]({% post_url 2015-06-04-a-quick-guide-plotting-with-python-and-matplotlib %}) of this guide, assuming that you have read and grasped it already.

# A bar chart grouped accoring to some variable

In this plot, a bar chart will be created grouped based on a variable with each group having its' own similar components. You may know what it actually looks like after plotting then it will be easy to analyze the code and learn.

{% highlight python %}
import matplotlib.pyplot as plt
import numpy as np

# Defines the sizes of the axes
plt.axis([0,14, 0,140])

p1 = plt.Rectangle((0, 0), 0.1, 0.1, fc="crimson")
p2 = plt.Rectangle((0, 0), 0.1, 0.1, fc="burlywood")
p3 = plt.Rectangle((0, 0), 0.1, 0.1, fc="chartreuse")

plt.legend((p1, p2, p3), ('category 1','category 2','category 3'), loc='upper left')

# Defines labels for each group
labels = ['        ', '      1', '        ', '        ', '      2', '        ', '        ', '      4', '        ']

# Creates discrete values for x co-ordinates (widths of the bars)
x = np.array([0,1,2,5,6,7,10,11,12]) + 1

# Defines some random set of values for y (heights of the bars)
y = np.array([55.433, 55.855, 55.719, 55.433, 90.199, 93.563, 55.433, 104.807, 106.693])
 
# Replaces the names in the x-axis with labels
plt.xticks(x, labels)

# Creates the bar chart
plt.bar(left = x, height=y, color=['crimson', 'burlywood', 'chartreuse'])

plt.grid(which='both')
plt.ylabel('This is your y-axis')
plt.xlabel('This is my x-axis')
plt.title("This is our title")

plt.show()
{% endhighlight %}

![plot_4](https://docs.google.com/drawings/d/17Fx3dWiy6119BrOkSOyTLZWY01ekxIQihSIIKlk8lDA/pub?w=960&h=720)

No we'll try to analyze each new individual component of this code piece. Starting from the beginning:

- `plt.axis([0,14, 0,140])` sets the limits of x-axis from \\(0\\) to \\(14\\) and limits of y-axis from \\(0\\) to \\(140\\)
- `labels = ['        ', '      1', '        ', '        ', '      2', '        ', '        ', '      4', '        ']` is used to create a representation for a group name. Here, there are \\(9\\) elements in the list, since there are \\(9\\) bars. Those bars need to be grouped into \\(3\\) groups, hence for each group a label is given. Each label should be displayed right below the bar at the middle of each group.
- `plt.xticks(x, labels)` replaces the display names of the values on x-axis with the labels, but the actual co-ordinates of the bars remain same at those previously defined x-axis values.
- `plt.bar(left = x, height=y, color=['crimson', 'burlywood', 'chartreuse'])` is where the actual bars are plotted. For parameter `left`, x values are given so that the left end of the bars will be equal to the first element in `x`. For `height` parameter, y values are given. `color` parameter uses the given set of colors and applies those set of colors on the bars in a circular order. Here, only \\(3\\) colors are given, so those colors rotate around the \\(9\\) bars exactly \\(3\\) times.