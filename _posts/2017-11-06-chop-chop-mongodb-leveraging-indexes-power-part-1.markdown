---
layout: post
title: Chop-chop, MongoDB! Leveraging indexes power. Part 1
date: 2017-11-06 21:55:03.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Data Engineering
- Databases
- Development
tags:
- data-science
- database
- development
- mongodb
meta:
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  _aioseop_description: How to use single field and compound indexes in MongoDB and
    don't screw them up.
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/chop-chop-mongodb-leveraging-indexes-power-part-1/"
excerpt: "As you may know, PostgreSQL provides you four index types: B-tree, Hash, GiST and GIN. They all named the way that if you don’t know ’em you’ll never get which one do you need."
---

As you may know, <a href="http://zonov.me/how-to-install-postgresql-using-docker/" target="_blank" rel="noopener">PostgreSQL</a> provides you four index types: B-tree, Hash, GiST and GIN. They all named the way that if you don't know 'em you'll never get which one do you need. In MongoDB indexes are named in a more human-readable way. Here they are:
1. Single field index.
2. Compound index.
3. <a href="http://zonov.me/chop-chop-mongodb-leveraging-indexes-power-part-2-multikey-indexes/" target="_blank" rel="noopener">Multikey index</a>.
4. <a href="http://zonov.me/chop-chop-mongodb-leveraging-indexes-power-part-3-text-indexes/" target="_blank" rel="noopener">Text index</a>.
5. Hashed index.
6. 2dsphere, 2d, geoHaystack indexes.
Since I'm using Mongo for more than a year now, I worked with few of them and will elucidate you the most commonly used ones.
<!--more-->

<img class="alignleft size-medium wp-image-395" src="{{ site.baseurl }}/assets/2017/11/mongo-hedgehog-226x300.jpg" alt="Hedgehog who loves Mongo" width="226" height="300" />

I'll start with the most common one:
<h2>Single field index</h2>
It's pretty obvious, nah?
You have a collection, e.g. Users, they have emails. You use this field to search them, so you <strong>have to </strong>make this field indexed. And most likely when you'll search for them by email, you won't need to filter by other fields. So long story short - it's a best case to use single field index. And here how is it looks:
{% highlight shell %}db.users.createIndex( { email: 1 } ){% endhighlight %}
As you may encounter, there is a <code>1</code> in the expression. It means that you create an index in ascending order. If you provide <code>-1</code>, you'll create a descending index. Honestly, it makes no difference in case of single field index, so just consider using only ascending. If you want to get the hang of why is it - here is some explanation. Mongo uses B-Tree structure to build an index. So the searching time is always <code>O(log(n))</code>. And it doesn't matter, what you'll have on your rightest node - zzzzz@somemail.com or aaaaa@somemail.com, it will always take the same time to fetch it. Same works for ranges, if your records are close in ascending index, they will also be close to each other in descending one. And if they are in different buckets - it will be slower anyway.
No more tricks here, let's go further.
<h2>Compound index</h2>
I bet you have no idea, what does this index intend to index. What, you have?! No waaay.
Ok, let's imaging again our precious users. They have country code and city fields. Both are strings (honestly doesn't matter for our case, just why not?). And we are building e.g. a promo website for fancy but big coffee shop network with social elements. So you want to suggest your clients when they enter your website to join other locals from their city in their coffee party in one of your cafes. You may think that I'm overcomplicating and you don't need multikey index here, you can just index city and it would be enough. Huh, not so fast! Suppose I'm from Moscow, Russia and you want to show me my neighbors (kinda joke, Moscow has 10M+ inhabitants, so they wouldn't be really my neighbors, but anyway). But you also have a cafe in Moscow, Idaho, USA. Bang!
So, if no more doubts, let's create an index:
{% highlight shell %}db.users.createIndex( { country: 1, city: 1 } ){% endhighlight %}
Try to answer the question, can you swap the order of country and city keys in the expression above? <em>(you better do not)</em>
As you see - you can also set ascending/descending order here. It doesn't matter if you intend to search for users in a specific city in a specific country. But if you will want to search for users in a subset of countries with sorting in ascending order and subset of cities, sorted in descending order, but your city indexed in ascended order - here is what will happen:
{% highlight javascript %}
> db.customers.find().sort({"country": 1, "city": 1}).explain()
{
	"queryPlanner" : {
		"plannerVersion" : 1,
		"namespace" : "test.customers",
		"indexFilterSet" : false,
		"parsedQuery" : {
		},
		"winningPlan" : {
			"stage" : "FETCH",
			"inputStage" : {
"stage" : "IXSCAN",
"keyPattern" : {
	"country" : 1,
	"city" : 1
},
"indexName" : "country_1_city_1",
"isMultiKey" : false,
"multiKeyPaths" : {
	"country" : [ ],
	"city" : [ ]
},
"isUnique" : false,
"isSparse" : false,
"isPartial" : false,
"indexVersion" : 2,
"direction" : "forward",
"indexBounds" : {
	"country" : [
		"[MinKey, MaxKey]"
	],
	"city" : [
		"[MinKey, MaxKey]"
	]
}
			}
		},
		"rejectedPlans" : [ ]
	},
	"serverInfo" : {
		"host" : "595d0c8f0fb2",
		"port" : 27017,
		"version" : "3.4.10",
		"gitVersion" : "078f28920cb24de0dd479b5ea6c66c644f6326e9"
	},
	"ok" : 1
}
> db.customers.find().sort({"country": 1, "city": -1}).explain()
{
	"queryPlanner" : {
		"plannerVersion" : 1,
		"namespace" : "test.customers",
		"indexFilterSet" : false,
		"parsedQuery" : {
		},
		"winningPlan" : {
			"stage" : "SORT",
			"sortPattern" : {
"country" : 1,
"city" : -1
			},
			"inputStage" : {
"stage" : "SORT_KEY_GENERATOR",
"inputStage" : {
	"stage" : "COLLSCAN",
	"direction" : "forward"
}
			}
		},
		"rejectedPlans" : [ ]
	},
	"serverInfo" : {
		"host" : "595d0c8f0fb2",
		"port" : 27017,
		"version" : "3.4.10",
		"gitVersion" : "078f28920cb24de0dd479b5ea6c66c644f6326e9"
	},
	"ok" : 1
}
{% endhighlight %}
Looks verbose, but don't be afraid, the most important thing here is that if you have an index in ascending order and try to sort in the descending - you just won't be using the index at all :( So always pay attention to this!
One more cool thing here:
{% highlight javascript %}
> db.customers.find().sort({"country": 1}).explain()
{
	"queryPlanner" : {
		"plannerVersion" : 1,
		"namespace" : "test.customers",
		"indexFilterSet" : false,
		"parsedQuery" : {
		},
		"winningPlan" : {
			"stage" : "FETCH",
			"inputStage" : {
"stage" : "IXSCAN",
"keyPattern" : {
	"country" : 1,
	"city" : 1
},
"indexName" : "country_1_city_1",
"isMultiKey" : false,
"multiKeyPaths" : {
	"country" : [ ],
	"city" : [ ]
},
"isUnique" : false,
"isSparse" : false,
"isPartial" : false,
"indexVersion" : 2,
"direction" : "forward",
"indexBounds" : {
	"country" : [
		"[MinKey, MaxKey]"
	],
	"city" : [
		"[MinKey, MaxKey]"
	]
}
			}
		},
		"rejectedPlans" : [ ]
	},
	"serverInfo" : {
		"host" : "595d0c8f0fb2",
		"port" : 27017,
		"version" : "3.4.10",
		"gitVersion" : "078f28920cb24de0dd479b5ea6c66c644f6326e9"
	},
	"ok" : 1
}
{% endhighlight %}
I currently don't have an index for countries explicitly. But because I have the compound index for country and city - I can use it to search only for countries. The same works if you have more than two parts in the compound index. F.e. if you have <code>{a: 1, b: 1, c: 1}</code> index, you can search in <code>{a:1, b: 1}</code> and this index will be used. Lovely.
 
I wanted to cover the whole topic of Mongo's indexes in this post, but it turned to be too cumbersome, so I decided to split it into a few posts. In further topics, I'll cover text indexes, hashed indexes, indexing array values, indexing embedded documents and maybe will go somewhere deeper.
If you enjoy this post - please like and share it, I luv it ^__^
UPD. <a href="http://zonov.me/chop-chop-mongodb-leveraging-indexes-power-part-2-multikey-indexes/" target="_blank" rel="noopener">Here</a> you can read my next post about indexes in MongoDB, this time about Multikey indexes.		
