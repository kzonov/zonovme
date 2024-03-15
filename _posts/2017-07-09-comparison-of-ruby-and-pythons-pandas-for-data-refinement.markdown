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
excerpt: "\n\t\t\t\t\t\t"
---
<p>
				My main tool for every day is Ruby, but a few months ago I started using Python for playing with data. I heard a lot that Python is heavily used by Data Scientists and scientists in general, but I didn't expect that even for not a python-experienced developer it can give so much power. So today I want to briefly introduce you Python's library <code>pandas</code>.</p>
<p><!--more--></p>
<p>Here is a quote from the official website:</p>
<blockquote><p><a href="http://pandas.pydata.org/"><em>pandas</em></a> is an open source, BSD-licensed library providing high-performance, easy-to-use data structures and data analysis tools for the <a class="reference external" href="http://www.python.org/">Python</a> programming language.</p></blockquote>
<p>&nbsp;</p>
<p>So it says that it's easy to use data structures and data analysis tool, so let's check how easy and good is it.</p>
<p>I will take Ebay Kleinanziegen dataset of over 370000 used cars sold on their platform. I'm using <a href="https://www.kaggle.com/orgesleka/used-cars-database">this</a> source.</p>
<p>First of all, you should set yourself some goal of what insight do you want to get from your data. Here is the questions I want to know: <strong>Cars of which brand can be sold quicker in Germany.</strong></p>
<p>I expect you already know how to work with Ruby so I'll show here step by step guide how to implement it using Python and then just show the same operations with Ruby at once.</p>
<p>First, we need to read the CSV file into a dataset.</p>
<p>[python title="Python"]<br />
import pandas as pd</p>
<p>full_table = pd.read_csv('./input/autos.csv', sep=',', header=0, encoding='cp1252')<br />
[/python]</p>
<p>Next, let's check how the data looks like because we need to know what do we need and what we can get rid of.</p>
<p>[python title="Python"]<br />
full_table.head(1)<br />
# output:<br />
# dateCrawled        name  seller offerType  price abtest vehicleType  yearOfRegistration  gearbox  powerPS model  kilometer  monthOfRegistration fuelType       brand notRepairedDamage          dateCreated  nrOfPictures  postalCode             lastSeen<br />
# 2016-03-24 11:52:17  Golf_3_1.6  privat   Angebot    480   test         NaN                1993  manuell        0  golf     150000                    0   benzin  volkswagen               NaN  2016-03-24 00:00:00             0       70435  2016-04-07 03:16:57<br />
[/python]</p>
<p>Looks a bit inconvenient, but I already can see few columns which are not I'm not interested in at all. Let's clean our dataset from them.</p>
<p>[python title="Python"]<br />
full_table.count()<br />
# output<br />
# 371528<br />
full_table[full_table.seller == 'privat'].seller.count()<br />
# output</p>
<p># 371525<br />
full_table[full_table.seller != 'privat'].seller<br />
# output:<br />
# 59155     gewerblich<br />
# 149393    gewerblich<br />
# 186181    gewerblich<br />
# In german it means &quot;commercial&quot;, so looks like we can remove it without any doubts.</p>
<p>full_table = full_table[full_table.seller == 'privat']<br />
full_table[full_table.offerType != 'Angebot'].offerType.count()<br />
# output:<br />
# 12<br />
# There are those cars, which are not for sale, but just some people place an advertisement that they want to buy smth. For my analysis I don't need them.</p>
<p>full_table = full_table[full_table.offerType == 'Angebot']</p>
<p># Also let's check cars which have some unrepaired damage and delete them from the dataset.<br />
full_table[full_table.notRepairedDamage == 'ja'].notRepairedDamage.count()<br />
# ouput:<br />
# 36286</p>
<p># Yeah, a lot. But I still don't want to use them next.</p>
<p>full_table = full_table[full_table.notRepairedDamage != 'ja']<br />
[/python]</p>
<p>Now let's remove useless columns:</p>
<p>[python title="Python"]<br />
full_table.drop(['dateCrawled', 'seller', 'offerType', 'abtest', 'nrOfPictures', 'monthOfRegistration', 'notRepairedDamage'], axis='columns', inplace=&lt;b&gt;True&lt;/b&gt;)<br />
full_table.head(1)<br />
# output:<br />
# name  price vehicleType  yearOfRegistration  gearbox  powerPS model  kilometer fuelType       brand          dateCreated  postalCode             lastSeen<br />
# Golf_3_1.6    480         NaN                1993  manuell        0  golf     150000   benzin  volkswagen  2016-03-24 00:00:00       70435  2016-04-07 03:16:57<br />
[/python]</p>
<p>Next, let's think about which data is really useful for us and which is not. Here are some ideas:</p>
<ol>
<li>Too old cars are usually kind of retro and they have different pricing rules that regular ones. So I'll limit my data to have a year of registration to be &gt;= than 1996.</li>
<li>People sell new car in the next year very rarely, so I can limit year to be &lt; 2016</li>
<li>If a car has less than 50 horsepower it's probably not a car.</li>
<li>It should be used, so odometer data should be higher than 1000.</li>
<li>The car should be in Germany. So I'll limit the postal index.</li>
</ol>
<p>[python title="Python"]<br />
full_table = full_table[<br />
    (full_table.yearOfRegistration &amp;gt;= 1996)<br />
    &amp;amp; (full_table.yearOfRegistration &amp;lt; 2016)<br />
    &amp;amp; (full_table.powerPS &amp;gt; 50)<br />
    &amp;amp; (full_table.kilometer &amp;gt; 1000)<br />
    &amp;amp; (full_table.postalCode &amp;gt; 10000)<br />
    &amp;amp; (full_table.postalCode &amp;lt; 99999)<br />
    ]<br />
[/python]</p>
<p>In result, we have 239245 cars. It's still enough for us.</p>
<p>Then to understand how fast a car had been sold, I'll add a new column for it.</p>
<p>[python title="Python"]<br />
full_table['soldWithin'] =<br />
    pd.to_datetime(full_table.lastSeen) - pd.to_datetime(full_table.dateCreated)<br />
[/python]</p>
<p>I don't believe that a car can be sold in less than one hour. I had experience with it %)</p>
<p>Also if the car wasn't sold for more than 6 months - seems that seller just forgot to mark it as sold.</p>
<p>[python title="Python"]<br />
full_table = full_table[<br />
    (full_table.soldWithin &amp;gt; pd.to_timedelta('3 hours'))<br />
    &amp;amp; (full_table.soldWithin &amp;lt; pd.to_timedelta('180 days'))<br />
    ]<br />
[/python]</p>
<p>Ok, preparation step is finished, so we can start answering the questions.</p>
<p>[python title="Python"]<br />
full_table.groupby('brand').agg(<br />
    {'soldWithin': {'average': lambda x: np.mean(x)}}<br />
    ).soldWithin.sort_values('average').head(5)<br />
[/python]</p>
<p>Looks monstrous, but with a small explanation, you can understand everything. Here I'm grouping cars by their brand and then for each calculating mean value of their soldWithin column. After that, I basically sort them and return 5 with a least soldWithin time range.</p>
<p>And in result we have TADAAAM!:</p>
<blockquote><p>brand        average<br />
opel       8 days 22:28:41.981568<br />
daewoo     8 days 22:37:20.325423<br />
ford       9 days 02:04:24.859733<br />
renault    9 days 05:29:40.067139<br />
volkswagen 9 days 09:15:42.559146</p></blockquote>
<p>I could expect this result :)</p>
<p>So again want to say, I'm not a professional data scientist neither Python developer. So some constructions here can be not optimal or aggregation function may be not absolutely representative, but it can still give you some insights about how cool are pandas and that if you need to work with CSV-like data in your daily job - you should consider turning this work into Python.</p>
<p>&nbsp;</p>
<p>Here is Ruby version, as I said. Imho, much worse and muuuch slower to perform.</p>
<p>[ruby]<br />
require 'csv'</p>
<p>full_table = []<br />
CSV.foreach('./input/autos.csv', { headers: true, col_sep: ',', encoding: 'cp1252', quote_char: &quot;@&quot; }) do |row|<br />
  full_table &lt;&lt; row.to_h<br />
end</p>
<p>full_table.count<br />
# 371824</p>
<p># Remove records we don't need<br />
full_table.delete_if do |ft|<br />
  ft.fetch('seller') != 'privat' || ft.fetch('offerType') != 'Angebot' || ft.fetch('notRepairedDamage') == 'ja'<br />
end</p>
<p>full_table.count<br />
# 335501</p>
<p># Probably not the best way of deleting &quot;columns&quot;, but the one which works<br />
full_table.map! do |ft|<br />
  ft.reject do |k|<br />
    ['dateCrawled', 'seller', 'offerType', 'abtest', 'nrOfPictures', 'monthOfRegistration', 'notRepairedDamage'].include?(k)<br />
  end<br />
end</p>
<p># Again additional grooming of records.<br />
full_table.delete_if do |ft|<br />
  ft.fetch('yearOfRegistration').to_i &lt; 1996 || ft.fetch('yearOfRegistration').to_i &gt;= 2016 ||<br />
    ft.fetch('powerPS').to_i &lt; 50               ||<br />
    ft.fetch('kilometer').to_i &lt;= 1000          ||<br />
    ft.fetch('postalCode').to_i &lt; 10000 || ft.fetch('postalCode').to_i &gt; 99999<br />
end</p>
<p>full_table.count<br />
# 242529</p>
<p># Ruby's time difference represented in seconds.<br />
# Also I consider that all datetimes are in UTC.<br />
full_table.map! do |ft|<br />
  sold_within = Time.parse(ft.fetch('lastSeen') + ' UTC') -<br />
    Time.parse(ft.fetch('dateCreated') + ' UTC')<br />
  ft.merge('soldWithin' =&gt; sold_within)<br />
end</p>
<p># Final refinement.<br />
full_table.delete_if do |ft|<br />
  ft.fetch('soldWithin') &lt;= 3 * 60 * 60 || ft.fetch('soldWithin') &gt;= 180 * 24 * 60 * 60<br />
end</p>
<p>full_table.count<br />
# 242394</p>
<p># So let's find the Answer!<br />
grouped_by_brand = full_table.group_by { |ft| ft.fetch('brand') }</p>
<p>grouped_by_brand = grouped_by_brand.inject([]) do |result, hash|<br />
  result &lt;&lt; [<br />
    hash.first,<br />
    hash.last.inject(0) { |m, h| m + h.fetch('soldWithin')} / hash.last.size<br />
  ]<br />
end</p>
<p>grouped_by_brand.sort_by { |gb| gb.last }[0..4]<br />
# [[&quot;opel&quot;, 771839.5123578034], [&quot;daewoo&quot;, 771941.4848484849],<br />
#  [&quot;ford&quot;, 780678.1240594605], [&quot;renault&quot;, 796958.4229377713],<br />
#  [&quot;volkswagen&quot;, 807704.2994675058]]<br />
[/ruby]</p>
<p><img class="aligncenter size-medium" src="{{ site.baseurl }}/assets/2017/07/panda-daycare-nursery-chengdu-research-base-breeding-8.jpg" alt="taken from http://www.boredpanda.com/" width="700" height="524" />		</p>
