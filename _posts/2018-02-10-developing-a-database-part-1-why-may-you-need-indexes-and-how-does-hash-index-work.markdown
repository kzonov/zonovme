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
excerpt: "I plan to write a series of posts about databases internals. In order to make it easily perceivable, I'll be writing a NoSQL DB from scratch in Ruby. No doubts that it's not the best fit for database development, but it's extremely readable and will help us a lot. This one will be about why may you want to have an index and what is a Hash index."
---

I plan to write a series of posts about databases internals. In order to make it easily perceivable, I'll be writing a NoSQL DB from scratch in Ruby. No doubts that it's not the best fit for database development, but it's extremely readable and will help us a lot. This one will be about why may you want to have an index and what is a Hash index.
UPD. I decided to not continue this series because it takes too much effort to investigate deep enough to explain, but it had got much fewer views and likes than more applicable ones. Probably will return to this topic once, but not now.
<!--more-->
Let's imagine an early-age database, where all the data is stored in smth like CSV sheet or JSON.
{% highlight javascript %}[
  { name: "Berlin", zip-from: 10115, zip-to: 14199 },
  { name: "Munich", zip-from: 80000, zip-to: 80999 },
  { name: "Hamburg", zip-from: 20000, zip-to: 20999 }
  ...
  {}
]
{% endhighlight %}
In this database, we don't have such thing as separate collections or tables. One database - one dataset. Let's make this assumption for now. And we will have to make a lot of trade-offs in the future as well %)
So, you are working in post office department and want to find an index range of city with the name "Hamburg". What would you do? Yup, you need to iterate through each of the records and compare the name with the needed value. And as you may see, our dataset is not even sorted by alphabet.
It requires 3 comparisons in the given case, but in Germany, you have few hundreds cities and villages, and if your DB is global - it's millions. So in the worst case, you have to iterate through each one of them. It's what we call O(n), which is not the worst algorithm complexity, but for sure not the optimal one. We would like to have smth O(log(n)), which could give us good performance on the big amount of records.
So what the databases did to give you such a possibility? Yup, they created INDEXES!
Index is an additional structure that based on main DB data. F.e. you can tell that contents section of a book is an index.
And more of that, there is an index which can give us about O(1) complexity! And its name is Hash index.
As a gut feeling, we might think that indexes are silver bullets that can solve all our performance issues with the databases, are they?
Here is the list of database operations: add and read a record. I don't add delete for now.
And here is the code of our database. It does not persist any data on the disk so far.
{% highlight go %}class Database
  def initialize
    @storage = []
  end
  def add_record(record)
    @storage << record
  end
  def find_by(field, value)
    @storage.each_with_index do |record, i|
      next if record[field] != value
      p "Required to iterate through #{i + 1} records"
      return record
    end
  end
end
{% endhighlight %}
I expect that the code is easily understandable for those who can read any programming language. Our database instance will have a collection of records. Add_record method is just adding an object to the collection. Find_by method takes the attribute name and attribute's value to search for. Then iterates through the whole collection in order to find the needed one. I didn't add update and delete methods so far because they aren't needed for this index illustration.
And here is my script, I'll be using to access The Database:
{% highlight go %}require './database'
d = Database.new
d.add_record({ name: "Berlin", zip_from: 10115, zip_to: 14199 })
d.add_record({ name: "Munich", zip_from: 80000, zip_to: 80999 })
d.add_record({ name: "Hamburg", zip_from: 20000, zip_to: 20999 })
d.add_record({ name: "Dresden", zip_from: 01067, zip_to: 01327 })
d.add_record({ name: "Leipzig", zip_from: 04001, zip_to: 04357 })
d.add_record({ name: "Magdeburg", zip_from: 39104, zip_to: 39130 })
d.add_record({ name: "Potsdam", zip_from: 14401, zip_to: 14482 })
d.add_record({ name: "Frankfurt (Oder)", zip_from: 15230, zip_to: 15236 })
d.add_record({ name: "Dortmund", zip_from: 44135, zip_to: 44388 })
{% endhighlight %}
and now if we try to search for Dortmund in this table, here is what we'll get:
{% highlight go %}[33] pry(main)> d.find_by(:name, "Dortmund")
"Required to iterate through 9 records"
=> {:name=>"Dortmund", :zip_from=>44135, :zip_to=>44388}
{% endhighlight %}
Yeah.... It's time to start thinking about indexes.
First one we will discuss is a Hash index, which is based on Hashmap algorithm, which you may know from some algorithmic source of knowledge (university/book/whatever). Let's build this index for the name field. We will use MD5 hashing function. It is the one, which is used by MongoDB. For sure we can use smth much simpler, even
{% highlight go %}def hash(value)
  value.size
end
{% endhighlight %}
which will give us hash keys based on the string size. But it will lead to plenty of collisions, which will decrease the index performance. On the other side, MD5 is quite fast and generates diverse index values, so it's a good choice.
{% highlight go %}
class Database
  # hash_index is a hash, because we may want to have indexes on multiple columns
  def initialize
    @storage = []
    @hash_index = {}
  end
  # Since our DB everytime adds the record to the end and it's single-threaded,
  #  we can be sure that `@storage.size - 1` is current element's index (pointer).
  #  So we put it into our index structure.
  def add_record(record)
    @storage << record
    update_hash_index(record, (@storage.size - 1))
  end
  def find_by(field, value)
    return find_in_index(field, value) if indexed?(field)
    find_iteratively(field, value)
  end
  private
  # The default field for indexing will be :name
  def update_hash_index(record, index, field = :name)
    @hash_index[field] = {} unless @hash_index[field]
    @hash_index[field].merge!(hashify(record[field]) => index)
  end
  # We check if the DB has an index on this field. This method doesn't check
  #  do we have this record indexed or not.
  def indexed?(field)
    @hash_index.key?(field)
  end
  # We get an index (pointer) from the index,
  #  which then use to get a record from the DB collection
  def find_in_index(field, value)
    @storage[@hash_index[field][hashify(value)]]
  end
  def find_iteratively(field, value)
    @storage.each_with_index do |record, i|
      next if record[field] != value
      p "Required to iterate through #{i + 1} records"
      return record
    end
  end
  # We're using MD5 for hashing
  def hashify(value)
    Digest::MD5.hexdigest value
  end
end
{% endhighlight %}
And this time when you search for Dortmund, you won't need to iterate through the whole collection:
{% highlight go %}[15] pry(main)> d.find_by(:name, "Dortmund")
=> {:name=>"Dortmund", :zip_from=>44135, :zip_to=>44388}
{% endhighlight %}
And here is how my index looks like:
{% highlight go %}@hash_index = {
  name: {
    "ee1611b61f5688e70c12b40684dbb395"=>0,
    "7b88a4aca50f33c258efc438d098c9f4"=>1,
    "35d7df6ed3d93be2927d14acc5f1fc9a"=>2,
    "19ed5f8ceab486afbee6b3ba5c88884d"=>3,
    "4a887b8a68acf9b04d9d027bddedb06b"=>4,
    "59aceadf846f772736c4b40eee7b155d"=>5,
    "d5404511bc77d6d86d8769e15ef1b679"=>6,
    "4bb6d2e87c2100a9a30f3895db22178a"=>7,
    "2728451089b9149bad7b106a792e51f9"=>8
  }
}
{% endhighlight %}
This listing is more verbose, but still can be recognized without long thinking process. But since it is more verbose, I added inline comments for you to have a better understanding.
In my example it may look a bit weird because usually your database is located not in the memory, but on the disk, so in a real world you would store not the index of an element, but it's offset in the file on the disk.
Remember, I mentioned possible collisions, which may happen if your hashing function is too simple? Here, on <a href="https://en.wikipedia.org/wiki/Hash_table#Collision_resolution" target="_blank" rel="noopener">the wiki page</a>, you can read more about it. It's not a desease, but still, it's a problem you would have to solve.
As you may found, now each time we change our data, we should change the index as well. This is what I meant when was telling that the index is not an all-problem-solver. Maintaining additional data structure is the additional overhead you imminently have using indexes. So well-chosen indexes will speed up your Read requests, but slow down Writes. That's why indexing is not an automated operation, so databases don't index everything by default, but instead allows you as a developer/administrator to choose which fields you want to indexes using which index type. To be absolutely honest with you, I have to mention, that most of the databases create one, primary index for you. Automatically.
Another thing you should keep in mind when adding an index is that it takes an additional disk space, so always think twice before add another one.
Thank you for reading! If this post will be somehow popular, I'll write more on this topic. This time about B-Tree indexes, compounded indexes and SSTables.
Be aware of your app persistence layer!		
