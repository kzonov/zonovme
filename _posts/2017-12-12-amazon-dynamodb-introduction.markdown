---
layout: post
title: Amazon DynamoDB introduction
date: 2017-12-12 09:36:28.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- AWS
- Databases
- DevOps
tags:
- amazon
- aws
- database
- development
- devops
- dynamodb
- mongodb
- nosql
meta:
  _edit_last: '1'
  hefo_before: '0'
  hefo_after: '0'
  _aioseop_description: Features and specifics of Amazon DynamoDB
  _aioseop_title: Amazon DynamoDB introduction
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/amazon-dynamodb-introduction/"
excerpt: "\n\t\t\t\t\t\t"
---
<p>
				This post is the second one in series about Amazon Web Services first steps howtos.</p>
<p>I believe that traditional guides like AWS Certification preparation and Linux Academy don't give the information in proper order, so here I give it in the format and the way how I give it to my colleagues at Babbel.</p>
<p>This post gives you an introduction to the DynamoDB and prepares a ground for the next practical lesson.</p>
<p><!--more--></p>
<p>DynamoDB history starts in 2009 when the initial paper about proposed structure for the new database, which can handle Amazon requirements had been created. But it took three years, to form it to production ready product. So it first had been released back in 2012.</p>
<p>It's a NoSQL database, looks a bit similar to the MongoDB, but actually has not so much in common.</p>
<h2>Features</h2>
<p>The first couple of words are about naming convention:</p>
<p>In DynamoDB you operate with terms "table", "item", "attribute". In MongoDB they are "collection", "document", "field".</p>
<p>I have few blog posts about MongoDB indexes, feel free to <a href="http://zonov.me/chop-chop-mongodb-leveraging-indexes-power-part-1/" target="_blank" rel="noopener">check 'em out</a>.</p>
<p>Next, I want to talk about some cool features you have when using the DynamoDB.</p>
<h3>1. TTL</h3>
<p>In DynamoDB you can turn on the Time to Live mode for a specific table. It means that record, which you put to the table, will be removed after some time. It is really useful if you store data, which will become stale after some period of time. So with it you can reduce your "disk" usage space.</p>
<h3>2. Scalability</h3>
<p>In the previous paragraph, I mentioned "disk" space. You may not worry about it too much. You actually have no space limit here. If your data doesn't fit your current capacity - it just will be auto-scaled.</p>
<h3>3. Replication</h3>
<p>By default Amazon stores three replicas of your DB in different Availability Zones inside one Region.</p>
<h3>4. Manual read consistency select</h3>
<p>When you perform a get request, you may specify, which level of consistency do you want. If it's a strong consistency - you ensure that you have all your distributed DB servers in the same state for this item/table and it will be retrieved. But it will require more read capacity, so it will either slow down other requests or require you to buy more read capacity units. If it's an eventual consistency (default mode) - 100% data consistency is very probable, but not guaranteed.</p>
<h3>5. DynamoDB Accelerator</h3>
<p>It is named DAX. It can store latency-critical part of your table in-memory cache (actually also in a distributed manner). It can be useful for real-time bidding, trading and other apps, where you must have almost no latency. It's a brand new technology in AWS, so feel free to try it, but keep in mind, that it goes with an additional pricing.</p>
<p>It's not a comprehensive list of DynamoDB features, but let's move forward.</p>
<h2>DynamoDB Streams</h2>
<p>Our second hands-on lesson will be about event triggering, and it's actually implemented using DynamoDB Streams.</p>
<p>DynamoDB Stream is a stream of events, happened in your table. For instance, update an item, remove an item, add an item operations. For me, it looks like DynamoDB Streams is just another implementation of messaging queue, but tightly coupled and build especially for the DynamoDB. Important to know that each event stays in a Stream for 24 hours and that their order is guaranteed.</p>
<p>Also, you may use AWS Kinesis for DB events delivery, if you prefer. It might be useful if you have your data in multiple Amazon DB services and you want to have a single pipeline for them.</p>
<p>DynamoDB Streams are really useful when you want your app to be subscribed to some events from the DB, like some microservice should listen to it and react in some way. Or you want to have an online machine learning. Or if you have an AWS Lambda function which should be triggered by specific actions. I already touched AWS Lambdas topic in the <a href="http://zonov.me/amazon-lambda-api-gateway-introduction/" target="_blank" rel="noopener">previous post</a>, if you miss it - add it to your Pocket!</p>
<h2>Primary key</h2>
<p>DynamoDB offers two types of primary keys: partition key or <strong>partition key</strong> + <strong>sort key</strong>.</p>
<h3>Partition key</h3>
<p>It is a normal primary key as you get used to. One unique field, that's it. This field is used as an input for hashing function, which decides in which distributed partition to put a specific record. This is the default approach when you create a new DynamoDB table.</p>
<h3>Partition key + sort key</h3>
<p>Another name of it is a "composed key", you may also face it in other databases. In DynamoDB you can choose this approach with just one checkbox when you create a new table. As you already get, this type consists of two keys. One <strong>partition key</strong>, which now doesn't have to be unique, and a <strong>sort key</strong>. And this pair should be unique. Because hash function still use only <strong>partition key</strong> to decide, on which partition to put the item, but items on each partition are sorted by <strong>sort key</strong>. And on physical level DynamoDB actually store your data close to each other for the same <strong>partition key</strong>, and when you search for smth, it searches first for the <strong>partition key</strong>, and only then in results for a specified <strong>sort key</strong>.</p>
<p>&nbsp;</p>
<p>This is it for now. DynamoDB is a really powerful tool and there is almost no reason why not to use it if you have your infrastructure on AWS and you need to leverage NoSQL database power.</p>
<p>I'll release hands-on introduction on Lambda/DynamoDB integration very soon, bear with me! :) ᕦʕ •ᴥ•ʔᕤ		</p>
