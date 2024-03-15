---
layout: post
title: Golang for Rubyists. Part 6. Structs, Methods and Interfaces
date: 2018-06-10 16:36:57.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Development
tags:
- development
- golang
- ruby
meta:
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  _adinserter_block_exceptions: ''
  hefo_before: '0'
  hefo_after: '0'
  _aioseop_description: The sixth chapter of Golang for Rubyists blog series, in which
    I help the reader to get the hang of statical typing approach to the Polymorphism,
    Interfaces implementation and Structs with methods, which can be applied to them.
  _aioseop_title: Golang for Rubyists. Structs, Methods, Interfaces
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/golang-for-rubyists-part-6-structs-methods-and-interfaces/"
excerpt: "Let the force be with you, my friends. Today we will dive into some of the features, statical typing brings to us, those are Structs and Interfaces."
---

Let the force be with you, my friends. Today we will dive into some of the features, statical typing brings to us, those are Structs and Interfaces.

<img src="{{ site.baseurl }}/assets/2018/06/todd-quackenbush-701-unsplash-1024x832.jpg" alt="Photo by Todd Quackenbush on Unsplash" width="640" height="520" class="aligncenter size-large wp-image-636" />

<h2>Structs</h2>
As I aim my series of articles to those, who has mostly scripting languages background, such as Ruby, Python, JS, I would expect that you have a very brief understanding what those things are. In the best case, you remember that words from your Computer Science study in a university. Let's jump right into the code and take a look at some Struct definition example:

{% highlight go %}
package main
import (
	"fmt"
)
type User struct {
    name   string
    email  string
}
func main() {
	u := User{name: "Max", email: "maxmustermann@fake.com"}
	fmt.Println("Hello,", u.name)
}
{% endhighlight %}

(<a href="https://play.golang.org/p/FEQVAtNYig7" target="_blank" rel="noopener">https://play.golang.org/p/FEQVAtNYig7</a>)

Doesn't it look similar to:
{% highlight ruby %}
class User
  attr_accessor :name
  attr_accessor :email
end
u = User.new(name: "Max", email: "maxmustermann@fake.com")
p "Hello, #{u.name}"
{% endhighlight %}

By me, it looks pretty much. But as you may know, Ruby also has structs. Just because it is not statically typed we have two different structs, OpenStruct and Struct. And even the second one is still not as strict as Golang's one, because it doesn't have a type check. Anyway, the same snippet using Ruby's Struct would look like this:
{% highlight ruby %}
User = Struct.new(:name, :email)
u = User.new(name: "Max", email: "maxmustermann@fake.com")
p "Hello, #{u.name}"
{% endhighlight %}

Uh, I love Ruby's expressiveness and readability.
Go is quite more verbose, but as you may notice, it is pretty readable as well.
What else should we add to understand structs? I think it should be clear enough from the example. Struct is basically a combination of one or more named fields with specified types. Fields can be accessed using dot (.) for both reading and writing.
<h2>Methods</h2>
Let's take a look at one more Ruby example, which uses Struct:
{% highlight ruby %}
User = Struct.new(:first_name, :last_name) do
  def full_name
    "#{first_name} #{last_name}"
  end
end
{% endhighlight %}

How to implement something similar in Golang? Easy-peasy!

{% highlight go %}
package main
import (
	"fmt"
)
type User struct {
    first_name   string
    last_name  string
}
func (u *User) full_name() string {
	return fmt.Sprintf("%s %s", u.first_name, u.last_name)
}
func main() {
	u := User{first_name: "Max", last_name: "Mustermann"}
	fmt.Println("Hello,", u.full_name())
}
{% endhighlight %}

(<a href="https://play.golang.org/p/RTaUjMnUXF6" target="_blank" rel="noopener">https://play.golang.org/p/RTaUjMnUXF6</a>)

Could you ever imagine it? It looks exactly the same as Ruby's methods. And even the name is exactly the same, Methods! So, the method in Golang is a function, which related to some specific Struct and can be applied only to it (only? hmmmm)

<h2>Interfaces</h2>
Do you remember, what polymorphism is? Wiki says:
<code>Polymorphism - the provision of a single interface to entities of different types</code>.
And how do we achieve it with Ruby? Eh, actually multiple ways, <a href="https://robots.thoughtbot.com/back-to-basics-polymorphism-and-ruby" rel="noopener" target="_blank">this Thoughtbot's article</a> perfectly describes them. But in general case, Ruby has a duck typing system, so let's take a look on the following example:

{% highlight ruby %}
class Dog
  attr_reader :hungry
  def initialize
    @hungry = true
  end
  def feed!
    p 'Making loud chewing noise'
    @hungry = false
  end
  def bard
    p 'bark'
  end
end
class Cat
  attr_reader :hungry
  def initialize
    @hungry = true
  end
  def feed!
    @hungry = false
  end
  def meow
    p 'meow'
  end
end
class AutoFeeder
  def self.feed_animal(animal)
    animal.feed!
  end
end
dog = Dog.new
p dog.hungry
AutoFeeder.feed(dog)
p dog.hungry
{% endhighlight %}

We have a class AutoFeeder which method expects something, which can be feeden (so, something, which implements interface "feed"). And since we're in Ruby, we don't have type checks, so we can just pass any object into this method and it either will perform the feeding operation or will fail with famous NoMethodError. And how can we achieve the same in Golang? As you remember, you should always specify a type for a function, so this snippet will work out:

{% highlight go %}
package main
import (
	"fmt"
)
type Dog struct {
	hungry bool
}
type Cat struct {
	hungry bool
}
func (d *Dog) feed() bool {
	fmt.Println("Making loud chewing noise")
	d.hungry = false
	return d.hungry
}
func (c *Cat) feed() bool {
	c.hungry = false
	return c.hungry
}
func feed_animal(d *Dog) {
	d.feed()
}
func main() {
	myFluffy := Dog{hungry: true}
	fmt.Println(myFluffy.hungry)
	feed_animal(&myFluffy)
	fmt.Println(myFluffy.hungry)
}
{% endhighlight %}

But how to make it work for my poor hungry kitten as well? Can we just pass my cat into feed_animal function?

{% highlight go %}
...
func main() {
	myHungryKitten := Cat{hungry: true}
	fmt.Println(myHungryKitten.hungry)
	feed_animal(&myHungryKitten)
	fmt.Println(myHungryKitten.hungry)
}
{% endhighlight %}

Ah, c'mon, ruthless static typing! <code>prog.go:33:14: cannot use &myHungryKitten (type *Cat) as type *Dog in argument to feed_animal</code>
Thank gods, we have Interfaces! Let's first take a look onto the example and then I will explain everything.

{% highlight go %}
package main
import (
	"fmt"
)
type Dog struct {
	hungry bool
}
type Cat struct {
	hungry bool
}
func (d *Dog) feed() bool {
	fmt.Println("Making loud chewing noise")
	d.hungry = false
	return d.hungry
}
func (c *Cat) feed() bool {
	c.hungry = false
	return c.hungry
}
type Animal interface {
	feed() bool
}
func feed_animal(a Animal) {
	a.feed()
}
func main() {
	myHungryKitten := Cat{hungry: true}
	fmt.Println("Feeding my kitty")
	feed_animal(&myHungryKitten)
	fmt.Println(myHungryKitten.hungry)
	myFluffy := Dog{hungry: true}
	fmt.Println("Feeding my puppy")
	feed_animal(&myFluffy)
	fmt.Println(myFluffy.hungry)
}
{% endhighlight %}

Just like that! Now both my pets are full and happy now, many thanks to Interfaces! So, I changed to things here.
Firstly I added an interface, named Animal. You see the syntax is very similar to the way you describe structs. But instead of defining attributes, you specify, which methods should the particular struct implement, in order to comply with this interface.
The second thing I changed is now <code>feed_animal</code> expects not a Dog pointer, but some struct, which implements the interface Animal.
(<a href="https://play.golang.org/p/8NlLiCZDr-R" target="_blank" rel="noopener">https://play.golang.org/p/8NlLiCZDr-R</a>)

Few important things to mention about Interfaces in Golang.
1. Structs don't have to explicitly specify, that they implement some interface. It is checked implicitly by the interface itself.
2. One struct may implement multiple interfaces.
Actually, interfaces topic is deeper than what I described in this article, but this information should be totally enough to understand them and to start using it.
Also, I hope methods and structs are clear for you now as well. Feel free to ask questions in the comments section or approach me via Twitter. Or even <a href="https://ko-fi.com/I2I2DEDW" rel="noopener" target="_blank">buy me a coffee</a> ^__^
Good luck in your endless but immersive learning path!
