---
layout: post
title: Golang for Rubyists. Part 2. Go type system
date: 2018-03-22 11:03:10.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Development
tags:
- golang
- ruby
- serverless
- tutorial
meta:
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  _adinserter_block_exceptions: ''
  hefo_before: '0'
  hefo_after: '0'
  _wp_old_slug: golang-for-rubyists-part-2-go-typing-system
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/golang-for-rubyists-part-2-go-type-system/"
excerpt: "Hello, my dear Golang newcomers. I feel so excited when I start writing this post! This is because I learn in the meantime. If when I’m writing about Ruby or JS I’m mostly describing my experience, probably with some additions of new findings I’ve got during the preparation, however in this particular case I’m learning together with you.
And today’s post will be about types and logical structures of Go. Let’s start!"
---

Hello, my dear Golang newcomers. I feel so excited when I start writing this post! This is because I learn in the meantime. If when I'm writing about Ruby or JS I'm mostly describing my experience, probably with some additions of new findings I've got during the preparation, however in this particular case I'm learning together with you.
And today's post will be about types and logical structures of Go. Let's start!
<!--more-->
Did you ever work with statically typed languages? I had some experience. F.e. Swift lang I really enjoyed, including their strong type system with optional values, also when I wrote an app using Clojure I didn't have much pain with their strong dynamic typing. However, on my daily base, I'm usually enjoying (am I?) the Ruby's and JS' Duck type system. On the opposite side in Go you have quite a strong typing, so let's start with smth small.
<h2>Variables assignment</h2>
In my previous post I used such a construction (ok, it was a bit different, but still):
<code>hello, world := "Hello", "World"</code>
The other way to write this would be:
{% highlight go %}
var hello string = "Hello"
var world string = "World"
{% endhighlight %}
The construct `<strong>:=</strong>` is a shortened version of the same, as you may see. It automatically realizes that you want to have a variable, not a constant, and also it automatically grasps your variable type.
Furthermore, you can also write <code>var hello = "Hello"</code> and it will grasp the type, so it's not only `<strong>:=</strong>`'s feature.
In all the examples above we defined variables together with its assignment. What you can also do is to split this up. F.e.
{% highlight go %}
package main 
import "fmt" 
func main() {
  var someString string
  someString = "hello"
  fmt.Println(someString)
}
{% endhighlight %}
(If the overall structure looks unfamiliar to you - check out my <a href="http://zonov.me/golang-osx-installation-and-getting-started/" rel="noopener" target="_blank">previous blog post</a>, where I described the most basics of Golang).
In this example, as you may see, we split up the variable definition and the value assignment. Since it's a <strong>variable</strong>, we can do this. Now do a couple of experiments:
1. Try to change the first line in function to be <code>const someString string</code>. Then try to run it <code>go run hello.go</code>. Ta-daam, compilation error. If you define a constant, you should instantly assign it. And it makes a lot of sense by me.
2. Now change your code to be:
{% highlight go %}
package main 
import "fmt"
func main() {
  var someString string
  someString = 123
  fmt.Println(someString)
}
{% endhighlight %}
Can you guess, what will be the result? Yup, an error. Because it's a statically typed language, you, script-writers! No auto type conversion for us here, piss off!
<h2>Types in Golang</h2>
Ok, let's be cool with it. In general, Go has following type categories: Numbers, Strings, Lists, Booleans.
<h3>Booleans</h3>
For Booleans no surprises (just the type name is not boolean, but <strong>bool</strong>), they are just a standard booleans, like true/false with usual boolean algebra rules. Also as in Ruby and JS you have the bang operator, which inverts the variable. You can also use two bangs for double inversion.
{% highlight go %}
someString := true
fmt.Println(!someString)
// will return you a false
{% endhighlight %}
Unfortunately this kind of structure, which we all like:
{% highlight go %}
someString := "something"
fmt.Println(!someString)
{% endhighlight %}
won't work.
<h3>Strings</h3>
The next convenient type, String. You already have got familiar with it in the previous post.
Make your code to look like this:
{% highlight go %}
package main 
import "fmt" 
func main() {
  doubleQuotedString := "Hello, \nWorld!"
  backticksQuotedString := `Hello, \nWorld!`
  singleQuotedChar := 'H'
  fmt.Println(doubleQuotedString)
  fmt.Println(backticksQuotedString)
  fmt.Println(singleQuotedChar)
}
{% endhighlight %}
Here we use three different notations. The first one is double-quoted, it interprets the special characters, like <strong>\n</strong> which I use here, so it will split the string into two lines in your output. The second notation uses backticks and kind of safe, so it doesn't interpret anything inside itself. The third one you've already seen in the previous post, it stores a character and returns you its UTF code.
There is no proper string interpolation in Golang, so it can be bothersome to deal with it, however there is a way, but we will talk about it in another post.
<h3>Numbers</h3>
Next type family is Numbers. As usual, it has float and integers. And if in Ruby you just have a generic type and interpreter decides by itself, which type to use, in Golang you should specify it by yourself. And the possible types are:
<h4>Integers</h4>
int8, int16, int32, int64, uint8, uint16, uint32, uint64
Numbers in these types, as you may understand, represent bits amount, and the prefix 'u' on some of them means that it can not be negative (<strong>unsigned</strong>). If you will try to pass a negative number to your uint variable, it will throw an error <code>constant -10 overflows uint32</code>
<h4>Floats</h4>
float32, float64. Nothing new here, numbers represent the same. Also as in any other language, numbers with floating points are not round, so this code won't give you 0.9:
{% highlight go %}
var x float32 = 1.1
var y float32 = 0.2
fmt.Println(x - y)
{% endhighlight %}
<h3>Lists</h3>
Lists are a big topic to discuss, so in this post I will cover it's types and the basic difference, and let's start with the most (as you would expect) familiar one.
<h4>Arrays</h4>
An array is a fixed size ordered homogeneous list. This sentence says for itself. The first thing to know is that once you set up an array size, you cannot change it. The second thing is that if you say that your array consists of numbers, you could not put there any strings. More of that, if you say that your array consists of int32, you cannot put e.g. int64 into it. Already scared? Let's see some examples.
{% highlight go %}
package main
import "fmt"
func main() {
  var x [10]int
  fmt.Println(x)
}
{% endhighlight %}
This is a syntax to define an array, which will have a length 10 and you expect it to contain integers.
If you run this program, it will print <code>[0 0 0 0 0 0 0 0 0 0]</code>. Now let's scoff at it and try to feed if with something wrong:
{% highlight go %}
package main
import "fmt"
func main() {
  var x [10]int
  var number int64 = 42
  x[3] = number
  fmt.Println(x)
}
{% endhighlight %}
Here we first define the array of ints, then define a variable with a type of int64, and then try to feed this variable to the array. And the result as expected is <code>./main.go:8:8: cannot use number (type int64) as type int in assignment</code>. This just affirms what I wrote above, however one more interesting thing here. As you could notice, we don't use number-suffixed int type, but just <strong>int</strong>. And as a Rubyist you could probably expect it to be some generic type, which will accept all the int types, but it is not. Is it just a reference to the int32? Hmm, again missed. It is smth strange, an integer, which may have at least 32 bits of size.
The other way to assign an array is also together with its value:
{% highlight go %}
x := [3]int{5, 6, 7}
{% endhighlight %}
<h4>Slices</h4>
They are actually much more common in the Golang world due to their flexibility. They are still homogeneous, but instead of being fixed in size, they are changeable. Here is a simple snippet:
{% highlight go %}
package main
import "fmt"
func main() {
  nums := []int{2, 3, 11, 13}
  nums = append(nums, 0, 1)
  var s []int = nums[3:len(nums)]
  fmt.Println(s)
}
{% endhighlight %}
Here we first define a Slice, which definition looks very similar to Arrays, just without a length setting. Then we modify it by adding two elements to the end. Ans as a next step we take elements starting from the index [3] to the end. So your result here will be <code>[13 0 1]</code>. Pretty flexible, huh? However there are some downsides of this structure, we will talk about in some future post.
<h4>Maps</h4>
Maps are almost the same as Ruby's Hashes. Just with one difference. Can you guess, what is the difference? Yeah, u rrright, they are statically typed!
{% highlight go %}
package main
import "fmt"
func main() {
  population := make(map[string]int)
  population["Berlin"] = 3500000
  population["Munich"] = 1500000
  fmt.Println(population)
}
{% endhighlight %}
The result will be <code>map[Berlin:3500000 Munich:1500000]</code>. The static typic here cannot stop you from using a deep maps, where elements are containing other maps, like <code>map[string]map[int]</code>. Is your brain already melting? No worries, we're close to an end. One more new thing here you could notice is a <code>make</code> function. If you define a list with this function, it not just defines it, but also allocates a memory for your structure.
Phew, enough for today. I go on vacation next week, but still, hope to have some time to write one more Golang for Rubyists post before that. Also, just a reminder, I have a youtube channel <a href="https://www.youtube.com/channel/UCjYpgu18Nd6Wngbwp47MHaA" rel="noopener" target="_blank">EverydayDev</a> where I sporadically (actualy quite often) post some short videos about my insights or interesting articles or tools I read about this day. It is a quite laborious process for me, creating even such short videos, so I really appreciate any feedback or likes I'm getting there. So please feel free to check them out as well. I'm glad if this post was useful to you, see you very soon!
