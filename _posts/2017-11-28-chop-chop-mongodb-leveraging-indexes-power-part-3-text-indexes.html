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
excerpt: "\n\t\t\t\t\t\t"
---
<p>
				Hey folks! Today we will try to find some text in our collection. And then we will add text indexes there and behold, how it become better (or not). Let's grab a beer and start.</p>
<h2><!--more--><br />
Index creation.</h2>
<p>Text indexes were around for a while, but the latest version had been released in MongoDB 3.2. So keep it in mind when you'll try things I describe here. Let's create some data first:</p>
<p>[javascript]<br />
var bulk = db.posts.initializeUnorderedBulkOp();<br />
bulk.insert( { title: &quot;Chop-chop, MongoDB! Leveraging indexes power. Part 3. Text indexes&quot;, url: &quot;http://zonov.me/chop-chop-mongodb-leveraging-indexes-power-part-3-text-indexes/&quot; } );<br />
bulk.insert( { title: &quot;Chop-chop, MongoDB! Leveraging indexes power. Part 2. Multikey indexes&quot;, url: &quot;http://zonov.me/chop-chop-mongodb-leveraging-indexes-power-part-2-multikey-indexes/&quot; } );<br />
bulk.insert( { title: &quot;Chop-chop, MongoDB! Leveraging indexes power. Part 1&quot;, url: &quot;http://zonov.me/chop-chop-mongodb-leveraging-indexes-power-part-1/&quot; } );<br />
bulk.insert( { title: &quot;Introduction to Terraform. Terraform + Github&quot;, url: &quot;http://zonov.me/terrafom-introduction-with-github/&quot; } );<br />
bulk.insert( { title: &quot;How to install and use PostgreSQL (or whatever you want) using Docker&quot;, url: &quot;http://zonov.me/how-to-install-postgresql-using-docker/&quot; } );<br />
bulk.insert( { title: &quot;Pair programming. Do’s and don’ts. And hows.&quot;, url: &quot;http://zonov.me/pair-programming-dos-and-donts-and-hows/&quot; } );<br />
bulk.insert( { title: &quot;Python for Data Analysis book review&quot;, url: &quot;http://zonov.me/python-for-data-analysis-book-review/&quot; } );<br />
bulk.insert( { title: &quot;PostgreSQL transactions Isolation levels&quot;, url: &quot;http://zonov.me/postgresql-transactions-isolation-levels/&quot; } );<br />
bulk.insert( { title: &quot;Comparison of Ruby and Python’s Pandas for Data refinement&quot;, url: &quot;http://zonov.me/comparison-of-ruby-and-pythons-pandas-for-data-refinement/&quot; } );<br />
bulk.execute();&amp;amp;amp;amp;lt;span data-mce-type=&quot;bookmark&quot; style=&quot;display: inline-block; width: 0px; overflow: hidden; line-height: 0;&quot; class=&quot;mce_SELRES_start&quot;&amp;amp;amp;amp;gt;&amp;amp;amp;amp;lt;/span&amp;amp;amp;amp;gt;<br />
[/javascript]</p>
<p>Let's try to find all records, where I wrote anything related to PostgreSQL:</p>
<p>[javascript]<br />
db.posts.find( { $text: { $search: &quot;PostgresQl&quot; } } )<br />
[/javascript]</p>
<p>Baaang:</p>
<p>[javascript]<br />
Error: error: {<br />
	&quot;ok&quot; : 0,<br />
	&quot;errmsg&quot; : &quot;text index required for $text query&quot;,<br />
	&quot;code&quot; : 27,<br />
	&quot;codeName&quot; : &quot;IndexNotFound&quot;<br />
}<br />
[/javascript]</p>
<p>I'm pretty sure, anything can be better than that, so let's add an index, I believe there is no way to screw it up even more.</p>
<p>[javascript]<br />
db.posts.createIndex( { title: &quot;text&quot; } )<br />
[/javascript]</p>
<p>Pay attention, for Text Index you have to explicitly specify it. <i>(As you may remember, for <a href="http://zonov.me/chop-chop-mongodb-leveraging-indexes-power-part-2-multikey-indexes/" target="_blank" rel="noopener">Multikey Indexes it worked just well without any explicit mark</a>).</i><br />
Searching again and voila!</p>
<p>[javascript]<br />
/* 1 */<br />
{<br />
&quot;_id&quot; : ObjectId(&quot;5a1cfe4ed2f7c8316ff62b30&quot;),<br />
&quot;title&quot; : &quot;How to install and use PostgreSQL (or whatever you want) using Docker&quot;,<br />
&quot;url&quot; : &quot;http://zonov.me/how-to-install-postgresql-using-docker/&quot;<br />
}</p>
<p>/* 2 */<br />
{<br />
&quot;_id&quot; : ObjectId(&quot;5a1cfe4ed2f7c8316ff62b33&quot;),<br />
&quot;title&quot; : &quot;PostgreSQL transactions Isolation levels&quot;,<br />
&quot;url&quot; : &quot;http://zonov.me/postgresql-transactions-isolation-levels/&quot;<br />
}<br />
[/javascript]</p>
<p><b>Keep in mind!</b> You can create only one Text Index per collection!<br />
<b>But!</b> You can create one Text Index for all your fields in a collection:<br />
<i>If you try it - don't forget to remove the previous index</i></p>
<p>[javascript]<br />
db.posts.createIndex( { &quot;$**&quot;: &quot;text&quot; } )<br />
[/javascript]</p>
<p>And now you can search in all fields, f.e.:</p>
<p>[javascript]<br />
db.posts.find( { $text: { $search: &quot;indexes-power&quot; } } )<br />
[/javascript]</p>
<p>this query will search in both Title and Url fields.</p>
<h2>Sensitivity</h2>
<p>As a human, Text Index can be sensitive. Case sensitive. Or insensitive. So, there are two ways of sensitivity:</p>
<ol>
<li>Case sensitivity. Your index can be sensitive to the case of a text. If it's insensitive, it will perceive "monGoDb" as "mongodb". Case insensitive - is a default option.</li>
<li>Diacritic sensitivity. It's hard to explain in Latin, but in Russian, we have letters "Ъ" and "Ь". It's absolutely different letters, but as you may notice - they look pretty similar. And diacritic insensitivity means that they will be treated as the same letter. This is also a default option. <em>(you can find more diacritic letters in <a href="http://www.unicode.org/Public/8.0.0/ucd/PropList.txt" target="_blank" rel="noopener">Unicode spec</a>)</em></li>
</ol>
<h2>Weights</h2>
<p>By default, all your kinda text fields in a collection have the same "importance" for search. I can check it on my index:</p>
<p>[javascript]<br />
db.posts.createIndex( { title: &quot;text&quot;, url: &quot;text&quot; } )<br />
[/javascript]</p>
<p>[javascript]<br />
db.posts.getIndexes()</p>
<p>/* 1 */<br />
[<br />
    {...},<br />
    {<br />
        &quot;v&quot; : 2,<br />
        &quot;key&quot; : {<br />
            &quot;_fts&quot; : &quot;text&quot;,<br />
            &quot;_ftsx&quot; : 1<br />
        },<br />
        &quot;name&quot; : &quot;title_text_url_text&quot;,<br />
        &quot;ns&quot; : &quot;local.posts&quot;,<br />
        &quot;weights&quot; : {<br />
            &quot;title&quot; : 1,<br />
            &quot;url&quot; : 1<br />
        },<br />
        &quot;default_language&quot; : &quot;english&quot;,<br />
        &quot;language_override&quot; : &quot;language&quot;,<br />
        &quot;textIndexVersion&quot; : 3<br />
    }<br />
]<br />
[/javascript]</p>
<p>If you want, you can manually set weights for your fields. With my following index, search results will be ranged that on first positions there more likely will be Title matches.</p>
<p>[javascript]<br />
db.posts.createIndex( { title: &quot;text&quot;, url: &quot;text&quot; }, { weights: { title: 50, url: 10 } } )<br />
[/javascript]</p>
<p>It can be useful if you have few fields and you are sure that some of them are more important for a customer. F.e. in e-commerce solution for products you may have a higher weight for a title, lower for a description and even lower for comments <i>(yes, you can also add embedded documents into your index)</i>.</p>
<h2>More</h2>
<p>Some more things to keep in mind while using the Text Indexes.</p>
<ul>
<li>You don't need to manually stem your words, it is done automatically by MongoDB;</li>
<li>With it, keep in mind that if you use Text Index - your index file size will grow very fast;</li>
<li>MongoDB supports different languages for search, it's important because it doesn't count stop words in the search (like a, and, the);</li>
<li>You can compound Text Index with <a href="http://zonov.me/chop-chop-mongodb-leveraging-indexes-power-part-1/" target="_blank" rel="noopener">other indexes</a>.</li>
</ul>
<p>I would definitely consider using MongoDB Text Indexes in case of an early startup/MVP, when there is just no time to struggle with more comprehensive but also more complex solution from Elastic or Sphinx.</p>
<p>That is it for MongoDB indexes for now. It's still a big topic to discuss, though, and if I will find an interest in it - I will be writing more about this NoSQL DB with its indexes.</p>
<p>Be as fast as O(1) and have a beautiful day! :)		</p>
