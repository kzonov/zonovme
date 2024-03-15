---
layout: post
title: Golang for Rubyists. Part 7. Ruby and Golang, methods comparison
date: 2018-06-29 21:23:56.000000000 +02:00
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
- golang arrays
- golang maps
- golang strings
- ruby
meta:
  _edit_last: '1'
  _adinserter_block_exceptions: ''
  hefo_before: '0'
  hefo_after: '0'
  _syntaxhighlighter_encoded: '1'
  _aioseop_description: Comparison of useful methods for Arrays, Maps/Hashes and Strings
    for Ruby and Golang.
  _aioseop_title: Golang for Rubyists. Part 7. Methods comparison of Golang and Ruby
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/golang-for-rubyists-part-7-ruby-and-golang-methods-comparison/"
excerpt: "Hello, my dear friends. We all love Ruby (you too, right?) for its expressiveness and a set of useful methods out of the box. It would be a pleasure if when start using a new language, you had the similar methods or at least familiar ways to achieve same goals. Let’s try to pick a few useful methods from Ruby and find out, are there any equivalents in Golang for them."
---
Hello, my dear friends. We all love Ruby (you too, right?) for its expressiveness and a set of useful methods out of the box. It would be a pleasure if when start using a new language, you had the similar methods or at least familiar ways to achieve same goals. Let's try to pick a few useful methods from Ruby and find out, are there any equivalents in Golang for them.

<img src="{{ site.baseurl }}/assets/2018/06/pablo-hermoso-422530-unsplash-1024x590.jpg" alt="pablo-hermoso-422530-unsplash" width="640" height="369" class="size-large wp-image-654" />

<h2>Array</h2>
Let's start with something simple:
{% highlight ruby %}
2.2.1 :001 > array = [1,3,5,7]
 => [1, 3, 5, 7]
2.2.1 :002 > array.shift
 => 1
2.2.1 :003 > array
 => [3, 5, 7]
2.2.1 :004 > array.push(1)
 => [3, 5, 7, 1]
2.2.1 :005 > array.unshift(array.pop)
 => [1, 3, 5, 7]
{% endhighlight %}
So, we used four methods here: <code>shift, unshift, push, pop</code>. Now let me try to do something similar in Golang:
{% highlight go %}
package main
import ("fmt")
func main() {
	// Initialize the slice
	array := []int{1, 3, 5, 7}
	fmt.Println(array)
	// Shift
	x := array[0]
	fmt.Println(x)
	array = array[1:]
	fmt.Println(array)
	// Push
	array = append(array, x)
	fmt.Println(array)
	// Pop and Unshift
	x, array = array[len(array)-1], array[:len(array)-1]
	array = append([]int{x}, array...)
	fmt.Println(array)
}
{% endhighlight %}
[https://play.golang.org/p/wNgO9LeX514](https://play.golang.org/p/wNgO9LeX514)

Not so short and expressive, huh? But still pretty straightforward. The only confusion I can anticipate is the last example, with pop and unshift. Just to explain, at first we assign to <code>x</code> the last element of an <code>array</code> and we modify the <code>array</code> variable to be everything starting from the element at index 0 to the pre-last one. And at the next line we create a new slice with just <code>x</code> and append an <code>array</code> to it.
Also, you could notice here, that usage of <code>[]</code> to access an array/slice element is exactly the same as in Ruby (or most of the languages).
Let's move on to the more solid examples:

{% highlight ruby %}
2.2.1 :001 > array = [1, 3, 5, 7]
 => [1, 3, 5, 7]
2.2.1 :002 > array.map { |a| a + 1 }
 => [2, 4, 6, 8]
2.2.1 :003 > array
 => [1, 3, 5, 7]
2.2.1 :005 > array.reject { |a| a % 3 == 0}
 => [1, 5, 7]
2.2.1 :006 > array
 => [1, 3, 5, 7]
2.2.1 :007 > array.sample
 => 7
2.2.1 :008 > array
 => [1, 3, 5, 7]
2.2.1 :009 >
2.2.1 :010 > array.min
 => 1
2.2.1 :011 >
2.2.1 :012 > array.max
 => 7
2.2.1 :014 > array.shuffle
 => [5, 7, 3, 1]
{% endhighlight %}

Here we iterate through a collection, without original collection modification, then we <code>reject</code> some value also without a modification, then we get a random element from it, then we have a basic min/max methods and a <code>shuffle</code> method, which shuffles an array elements. Are you optimistic about Golang abilities here? Let it a try! Nope, let me just tell you, that Go is not a functional language. Not at all. Not for an iota. The only way you can achieve map/reduce/filter functionality is by writing <code>for</code> loops. Also, there are no built-in min/max functions, unfortunately. Surprisingly I was able to find a <code>shuffle</code> implementation:
{% highlight go %}
package main
import (
	"fmt"
	"math/rand"
)
func main() {
	// Initialize the slice
	array := []int{1, 3, 5, 7}
	fmt.Println(array)
	rand.Shuffle(len(array), func(i, j int) {
		array[i], array[j] = array[j], array[i]
	})
	fmt.Println(array)
}
{% endhighlight %}
Btw it still uses two <code>for</code> loops under the hood: <a href="https://golang.org/src/math/rand/rand.go#L232" rel="noopener" target="_blank">https://golang.org/src/math/rand/rand.go#L232</a>. Interesting to notice here is that it's possible to pass a function to a method in Golang.
Just to give an impression, here is the possible implementation of our own <code>Filter</code> function in Golang:
{% highlight go %}
package main
import ("fmt")
func main() {
	array := []int{1, 3, 5, 7}
	filteredArray := Filter(array, func(i int) bool {
		return i%3 != 0
	})
	fmt.Println(filteredArray)
}
func Filter(in []int, fn func(int) bool) []int {
	out := make([]int, 0, len(in))
	for i := 0; i < len(in); i++ {
		current := in[i]
		if fn(current) {
			out = append(out, current)
		}
	}
	return out
}
{% endhighlight %}
[https://play.golang.org/p/LFvueXzO-BU](https://play.golang.org/p/LFvueXzO-BU)
Here is a version, using interfaces, more generic, but still far from ideal <a href="https://play.golang.org/p/N9JvUXo7ugq" rel="noopener" target="_blank">https://play.golang.org/p/N9JvUXo7ugq</a>

As you can see, this will work only with slices of integers. In the link above you can find a more generic implementation. It uses reflection mechanism of Golang, which we didn't explore yet.
So, this function may look cumbersome, but let's take a look onto Ruby's <code>map</code> method under the hood:

{% highlight go %}
static VALUE
rb_ary_collect(VALUE ary)
{
    long i;
    VALUE collect;
    RETURN_SIZED_ENUMERATOR(ary, 0, 0, ary_enum_length);
    collect = rb_ary_new2(RARRAY_LEN(ary));
    for (i = 0; i < RARRAY_LEN(ary); i++) {
        rb_ary_push(collect, rb_yield(RARRAY_AREF(ary, i)));
    }
    return collect;
}
{% endhighlight %}
Looks pretty similar, isn't it? Sure, in Ruby we don't work with types - that's the only difference. So, long story short, use <code>for</code> loops, that's the way to go.
But also one thing I want to mention is that there is a more convenient way to iterate through a collection, here how it looks like (works for both maps and arrays):
{% highlight go %}
package main
import ("fmt")
func main() {
	array := []int{1, 3, 5, 7}
	for i := range array {
	  fmt.Println(i * 2)
	}
}
{% endhighlight %}
(<a href="https://play.golang.org/p/6g28EUCvQ6f" rel="noopener" target="_blank">https://play.golang.org/p/6g28EUCvQ6f</a>)
<h2>Hashes</h2>
One of the most heavily used data structures in Ruby is Hash.
Just kiddin, I have no idea, which is the most heavily used one. Let's take a look at these examples, where we apply a couple of useful functions to it:
{% highlight ruby %}
2.2.1 :001 > hash = { foo: 'bar', fizz: 'buzz', berlin: 'rain', munich: 'beer' }
 => {:foo=>"bar", :fizz=>"buzz", :berlin=>"rain", :munich=>"beer"}
2.2.1 :003 >
2.2.1 :004 >   hash.values
 => ["bar", "buzz", "rain", "beer"]
2.2.1 :005 > hash.keys
 => [:foo, :fizz, :berlin, :munich]
2.2.1 :006 >
2.2.1 :008 > hash.values_at(:munich, :berlin)
 , > ["beer", "rain"]
{% endhighlight %}
Nothing magical, just retrieving all values, all keys, and values by multiple keys, should be easy-peasy?
{% highlight go %}
package main
import ("fmt")
func main() {
	hash := map[string]string{"foo": "bar", "fizz": "buzz", "berlin": "rain", "munich": "beer"}
	fmt.Println(hash)
	values := make([]string, 0, len(hash))
	for _, value := range hash {
		values = append(values, value)
	}
	fmt.Println(values)
	keys := make([]string, 0, len(hash))
	for key := range hash {
		keys = append(keys, key)
	}
	fmt.Println(keys)
	valuesAt := []string{hash["munich"], hash["berlin"]}
	fmt.Println(valuesAt)
}
{% endhighlight %}
(<a href="https://play.golang.org/p/YN0xnhly-r9" rel="noopener" target="_blank">https://play.golang.org/p/YN0xnhly-r9</a>)

<img src="{{ site.baseurl }}/assets/2018/06/aww-300x298.png" alt="aww" width="300" height="298" class="aligncenter size-medium wp-image-648" />

I am honestly not sure about the last one, probably there is a way to make it more readable. But anyway, beauty of Go in it's straightforwardness :)
<h2>String</h2>
In Ruby, Strings can operate as arrays in most cases, but here we will pick a few string-specific methods:
{% highlight ruby %}
2.2.1 :001 > str = " Star wars "
 => " Star wars "
2.2.1 :002 > str.upcase
 => " STAR WARS "
2.2.1 :003 > str.downcase
 => " star wars "
2.2.1 :004 > str.strip
 => "Star wars"
{% endhighlight %}
Nothing wow-like for Ruby, boring stuff, but let's check, what Golang has to offer for such basic things:
{% highlight go %}
package main
import (
	"fmt"
	"strings"
)
func main() {
	str := " Star wars "
	fmt.Println(str)
	fmt.Println(strings.ToLower(str))
	fmt.Println(strings.ToUpper(str))
	fmt.Println(strings.TrimSpace(str))
}
{% endhighlight %}
(<a href="https://play.golang.org/p/9GvLs9yFIea" rel="noopener" target="_blank">https://play.golang.org/p/9GvLs9yFIea</a>)
Bang-bang! Such a bliss! Actually, Golang has a pretty extensive set of methods for strings manipulation, you can find way more in their official documentation: <a href="https://golang.org/pkg/strings/" rel="noopener" target="_blank">https://golang.org/pkg/strings/</a>
<h2>Conclusion</h2>
There are much more to discover, so start using Golang for your pet projects or for your actual projects at work, f.e. if you plan to have some simple Lambda function, it's a good place to introduce it, check out <a href="http://zonov.me/golang-for-rubyists-part-5-how-to-start-applying-golang-to-aws-lambda/" rel="noopener" target="_blank">my past blog post</a> on how to make it painlessly.
But for today that's it, thank you for reading.
If you don't want to miss the next posts, subscribe to my Twitter, tho I chat there for random topics, also I share some interesting posts I've discovered, as well as my own.
And psst, there is a big orange button beneath, using it, you can buy me an espresso macchiato, which I really love and will appreciate ;)
P.S. Thanks Claude for coffee and pleasant feedback, it helps staying motivated!		
