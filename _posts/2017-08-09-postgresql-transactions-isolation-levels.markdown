---
layout: post
title: PostgreSQL transactions Isolation levels
date: 2017-08-09 18:35:13.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Development
tags:
- database
- postgresql
- sql
meta:
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  hefo_before: '0'
  hefo_after: '0'
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/postgresql-transactions-isolation-levels/"
excerpt: "A few days ago I read Joe Nelson's post about DB's transactions anomalies and Postgres's isolation levels to prevent them."
---

A few days ago I read <a href="https://begriffs.com/posts/2017-08-01-practical-guide-sql-isolation.html" target="_blank" rel="noopener">Joe Nelson's post</a> about DB's transactions anomalies and Postgres's isolation levels to prevent them.
I found it very useful and descriptive, so I decided to make a short summary of the information he showed there for those who tl;dr.
<!--more-->
<table cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td valign="top"><span style="font-family: Helvetica; font-size: small;"><b>Anomaly</b></span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;"><b>Description</b></span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;"><b>Serializable Level</b></span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;"><b>Snapshot Isolation Level</b></span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;"><b>Read Committed Level</b></span></td>
</tr>
<tr>
<td valign="top"><span style="font-family: Helvetica; font-size: small;"><b>Dirty Writes</b></span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Transaction T1 modifies an item, T2 further modifies it before T1 commits or rolls back.</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Not possible</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Not possible</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Not possible</span></td>
</tr>
<tr>
<td valign="top"><span style="font-family: Helvetica; font-size: small;"><b>Dirty Reads</b></span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">A transaction reads data written by a concurrent uncommitted transaction.</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Not possible</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Not possible</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Not possible</span></td>
</tr>
<tr>
<td valign="top"><span style="font-family: Helvetica; font-size: small;"><b>Read Skew</b></span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">A transaction re-reads data it has previously read and finds that data has been modified by another transaction. Requires two reads.</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Not possible</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Not possible</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Possible</span></td>
</tr>
<tr>
<td valign="top"><span style="font-family: Helvetica; font-size: small;"><b>Phantom Reads</b></span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">The same as Read Skew but reads a collection instead of just one row.</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Not possible</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Not possible</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Possible</span></td>
</tr>
<tr>
<td valign="top"><span style="font-family: Helvetica; font-size: small;"><b>Lost Update</b></span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">T1 reads an item. T2 updates it. T1 updates it based on what it read than commits. T2’s update has been lost.</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Not possible</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Not possible</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Possible</span></td>
</tr>
<tr>
<td valign="top"><span style="font-family: Helvetica; font-size: small;"><b>Write Skew</b></span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Two concurrent transactions each determine what they are writing based on reading a data set which overlaps what the other is writing.</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Not possible</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Possible</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Possible</span></td>
</tr>
<tr>
<td valign="top"><span style="font-family: Helvetica; font-size: small;"><b>Read-Only Serialization</b></span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">T2 reads a collection. T1 reads it and then updates it. T2 reads it based on previously read version. T3 reads it after T1 committed.</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Almost impossible (less than 0.01%)</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Possible</span></td>
<td valign="top"><span style="font-family: Helvetica; font-size: small;">Possible</span></td>
</tr>
</tbody>
</table>
 
So you can use these three isolation levels in Postgres: Serializable, Snapshot isolation, Read Commited. Here are reasons why you can choose one of those.
<ol>
<li>Serializable. You can be sure that no anomaly will affect your transactions because it's the most restrictive level. Basically no anomaly except Read-Only Serialization one, but even this one can occur very rarely. Keep in mind that it slows down your transactions because you need to put and release collection locks. It's the default one and definitely should be used when you work with the money.</li>
<li>Snapshot isolation level. Aka Repeatable Read level. It will work faster because it will not check changes of concurrent transactions before it commits. It's still proper isolation level, but your app should retry write transaction in case if write skew appeared. But it's still safe for read transactions.</li>
<li>Read committed level. Default isolation level for PostgreSQL. Suitable for the most usual use cases, but you should consider another isolation level if you work with complex transactions. Here is the example from the PostgreSQL wiki:
{% highlight sql %}
BEGIN;
UPDATE website SET hits = hits + 1;
-- run from another session: DELETE FROM website WHERE hits = 10;
COMMIT;
{% endhighlight %}
In this example delete transaction will not see the websites which had 9 before the update transaction started to perform.</li>
</ol>
So if you see that in your app you have some concurrency phenomena, consider looking deeper in DB's isolation level docs and choosing another level.
Here are posts I used and suggest you to read to understand this topic deeper:
<ol>
<li><a href="https://begriffs.com/posts/2017-08-01-practical-guide-sql-isolation.html" target="_blank" rel="noopener">Original post about concurrency phenomena and DB isolation levels.</a></li>
<li><a href="https://www.postgresql.org/docs/9.1/static/transaction-iso.html" target="_blank" rel="noopener">Official docs of PostgreSQL.</a></li>
<li><a href="https://johann.schleier-smith.com/blog/2016/01/06/analyzing-a-read-only-transaction-anomaly-under-snapshot-isolation.html" target="_blank" rel="noopener">Analyze of Read-Only Serialization anomaly.</a></li>
<li><a href="https://www.pythian.com/blog/understanding-mysql-isolation-levels-repeatable-read/" target="_blank" rel="noopener">Repeatable-Read isolation level in MySQL.</a></li>
</ol>
