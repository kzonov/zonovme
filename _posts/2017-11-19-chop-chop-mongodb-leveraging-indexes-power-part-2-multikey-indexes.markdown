---
layout: post
title: Chop-chop, MongoDB! Leveraging indexes power. Part 2. Multikey indexes
date: 2017-11-19 19:09:29.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Databases
- Development
tags:
- database
- development
- indexes
- mongo
- mongodb
- performance
meta:
  _edit_last: '1'
  _aioseop_description: How to use Multikey indexes in MongoDB. Advantages, restrictions
    and howto.
  _aioseop_title: Chop-chop, MongoDB! Leveraging indexes power. Part 2. Multikey indexes
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/chop-chop-mongodb-leveraging-indexes-power-part-2-multikey-indexes/"
excerpt: "Hello boys and girls, looking forward to know more about MongoDB indexes?
Today we'll talk about Multikey indexes. Yeah, only about them because it's quite a big topic. I also wanted to cover text indexes, but they are too cool to talk about them in the same post, they deserve their own %)"
---

Hello boys and girls, looking forward to know more about MongoDB indexes?
Today we'll talk about Multikey indexes. Yeah, only about them because it's quite a big topic. I also wanted to cover text indexes, but they are too cool to talk about them in the same post, they deserve their own %)
So let's start!
<!--more-->
First of all if you didn't read my previous post - it's also a useful one, check it out: <a href="http://zonov.me/chop-chop-mongodb-leveraging-indexes-power-part-1/">Chop-chop, MongoDB! Leveraging indexes power. Part 1</a>
So, back to the topic. Both MongoDB and PostgreSQL have array data type. And no doubts that both have indexes for them. For PostgreSQL the index type is GIN, for MongoDB it's Multikey.
<h3>Basic idea</h3>
Basically, to understand the idea and mechanics of the Multikey Index you may just read the first block of text in the official MongoDB's docs:
"To index a field that holds an array value, MongoDB creates an index key for each element in the array. These multikey indexes support efficient queries against array fields. Multikey indexes can be constructed over arrays that hold both scalar values (e.g. strings, numbers) and nested documents."
The most important thing I suggest you think about is that it really creates a new index key for each element. It means that this index can cause explosive index growth, so admonition for you, think before using arrays in general and indexes for them in particular. It's not something that can kill your database in a moment, but bigger index - more RAM needed. MongoDB has a strategy to keep your RAM less cluttered, it stores only last or most readable elements of your index in the RAM, but AFAIK it works only for numerical indexes, but not for a Multikey. Cannot tell you with 100% confidence, this topic is scarcely covered in both official docs and blogposts, also hardly discoverable by experience.
As usual with Mongo good thing here is that you don't need to explicitly specify the index type. If your field contains an array, the Multikey index will be created. Even if it's a compounded index.
<h3>Constraints</h3>
You know you can create unique indexes, do you? I assume yes. Only one thing to notice here. If you create a unique index for an array value - it doesn't validate your array to contain only unique values, it just requires that your array won't hold the same values as others, here is an example:
First, create a unique index for a collection of posts.
<code>db.posts.createIndex({tags: 1}, { unique: true })</code>
Then add a new document:
<code>db.posts.insert({tags: ["databases", "devops", "software"]})</code>
Then if we try to add one more - it will complain:
<code>db.posts.insert({tags: ["databases", "devops"]})</code>
<code>E11000 duplicate key error collection: local.posts index: tags_1 dup key: { : "databases" }</code>
But if you try to add a document with duplicating tags, Mongo allows you:
<code>db.posts.insert({tags: ["ruby", "ruby"]})</code>
Another limitation is that you cannot create a compounded index for more than one array field. You can combine array field with others, but not with another array field.
<h3>Covered queries</h3>
There is a term, "covered query", which means that your query fully covered with the index. F.e. you have posts, which have a title and let's assume, amount of characters and you have a compounded index for both these fields. And if you make a query
<code>db.posts.find({title: "post 1"}, { "title": 1, "characters": 1 })</code>
You will end up using the only index, without trying to read anything from the DB itself, which is a pure speed! But if you add <code>tags</code>:
<code>db.posts.find({title: "post 1"}, { "title": 1, "characters": 1, "tags": 1 })</code>
the magic will be gone, even if you have this field indexed. The reason is that covered query is used only when you retrieve a document which lays completely in the index entry. But as you remember, Multikey index lays in multiple - it creates an index entry for each element in the array.
That's it. There are some more things to share about Multikey indexes, but here I covered the most important which can make you feel at ease using them.
All the best, see you in about two weeks!		
