---
layout: post
title: Developing a database. Part 1. Why may you need indexes and how does Hash index
  work.
date: 2018-02-10 15:10:31.000000000 +01:00
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
- database
- development
- devops
- mongodb
- nosql
- ruby
meta:
  _edit_last: '1'
  _adinserter_block_exceptions: ''
  hefo_before: '0'
  hefo_after: '0'
  _syntaxhighlighter_encoded: '1'
  _aioseop_description: In this article I'm creating a basic database from scratch
    using Ruby to illustrate, why an index may be needed and how does Hash index,
    which exists in such databases as MongoDB and PostgreSQL, works.
  _aioseop_title: Developing a database. Part 1. Why may you need indexes and how
    does Hash index work.
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/developing-a-database-part-1-why-may-you-need-indexes-and-how-does-hash-index-work/"
excerpt: "\n\t\t\t\t\t\t"
---
<p>
				I plan to write a series of posts about databases internals. In order to make it easily perceivable, I'll be writing a NoSQL DB from scratch in Ruby. No doubts that it's not the best fit for database development, but it's extremely readable and will help us a lot. This one will be about why may you want to have an index and what is a Hash index.</p>
<p>UPD. I decided to not continue this series because it takes too much effort to investigate deep enough to explain, but it had got much fewer views and likes than more applicable ones. Probably will return to this topic once, but not now.<br />
<!--more--></p>
<p>Let's imagine an early-age database, where all the data is stored in smth like CSV sheet or JSON.</p>
<p>[javascript][<br />
  { name: &quot;Berlin&quot;, zip-from: 10115, zip-to: 14199 },<br />
  { name: &quot;Munich&quot;, zip-from: 80000, zip-to: 80999 },<br />
  { name: &quot;Hamburg&quot;, zip-from: 20000, zip-to: 20999 }<br />
  ...<br />
  {}<br />
]</p>
<p>[/javascript]</p>
<p>In this database, we don't have such thing as separate collections or tables. One database - one dataset. Let's make this assumption for now. And we will have to make a lot of trade-offs in the future as well %)<br />
So, you are working in post office department and want to find an index range of city with the name "Hamburg". What would you do? Yup, you need to iterate through each of the records and compare the name with the needed value. And as you may see, our dataset is not even sorted by alphabet.</p>
<p>It requires 3 comparisons in the given case, but in Germany, you have few hundreds cities and villages, and if your DB is global - it's millions. So in the worst case, you have to iterate through each one of them. It's what we call O(n), which is not the worst algorithm complexity, but for sure not the optimal one. We would like to have smth O(log(n)), which could give us good performance on the big amount of records.<br />
So what the databases did to give you such a possibility? Yup, they created INDEXES!<br />
Index is an additional structure that based on main DB data. F.e. you can tell that contents section of a book is an index.</p>
<p>And more of that, there is an index which can give us about O(1) complexity! And its name is Hash index.<br />
As a gut feeling, we might think that indexes are silver bullets that can solve all our performance issues with the databases, are they?<br />
Here is the list of database operations: add and read a record. I don't add delete for now.<br />
And here is the code of our database. It does not persist any data on the disk so far.</p>
<p>[ruby]class Database<br />
  def initialize<br />
    @storage = []<br />
  end</p>
<p>  def add_record(record)<br />
    @storage &lt;&lt; record<br />
  end</p>
<p>  def find_by(field, value)<br />
    @storage.each_with_index do |record, i|<br />
      next if record[field] != value<br />
      p &quot;Required to iterate through #{i + 1} records&quot;<br />
      return record<br />
    end<br />
  end<br />
end</p>
<p>[/ruby]</p>
<p>I expect that the code is easily understandable for those who can read any programming language. Our database instance will have a collection of records. Add_record method is just adding an object to the collection. Find_by method takes the attribute name and attribute's value to search for. Then iterates through the whole collection in order to find the needed one. I didn't add update and delete methods so far because they aren't needed for this index illustration.<br />
And here is my script, I'll be using to access The Database:</p>
<p>[ruby]require './database'</p>
<p>d = Database.new</p>
<p>d.add_record({ name: &quot;Berlin&quot;, zip_from: 10115, zip_to: 14199 })<br />
d.add_record({ name: &quot;Munich&quot;, zip_from: 80000, zip_to: 80999 })<br />
d.add_record({ name: &quot;Hamburg&quot;, zip_from: 20000, zip_to: 20999 })<br />
d.add_record({ name: &quot;Dresden&quot;, zip_from: 01067, zip_to: 01327 })<br />
d.add_record({ name: &quot;Leipzig&quot;, zip_from: 04001, zip_to: 04357 })<br />
d.add_record({ name: &quot;Magdeburg&quot;, zip_from: 39104, zip_to: 39130 })<br />
d.add_record({ name: &quot;Potsdam&quot;, zip_from: 14401, zip_to: 14482 })<br />
d.add_record({ name: &quot;Frankfurt (Oder)&quot;, zip_from: 15230, zip_to: 15236 })<br />
d.add_record({ name: &quot;Dortmund&quot;, zip_from: 44135, zip_to: 44388 })</p>
<p>[/ruby]</p>
<p>and now if we try to search for Dortmund in this table, here is what we'll get:</p>
<p>[ruby][33] pry(main)&gt; d.find_by(:name, &quot;Dortmund&quot;)<br />
&quot;Required to iterate through 9 records&quot;<br />
=&gt; {:name=&gt;&quot;Dortmund&quot;, :zip_from=&gt;44135, :zip_to=&gt;44388}</p>
<p>[/ruby]</p>
<p>Yeah.... It's time to start thinking about indexes.<br />
First one we will discuss is a Hash index, which is based on Hashmap algorithm, which you may know from some algorithmic source of knowledge (university/book/whatever). Let's build this index for the name field. We will use MD5 hashing function. It is the one, which is used by MongoDB. For sure we can use smth much simpler, even</p>
<p>[ruby]def hash(value)<br />
  value.size<br />
end</p>
<p>[/ruby]</p>
<p>which will give us hash keys based on the string size. But it will lead to plenty of collisions, which will decrease the index performance. On the other side, MD5 is quite fast and generates diverse index values, so it's a good choice.</p>
<p>[ruby]<br />
class Database<br />
  # hash_index is a hash, because we may want to have indexes on multiple columns<br />
  def initialize<br />
    @storage = []<br />
    @hash_index = {}<br />
  end</p>
<p>  # Since our DB everytime adds the record to the end and it's single-threaded,<br />
  #  we can be sure that `@storage.size - 1` is current element's index (pointer).<br />
  #  So we put it into our index structure.<br />
  def add_record(record)<br />
    @storage &lt;&lt; record<br />
    update_hash_index(record, (@storage.size - 1))<br />
  end</p>
<p>  def find_by(field, value)<br />
    return find_in_index(field, value) if indexed?(field)<br />
    find_iteratively(field, value)<br />
  end</p>
<p>  private</p>
<p>  # The default field for indexing will be :name<br />
  def update_hash_index(record, index, field = :name)<br />
    @hash_index[field] = {} unless @hash_index[field]<br />
    @hash_index[field].merge!(hashify(record[field]) =&gt; index)<br />
  end</p>
<p>  # We check if the DB has an index on this field. This method doesn't check<br />
  #  do we have this record indexed or not.<br />
  def indexed?(field)<br />
    @hash_index.key?(field)<br />
  end</p>
<p>  # We get an index (pointer) from the index,<br />
  #  which then use to get a record from the DB collection<br />
  def find_in_index(field, value)<br />
    @storage[@hash_index[field][hashify(value)]]<br />
  end</p>
<p>  def find_iteratively(field, value)<br />
    @storage.each_with_index do |record, i|<br />
      next if record[field] != value<br />
      p &quot;Required to iterate through #{i + 1} records&quot;<br />
      return record<br />
    end<br />
  end</p>
<p>  # We're using MD5 for hashing<br />
  def hashify(value)<br />
    Digest::MD5.hexdigest value<br />
  end<br />
end</p>
<p>[/ruby]</p>
<p>And this time when you search for Dortmund, you won't need to iterate through the whole collection:</p>
<p>[ruby][15] pry(main)&gt; d.find_by(:name, &quot;Dortmund&quot;)<br />
=&gt; {:name=&gt;&quot;Dortmund&quot;, :zip_from=&gt;44135, :zip_to=&gt;44388}</p>
<p>[/ruby]</p>
<p>And here is how my index looks like:</p>
<p>[ruby]@hash_index = {<br />
  name: {<br />
    &quot;ee1611b61f5688e70c12b40684dbb395&quot;=&gt;0,<br />
    &quot;7b88a4aca50f33c258efc438d098c9f4&quot;=&gt;1,<br />
    &quot;35d7df6ed3d93be2927d14acc5f1fc9a&quot;=&gt;2,<br />
    &quot;19ed5f8ceab486afbee6b3ba5c88884d&quot;=&gt;3,<br />
    &quot;4a887b8a68acf9b04d9d027bddedb06b&quot;=&gt;4,<br />
    &quot;59aceadf846f772736c4b40eee7b155d&quot;=&gt;5,<br />
    &quot;d5404511bc77d6d86d8769e15ef1b679&quot;=&gt;6,<br />
    &quot;4bb6d2e87c2100a9a30f3895db22178a&quot;=&gt;7,<br />
    &quot;2728451089b9149bad7b106a792e51f9&quot;=&gt;8<br />
  }<br />
}</p>
<p>[/ruby]</p>
<p>This listing is more verbose, but still can be recognized without long thinking process. But since it is more verbose, I added inline comments for you to have a better understanding.<br />
In my example it may look a bit weird because usually your database is located not in the memory, but on the disk, so in a real world you would store not the index of an element, but it's offset in the file on the disk.<br />
Remember, I mentioned possible collisions, which may happen if your hashing function is too simple? Here, on <a href="https://en.wikipedia.org/wiki/Hash_table#Collision_resolution" target="_blank" rel="noopener">the wiki page</a>, you can read more about it. It's not a desease, but still, it's a problem you would have to solve.</p>
<p>As you may found, now each time we change our data, we should change the index as well. This is what I meant when was telling that the index is not an all-problem-solver. Maintaining additional data structure is the additional overhead you imminently have using indexes. So well-chosen indexes will speed up your Read requests, but slow down Writes. That's why indexing is not an automated operation, so databases don't index everything by default, but instead allows you as a developer/administrator to choose which fields you want to indexes using which index type. To be absolutely honest with you, I have to mention, that most of the databases create one, primary index for you. Automatically.<br />
Another thing you should keep in mind when adding an index is that it takes an additional disk space, so always think twice before add another one.</p>
<p>Thank you for reading! If this post will be somehow popular, I'll write more on this topic. This time about B-Tree indexes, compounded indexes and SSTables.<br />
Be aware of your app persistence layer!		</p>
