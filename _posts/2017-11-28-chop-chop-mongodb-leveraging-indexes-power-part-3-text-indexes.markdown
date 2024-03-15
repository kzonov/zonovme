---
layout: post
title: Chop-chop, MongoDB! Leveraging indexes power. Part 3. Text indexes
date: 2017-11-28 09:56:00.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Data Engineering
- Databases
tags:
- database
- development
- mongodb
meta:
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/chop-chop-mongodb-leveraging-indexes-power-part-3-text-indexes/"
excerpt: "Hey folks! Today we will try to find some text in our collection. And then we will add text indexes there and behold, how it become better (or not). Let's grab a beer and start."
---

Hey folks! Today we will try to find some text in our collection. And then we will add text indexes there and behold, how it become better (or not). Let's grab a beer and start.
<h2><!--more-->
Index creation.</h2>
Text indexes were around for a while, but the latest version had been released in MongoDB 3.2. So keep it in mind when you'll try things I describe here. Let's create some data first:
{% highlight javascript %}
var bulk = db.posts.initializeUnorderedBulkOp();
bulk.insert( { title: "Chop-chop, MongoDB! Leveraging indexes power. Part 3. Text indexes", url: "http://zonov.me/chop-chop-mongodb-leveraging-indexes-power-part-3-text-indexes/" } );
bulk.insert( { title: "Chop-chop, MongoDB! Leveraging indexes power. Part 2. Multikey indexes", url: "http://zonov.me/chop-chop-mongodb-leveraging-indexes-power-part-2-multikey-indexes/" } );
bulk.insert( { title: "Chop-chop, MongoDB! Leveraging indexes power. Part 1", url: "http://zonov.me/chop-chop-mongodb-leveraging-indexes-power-part-1/" } );
bulk.insert( { title: "Introduction to Terraform. Terraform + Github", url: "http://zonov.me/terrafom-introduction-with-github/" } );
bulk.insert( { title: "How to install and use PostgreSQL (or whatever you want) using Docker", url: "http://zonov.me/how-to-install-postgresql-using-docker/" } );
bulk.insert( { title: "Pair programming. Do’s and don’ts. And hows.", url: "http://zonov.me/pair-programming-dos-and-donts-and-hows/" } );
bulk.insert( { title: "Python for Data Analysis book review", url: "http://zonov.me/python-for-data-analysis-book-review/" } );
bulk.insert( { title: "PostgreSQL transactions Isolation levels", url: "http://zonov.me/postgresql-transactions-isolation-levels/" } );
bulk.insert( { title: "Comparison of Ruby and Python’s Pandas for Data refinement", url: "http://zonov.me/comparison-of-ruby-and-pythons-pandas-for-data-refinement/" } );
bulk.execute();&amp;amp;amp;lt;span data-mce-type="bookmark" style="display: inline-block; width: 0px; overflow: hidden; line-height: 0;" class="mce_SELRES_start"&amp;amp;amp;gt;&amp;amp;amp;lt;/span&amp;amp;amp;gt;
{% endhighlight %}
Let's try to find all records, where I wrote anything related to PostgreSQL:
{% highlight javascript %}
db.posts.find( { $text: { $search: "PostgresQl" } } )
{% endhighlight %}
Baaang:
{% highlight javascript %}
Error: error: {
	"ok" : 0,
	"errmsg" : "text index required for $text query",
	"code" : 27,
	"codeName" : "IndexNotFound"
}
{% endhighlight %}
I'm pretty sure, anything can be better than that, so let's add an index, I believe there is no way to screw it up even more.
{% highlight javascript %}
db.posts.createIndex( { title: "text" } )
{% endhighlight %}
Pay attention, for Text Index you have to explicitly specify it. <i>(As you may remember, for <a href="http://zonov.me/chop-chop-mongodb-leveraging-indexes-power-part-2-multikey-indexes/" target="_blank" rel="noopener">Multikey Indexes it worked just well without any explicit mark</a>).</i>
Searching again and voila!
{% highlight javascript %}
/* 1 */
{
"_id" : ObjectId("5a1cfe4ed2f7c8316ff62b30"),
"title" : "How to install and use PostgreSQL (or whatever you want) using Docker",
"url" : "http://zonov.me/how-to-install-postgresql-using-docker/"
}
/* 2 */
{
"_id" : ObjectId("5a1cfe4ed2f7c8316ff62b33"),
"title" : "PostgreSQL transactions Isolation levels",
"url" : "http://zonov.me/postgresql-transactions-isolation-levels/"
}
{% endhighlight %}
<b>Keep in mind!</b> You can create only one Text Index per collection!
<b>But!</b> You can create one Text Index for all your fields in a collection:
<i>If you try it - don't forget to remove the previous index</i>
{% highlight javascript %}
db.posts.createIndex( { "$**": "text" } )
{% endhighlight %}
And now you can search in all fields, f.e.:
{% highlight javascript %}
db.posts.find( { $text: { $search: "indexes-power" } } )
{% endhighlight %}
this query will search in both Title and Url fields.
<h2>Sensitivity</h2>
As a human, Text Index can be sensitive. Case sensitive. Or insensitive. So, there are two ways of sensitivity:
<ol>
<li>Case sensitivity. Your index can be sensitive to the case of a text. If it's insensitive, it will perceive "monGoDb" as "mongodb". Case insensitive - is a default option.</li>
<li>Diacritic sensitivity. It's hard to explain in Latin, but in Russian, we have letters "Ъ" and "Ь". It's absolutely different letters, but as you may notice - they look pretty similar. And diacritic insensitivity means that they will be treated as the same letter. This is also a default option. <em>(you can find more diacritic letters in <a href="http://www.unicode.org/Public/8.0.0/ucd/PropList.txt" target="_blank" rel="noopener">Unicode spec</a>)</em></li>
</ol>
<h2>Weights</h2>
By default, all your kinda text fields in a collection have the same "importance" for search. I can check it on my index:
{% highlight javascript %}
db.posts.createIndex( { title: "text", url: "text" } )
{% endhighlight %}
{% highlight javascript %}
db.posts.getIndexes()
/* 1 */
[
    {...},
    {
        "v" : 2,
        "key" : {
            "_fts" : "text",
            "_ftsx" : 1
        },
        "name" : "title_text_url_text",
        "ns" : "local.posts",
        "weights" : {
            "title" : 1,
            "url" : 1
        },
        "default_language" : "english",
        "language_override" : "language",
        "textIndexVersion" : 3
    }
]
{% endhighlight %}
If you want, you can manually set weights for your fields. With my following index, search results will be ranged that on first positions there more likely will be Title matches.
{% highlight javascript %}
db.posts.createIndex( { title: "text", url: "text" }, { weights: { title: 50, url: 10 } } )
{% endhighlight %}
It can be useful if you have few fields and you are sure that some of them are more important for a customer. F.e. in e-commerce solution for products you may have a higher weight for a title, lower for a description and even lower for comments <i>(yes, you can also add embedded documents into your index)</i>.
<h2>More</h2>
Some more things to keep in mind while using the Text Indexes.
<ul>
<li>You don't need to manually stem your words, it is done automatically by MongoDB;</li>
<li>With it, keep in mind that if you use Text Index - your index file size will grow very fast;</li>
<li>MongoDB supports different languages for search, it's important because it doesn't count stop words in the search (like a, and, the);</li>
<li>You can compound Text Index with <a href="http://zonov.me/chop-chop-mongodb-leveraging-indexes-power-part-1/" target="_blank" rel="noopener">other indexes</a>.</li>
</ul>
I would definitely consider using MongoDB Text Indexes in case of an early startup/MVP, when there is just no time to struggle with more comprehensive but also more complex solution from Elastic or Sphinx.
That is it for MongoDB indexes for now. It's still a big topic to discuss, though, and if I will find an interest in it - I will be writing more about this NoSQL DB with its indexes.
Be as fast as O(1) and have a beautiful day! :)		
