---
layout: post
title: Golang for Rubyists. Part 3. Ampersands and asterisks. Pointers and references.
date: 2018-04-24 22:15:55.000000000 +02:00
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
- tutorial
meta:
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  _adinserter_block_exceptions: ''
  hefo_before: '0'
  hefo_after: '0'
  _aioseop_description: Pointers and references in Golang from the point of perspective
    of Ruby developer. What the heck are ampersands and asterisks?
  _aioseop_title: Golang for Rubyists. Ampersands and asterisks. Pointers and references.
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/golang-for-rubyists-part-3-ampersands-and-asterisks-pointers-and-references/"
excerpt: "Welcome back, my fellow learners.
I still on my way of learning Golang and recently stumbled upon some listing, where I’ve found asterisks and ampersands, which I couldn’t get the hang of and had to google. So I think it is a great topic for the third post in the series."
---

Welcome back, my fellow learners.
I still on my way of learning Golang and recently stumbled upon some listing, where I've found asterisks and ampersands, which I couldn't get the hang of and had to google. So I think it is a great topic for the third post in the series.
<!--more-->
Let's first recall, where can you find asterisks (*) and ampersands (&) in Ruby? Obviously we don't speak about logical or math functions here, it is the same in any (oh, such a broad word, ok, in most of) programming languages. So, asterisks, here is the code, which any Ruby developer understands:
{% highlight go %}
2.2.1 :001 > def meaningful_method(var1, var2, var3)
2.2.1 :002?>   p var1
2.2.1 :003?>   p var2
2.2.1 :004?>   p var3
2.2.1 :005?>   end
 => :meaningful_method
2.2.1 :006 >
2.2.1 :007 >
2.2.1 :008 >   meaningful_method(['a', 'b', 'c'])
ArgumentError: wrong number of arguments (1 for 3)
	from (irb):1:in `meaningful_method'
	from (irb):8
	from /Users/kirillzonov/.rvm/rubies/ruby-2.2.1/bin/irb:11:in `<main>'
2.2.1 :009 >
2.2.1 :010 >   meaningful_method(*['a', 'b', 'c'])
"a"
"b"
"c"
 => "c"
{% endhighlight %}
So, we name this operator "splat", it kinda opens an array. It can work the similar way in method arguments:
{% highlight go %}
2.2.1 :015 >   def another_meaningful_method(*vars)
2.2.1 :016?>     p vars
2.2.1 :017?>   end
 => :another_meaningful_method
2.2.1 :018 >
2.2.1 :019 >
2.2.1 :021 > another_meaningful_method(['a', 'b', 'c'])
[["a", "b", "c"]]
 => [["a", "b", "c"]]
2.2.1 :022 > another_meaningful_method('a', 'b', 'c')
["a", "b", "c"]
 => ["a", "b", "c"]
{% endhighlight %}
We usually use it to pass some additional arguments, amount or existing of which we're not sure about. So, long story short, in Ruby we use asterisk to open an array. Or multiply numbers. Or multiply a number and a string. Or something else. And you can use two asterisks and have a different behaviour. Ok, Ruby is weird sometimes. What about ampersand?
The first thing comes to our mind is double ampersand as a logical operator. Move on, what else?
{% highlight go %}
2.2.1 :026 > [1, 2, 3] & [2, 3, 4]
 => [2, 3]
{% endhighlight %}
Ok, arrays' intersection. Anything else?
{% highlight go %}
2.2.1 :036 >   def even_more_meaningful_method(var1, &block)
2.2.1 :037?>     p var1
2.2.1 :038?>     block.call('bar')
2.2.1 :039?>   end
 => :even_more_meaningful_method
2.2.1 :040 > even_more_meaningful_method('foo') { |block_argument| p block_argument }
"foo"
"bar"
 => "bar"
{% endhighlight %}
Yeah, our beloved blocks. I remember when I was just starting doing Ruby that syntax could blow my mind. So we use ampersand here to tell that the argument we're expecting is a block, so it will be passed in different way than plaun arguments. Cannot recall any other usages of ampersand in Ruby from the top of my head, so let's check out, how does Golang suggest us to use ampersands.
If you have an engineering education, you're most likely have heard about pointers from your C/C++ course. And if you're like me - you've never worked with them in a real life outside of a university. So let's refresh our memory.
We assume that we have a drawer, where there is some object on each shelf. Then you look into one drawer, see that there is an orange and say to your friend, hey dude, remember "an orange". This is a typical variable assignment. You just assigned to your friend's memory value "an orange". If you then will eat this delicious juicy orange and put some disgusting thing like broccoli onto that shelf - your friend will still remember "an orange". BUT. Instead of telling your friend "please remember "an orange", you can tell him, "dude, there is something on the second shelf of that drawer, please remember it". He will remember <b>where is "it" located</b>, not the object itself. And this is a pointer assignment. Probably my real-world-explanation convoluted things just more, so let's simply take a look at the following listing:
{% highlight go %}
package main
import "fmt"
func main() {
	variable1 := 1
	variable2 := 2
	pointer1 := &variable1
	// set pointer1 to address of variable1
	// pointer1 address: 0x10414020
	// pointer1 value:   1
	// variable1 value:  1
	*pointer1 = 3
	// change the value at address &variable1 to 3
	// pointer1 address: 0x10414020
	// pointer1 value:   3
	// variable1 value:  3
	variable1 = 4
	// change the value of variable1 to 4
	// pointer1 address: 0x10414020
	// pointer1 value:   4
	// variable1 value:  4
	pointer1 = &variable2
	// set pointer1 to the address of variable2
	// pointer1 address: 0x10414024
	// pointer1 value:   2
	// variable1 value:  4
	// variable2 value:  2
	pointer2 := pointer1
	// set pointer2 to the address in pointer1
	// pointer1 address:  0x10414024
	// pointer1 value:    2
	// pointer2 address: 0x10414024
	// pointer2 value:   2
	// variable1 value:  4
	// variable2 value:  2
	*pointer1 = 5
	// change the value at the address &variable2 to 5
	// pointer1 address:  0x10414024
	// pointer1 value:    5
	// pointer2 address: 0x10414024
	// pointer2 value:   5
	// variable1 value:  4
	// variable2 value:  5
	pointer1 = &variable1
	// change pointer1 to address of variable1
	// pointer1 address:  0x10414020
	// pointer1 value:    4
	// pointer2 address: 0x10414024
	// pointer2 value:   5
	// variable1 value:  4
	// variable2 value:  5
}
{% endhighlight %}
This code may look cumbersome but just because of comments, don't be afraid, just go through it without hurry.
Long story short, if you assign some variable to another variable, it will copy its value.
{% highlight go %}
2.2.1 :043 >   a = 1
 => 1
2.2.1 :044 > b = a
 => 1
2.2.1 :045 > a = 2
 => 2
2.2.1 :046 > b
 => 1
{% endhighlight %}
If you assign an memory address of some variable (pointer) to another variable, it will be pointing onto the same memory cell. And if you change the value of the original variable and then will try to access it from a second variable - you will get an updated value.
And as you all know, in Ruby you cannot operate with pointers. And it is pass-by-value language (sarcasm). Let's make a last small experiment to illustrate it:
{% highlight go %}
2.2.1 :054 >   a = [1, 2, 3]
 => [1, 2, 3]
2.2.1 :055 > b = a
 => [1, 2, 3]
2.2.1 :056 > b << 42
 => [1, 2, 3, 42]
2.2.1 :057 > a
 => [1, 2, 3, 42]
{% endhighlight %}

<img class="size-large" src="{{ site.baseurl }}/assets/2018/04/85afd1ebc17194788d7581af4cf1a29f7dfc7b08b922f50dbc67bd64f08fa370.jpg" alt="Wait a minute" width="604" height="453" />

Ok, that's another story, however, if you're curious about how Ruby actually works with objects (this is a cue, this array is not just an address in memory, it is an object) and their assignments and passing - I can recommend <a href="https://robertheaton.com/2014/07/22/is-ruby-pass-by-reference-or-pass-by-value/" target="_blank" rel="noopener">this article</a>.
I hope you enjoyed reading this article and get the hang of how you can use pointers in Golang and how working with variables is different from Ruby. See ya!
P.S. If you missed previous posts in this series, here they are, right for you!
<ul>
<li><a href="http://zonov.me/golang-for-rubyists-part-2-go-type-system/" target="_blank" rel="noopener">Golang for Rubyists. Part 2. Go type system</a></li>
<li><a href="http://zonov.me/golang-osx-installation-and-getting-started/" target="_blank" rel="noopener">Golang for Rubyists. Part 1. Go 1.10 OSX installation and getting started</a></li>
</ul>
UPD. Stooop, but what about references and pass by reference, 'cause you mentioned it in the title? Ah, Golang doesn't have that, actually. You can either have a pointer-variable or copy the variable value to a new variable and so to a new memory cell. I can point you to <a href="https://dave.cheney.net/2017/04/29/there-is-no-pass-by-reference-in-go" target="_blank" rel="noopener">this article</a>, where you can find a deeper explanation.		
