---
layout: post
title: Comparison of Ruby and Python's Pandas for Data refinement
date: 2017-07-09 15:38:01.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Development
tags:
- data-science
- pandas
- python
- ruby
meta:
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  _wp_old_slug: comparison-of-ruby-and-pythons-pandas-in-data-grooming
  hefo_before: '0'
  hefo_after: '0'
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/comparison-of-ruby-and-pythons-pandas-for-data-refinement/"
excerpt: "My main tool for every day is Ruby, but a few months ago I started using Python for playing with data."
---

My main tool for every day is Ruby, but a few months ago I started using Python for playing with data. I heard a lot that Python is heavily used by Data Scientists and scientists in general, but I didn't expect that even for not a python-experienced developer it can give so much power. So today I want to briefly introduce you Python's library <code>pandas</code>.
<!--more-->
Here is a quote from the official website:
<blockquote><a href="http://pandas.pydata.org/"><em>pandas</em></a> is an open source, BSD-licensed library providing high-performance, easy-to-use data structures and data analysis tools for the <a class="reference external" href="http://www.python.org/">Python</a> programming language.</blockquote>
 
So it says that it's easy to use data structures and data analysis tool, so let's check how easy and good is it.
I will take Ebay Kleinanziegen dataset of over 370000 used cars sold on their platform. I'm using <a href="https://www.kaggle.com/orgesleka/used-cars-database">this</a> source.
First of all, you should set yourself some goal of what insight do you want to get from your data. Here is the questions I want to know: <strong>Cars of which brand can be sold quicker in Germany.</strong>
I expect you already know how to work with Ruby so I'll show here step by step guide how to implement it using Python and then just show the same operations with Ruby at once.
First, we need to read the CSV file into a dataset.
{% highlight python %}
import pandas as pd
full_table = pd.read_csv('./input/autos.csv', sep=',', header=0, encoding='cp1252')
{% endhighlight %}
Next, let's check how the data looks like because we need to know what do we need and what we can get rid of.
{% highlight python %}
full_table.head(1)
# output:
# dateCrawled        name  seller offerType  price abtest vehicleType  yearOfRegistration  gearbox  powerPS model  kilometer  monthOfRegistration fuelType       brand notRepairedDamage          dateCreated  nrOfPictures  postalCode             lastSeen
# 2016-03-24 11:52:17  Golf_3_1.6  privat   Angebot    480   test         NaN                1993  manuell        0  golf     150000                    0   benzin  volkswagen               NaN  2016-03-24 00:00:00             0       70435  2016-04-07 03:16:57
{% endhighlight %}
Looks a bit inconvenient, but I already can see few columns which are not I'm not interested in at all. Let's clean our dataset from them.
{% highlight python %}
full_table.count()
# output
# 371528
full_table[full_table.seller == 'privat'].seller.count()
# output
# 371525
full_table[full_table.seller != 'privat'].seller
# output:
# 59155     gewerblich
# 149393    gewerblich
# 186181    gewerblich
# In german it means "commercial", so looks like we can remove it without any doubts.
full_table = full_table[full_table.seller == 'privat']
full_table[full_table.offerType != 'Angebot'].offerType.count()
# output:
# 12
# There are those cars, which are not for sale, but just some people place an advertisement that they want to buy smth. For my analysis I don't need them.
full_table = full_table[full_table.offerType == 'Angebot']
# Also let's check cars which have some unrepaired damage and delete them from the dataset.
full_table[full_table.notRepairedDamage == 'ja'].notRepairedDamage.count()
# ouput:
# 36286
# Yeah, a lot. But I still don't want to use them next.
full_table = full_table[full_table.notRepairedDamage != 'ja']
{% endhighlight %}
Now let's remove useless columns:
{% highlight python %}
full_table.drop(['dateCrawled', 'seller', 'offerType', 'abtest', 'nrOfPictures', 'monthOfRegistration', 'notRepairedDamage'], axis='columns', inplace=<b>True</b>)
full_table.head(1)
# output:
# name  price vehicleType  yearOfRegistration  gearbox  powerPS model  kilometer fuelType       brand          dateCreated  postalCode             lastSeen
# Golf_3_1.6    480         NaN                1993  manuell        0  golf     150000   benzin  volkswagen  2016-03-24 00:00:00       70435  2016-04-07 03:16:57
{% endhighlight %}
Next, let's think about which data is really useful for us and which is not. Here are some ideas:
<ol>
<li>Too old cars are usually kind of retro and they have different pricing rules that regular ones. So I'll limit my data to have a year of registration to be >= than 1996.</li>
<li>People sell new car in the next year very rarely, so I can limit year to be < 2016</li>
<li>If a car has less than 50 horsepower it's probably not a car.</li>
<li>It should be used, so odometer data should be higher than 1000.</li>
<li>The car should be in Germany. So I'll limit the postal index.</li>
</ol>
{% highlight python %}
full_table = full_table[
    (full_table.yearOfRegistration &gt;= 1996)
    &amp; (full_table.yearOfRegistration &lt; 2016)
    &amp; (full_table.powerPS &gt; 50)
    &amp; (full_table.kilometer &gt; 1000)
    &amp; (full_table.postalCode &gt; 10000)
    &amp; (full_table.postalCode &lt; 99999)
    ]
{% endhighlight %}
In result, we have 239245 cars. It's still enough for us.
Then to understand how fast a car had been sold, I'll add a new column for it.
{% highlight python %}
full_table['soldWithin'] =
    pd.to_datetime(full_table.lastSeen) - pd.to_datetime(full_table.dateCreated)
{% endhighlight %}
I don't believe that a car can be sold in less than one hour. I had experience with it %)
Also if the car wasn't sold for more than 6 months - seems that seller just forgot to mark it as sold.
{% highlight python %}
full_table = full_table[
    (full_table.soldWithin &gt; pd.to_timedelta('3 hours'))
    &amp; (full_table.soldWithin &lt; pd.to_timedelta('180 days'))
    ]
{% endhighlight %}
Ok, preparation step is finished, so we can start answering the questions.
{% highlight python %}
full_table.groupby('brand').agg(
    {'soldWithin': {'average': lambda x: np.mean(x)}}
    ).soldWithin.sort_values('average').head(5)
{% endhighlight %}
Looks monstrous, but with a small explanation, you can understand everything. Here I'm grouping cars by their brand and then for each calculating mean value of their soldWithin column. After that, I basically sort them and return 5 with a least soldWithin time range.
And in result we have TADAAAM!:
<blockquote>brand        average
opel       8 days 22:28:41.981568
daewoo     8 days 22:37:20.325423
ford       9 days 02:04:24.859733
renault    9 days 05:29:40.067139
volkswagen 9 days 09:15:42.559146</blockquote>
I could expect this result :)
So again want to say, I'm not a professional data scientist neither Python developer. So some constructions here can be not optimal or aggregation function may be not absolutely representative, but it can still give you some insights about how cool are pandas and that if you need to work with CSV-like data in your daily job - you should consider turning this work into Python.
 
Here is Ruby version, as I said. Imho, much worse and muuuch slower to perform.
{% highlight go %}
require 'csv'
full_table = []
CSV.foreach('./input/autos.csv', { headers: true, col_sep: ',', encoding: 'cp1252', quote_char: "@" }) do |row|
  full_table << row.to_h
end
full_table.count
# 371824
# Remove records we don't need
full_table.delete_if do |ft|
  ft.fetch('seller') != 'privat' || ft.fetch('offerType') != 'Angebot' || ft.fetch('notRepairedDamage') == 'ja'
end
full_table.count
# 335501
# Probably not the best way of deleting "columns", but the one which works
full_table.map! do |ft|
  ft.reject do |k|
    ['dateCrawled', 'seller', 'offerType', 'abtest', 'nrOfPictures', 'monthOfRegistration', 'notRepairedDamage'].include?(k)
  end
end
# Again additional grooming of records.
full_table.delete_if do |ft|
  ft.fetch('yearOfRegistration').to_i < 1996 || ft.fetch('yearOfRegistration').to_i >= 2016 ||
    ft.fetch('powerPS').to_i < 50               ||
    ft.fetch('kilometer').to_i <= 1000          ||
    ft.fetch('postalCode').to_i < 10000 || ft.fetch('postalCode').to_i > 99999
end
full_table.count
# 242529
# Ruby's time difference represented in seconds.
# Also I consider that all datetimes are in UTC.
full_table.map! do |ft|
  sold_within = Time.parse(ft.fetch('lastSeen') + ' UTC') -
    Time.parse(ft.fetch('dateCreated') + ' UTC')
  ft.merge('soldWithin' => sold_within)
end
# Final refinement.
full_table.delete_if do |ft|
  ft.fetch('soldWithin') <= 3 * 60 * 60 || ft.fetch('soldWithin') >= 180 * 24 * 60 * 60
end
full_table.count
# 242394
# So let's find the Answer!
grouped_by_brand = full_table.group_by { |ft| ft.fetch('brand') }
grouped_by_brand = grouped_by_brand.inject([]) do |result, hash|
  result << [
    hash.first,
    hash.last.inject(0) { |m, h| m + h.fetch('soldWithin')} / hash.last.size
  ]
end
grouped_by_brand.sort_by { |gb| gb.last }[0..4]
# [["opel", 771839.5123578034], ["daewoo", 771941.4848484849],
#  ["ford", 780678.1240594605], ["renault", 796958.4229377713],
#  ["volkswagen", 807704.2994675058]]
{% endhighlight %}
<img class="aligncenter size-medium" src="{{ site.baseurl }}/assets/2017/07/panda-daycare-nursery-chengdu-research-base-breeding-8.jpg" alt="taken from http://www.boredpanda.com/" width="700" height="524" />		
