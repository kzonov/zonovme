---
layout: post
title: Python for Data Analysis book review
date: 2017-08-20 12:23:39.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Data Engineering
- Development
tags:
- books
- data-science
- development
- pandas
- python
- книги
meta:
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  hefo_before: '0'
  hefo_after: '0'
  _aioseop_description: Personal review of the book Python for Data Analysis and some
    highlights from it.
  _aioseop_title: Python for Data Analysis - review
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/python-for-data-analysis-book-review/"
excerpt: "I just finished reading the book Python for Data Analysis. Here I want to share some points which were interesting for me, some examples and also my opinion on this book."
---

I just finished reading the book <a href="http://a.co/04UXQze">Python for Data Analysis</a>. Here I want to share some points which were interesting for me, some examples and also my opinion on this book.
<!--more-->
First of all, let me say about my experience in Python and Data Science area. It's almost 0 :)
I passed Machine Learning course on Coursera, had played with data on Kaggle.com a little bit. Also, I had a course of basic Data Analysis in university, but it was a long time ago. But so far I have a great experience with other interpreting languages, like Ruby and JS. So Python itself gives some WTFs for me, but in total there is nothing difficult. My expectation of this book was to understand deepness, coolness, and power of data-specific tools in Python. I can say that my expectations had been satisfied!
On Kaggle I played with data using Python with Pandas, but I was like a blind kitten, trying to leverage tools, which I don't really know. This book didn't help me learn how to work with data on Python, absolutely not, but it turned some unknown unknowns to known unknowns. But let's first take a look at some examples, showing a power of Pandas.
Let's build an array of normalized random numbers.
{% highlight javascript %}
In [1]: import numpy as np
In [2]: arr = np.random.randn(5, 4)
In [3]: arr
Out[3]:
array([[-0.85150584, 0.56817245, 1.69727504, -0.98400587],
[ 1.4706872 , -0.47147902, 0.57113332, -0.26893879],
[ 1.59553832, -0.82034912, -0.5345228 , 0.25674404],
[ 0.36665399, -0.01023922, 0.71392424, 0.94836333],
[ 1.13146502, -1.33895134, -1.47191369, 0.32307268]])
{% endhighlight %}
Nothing special, huh? Let's do some data magic with it!
{% highlight javascript %}
In [4]: arr[2]
Out[4]: array([ 1.59553832, -0.82034912, -0.5345228 ,  0.25674404])
{% endhighlight %}
"No dude, it's not a magic, we can do it in any language with the same syntax". Ok, next call:
{% highlight javascript %}
In [5]: arr[[2, 3]]
Out[5]:
array([[ 1.59553832, -0.82034912, -0.5345228 ,  0.25674404],
       [ 0.36665399, -0.01023922,  0.71392424,  0.94836333]])
{% endhighlight %}
This way you can easily access a subset of needed rows in your table or array. Keep in mind that all this you need to not just play with stupid random arrays, but to clean and analyze your data from pipelines or CSV or DB, whatever.
You can do any cool element-wise math magic with your table, f.e.:
{% highlight javascript %}
In [6]: np.floor(arr)
Out[6]:
array([[-1.,  0.,  1., -1.],
       [ 1., -1.,  0., -1.],
       [ 1., -1., -1.,  0.],
       [ 0., -1.,  0.,  0.],
       [ 1., -2., -2.,  0.]])
In [7]: np.add(arr, 1)
Out[7]:
array([[ 0.14849416,  1.56817245,  2.69727504,  0.01599413],
       [ 2.4706872 ,  0.52852098,  1.57113332,  0.73106121],
       [ 2.59553832,  0.17965088,  0.4654772 ,  1.25674404],
       [ 1.36665399,  0.98976078,  1.71392424,  1.94836333],
       [ 2.13146502, -0.33895134, -0.47191369,  1.32307268]])
In [8]: arr + 1
Out[8]:
array([[ 0.14849416,  1.56817245,  2.69727504,  0.01599413],
       [ 2.4706872 ,  0.52852098,  1.57113332,  0.73106121],
       [ 2.59553832,  0.17965088,  0.4654772 ,  1.25674404],
       [ 1.36665399,  0.98976078,  1.71392424,  1.94836333],
       [ 2.13146502, -0.33895134, -0.47191369,  1.32307268]])
In [9]: np.square(arr)
Out[9]:
array([[  7.25062192e-01,   3.22819939e-01,   2.88074255e+00, 9.68267554e-01],
       [  2.16292083e+00,   2.22292462e-01,   3.26193274e-01, 7.23280707e-02],
       [  2.54574252e+00,   6.72972680e-01,   2.85714622e-01, 6.59175011e-02],
       [  1.34435147e-01,   1.04841645e-04,   5.09687820e-01, 8.99393011e-01],
       [  1.28021310e+00,   1.79279069e+00,   2.16652990e+00, 1.04375953e-01]])
{% endhighlight %}
Let's add some meaning to this meaningless life:
{% highlight javascript %}
In [10]: arr.mean()
Out[10]: 0.14455619740592446
{% endhighlight %}
In the example above, it's a mean value among entire array. But that's not everything you can mean. You can also calculate mean row-wise or column-wise:
{% highlight javascript %}
In [11]: arr.mean(axis = 1)
Out[11]: array([ 0.10748395,  0.32535068,  0.12435261,  0.50467558, -0.33908183])
In [12]: arr.mean(axis = 0)
Out[12]: array([ 0.74256774, -0.41456925,  0.19517922,  0.05504708])
{% endhighlight %}
Let's make a step into more real data analysis-like actions:
{% highlight javascript %}
In [1]: import pandas as pd
In [2]: data = pd.DataFrame({'k1': ['one'] * 3 + ['two'] * 4, 'k2': [1, 1, 2, 3, 3, 4, 4]})
In [3]: data
Out[3]:
    k1  k2
0  one   1
1  one   1
2  one   2
3  two   3
4  two   3
5  two   4
6  two   4
{% endhighlight %}
DataFrame is table-like data structure in Pandas, you can google it if you want, this post is already big enough to cover this topic %)
As you may see - we have duplicating elements in there. Go remove 'em!
{% highlight javascript %}
In [4]: data.duplicated()
Out[4]:
0    False
1     True
2    False
3    False
4     True
5    False
6     True
dtype: bool
In [5]: data.drop_duplicates()
Out[5]:
    k1  k2
0  one   1
2  one   2
3  two   3
5  two   4
{% endhighlight %}
Isn't it cool, dude? You can also consider duplications inside some grouping or just in a subset of columns, which is amazing.
It's not all the power you have in Pandas and Numpy, but for other examples, I have to use prepared data sets, and I won't do it in this post. I think this should be enough to understand that this toolkit is pretty powerful.
But let's go back to the book and write some conclusion.
If you know nothing about data analysis with Python, but have some brief ideas of what is Python and what is data analysis - this book is for you. It will not teach you anything, but it will show, what you can google for in the future. If you own this book and can use it in everyday work life - it can be a good docs source for you too. But if you are either absolute newbie in both Python and data analysis or in the opposite - you are pretty familiar with statistics and have a brief idea about Python's toolkit and infrastructure - I bet using google will be more useful for you than this book.
I do not regret the time I spend reading this book but think twice before reading this or any of this kind of tutorial-like books.		
