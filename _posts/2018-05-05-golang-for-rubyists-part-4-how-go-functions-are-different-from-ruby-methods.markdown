---
layout: post
title: Golang for Rubyists. Part 4. How Go functions are different from Ruby methods
date: 2018-05-05 11:53:32.000000000 +02:00
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
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/golang-for-rubyists-part-4-how-go-functions-are-different-from-ruby-methods/"
excerpt: "Good morning, my dear readers, today we will talk about a concept, without which no software can be developed! Ok, not only without this concept… And actually, it can be…
Ok, never mind, it is still a very important topic if you wish to get the hang of Golang!"
---

Good morning, my dear readers, today we will talk about a concept, without which no software can be developed! Ok, not only without this concept... And actually, it can be...
Ok, never mind, it is still a very important topic if you wish to get the hang of Golang!
<!--more-->
As you already get from the title, today's topic is about functions or as we call them in Ruby, methods. I will not teach you, why do we need functions at all, as I assume you know at least one programming language, so my purpose here is to show you differences and gotchas about functions in Golang, comparing to similar structures in Ruby.
Let's start with a creation of a simple program, which will add two numbers for us.
{% highlight go %}
package main
import (
	"fmt"
)
func main() {
	firstNumber := 40
	secondNumber := 2
	sum := firstNumber + secondNumber
	fmt.Println("Here is the answer, friend: ", sum)
}
{% endhighlight %}
It looks pretty similar to what you've seen before in my previous posts. We already have one function, which name is <code>main()</code>. It is a special function and I have written a more thorough explanation of it in the <a href="http://zonov.me/golang-osx-installation-and-getting-started/" target="_blank" rel="noopener">first post</a> of this series. But sum is a really generic function and you don't want to just have it in your <code>main()</code>, but instead move to some pure function, specifically dedicated for that. Here it is:
{% highlight go %}
package main
import (
	"fmt"
)
func sum(firstNumber int, secondNumber int) (int) {
	sum := firstNumber + secondNumber
	return sum
}
func main() {
	firstNumber := 40
	secondNumber := 2
	fmt.Println("Here is the answer, friend: ", sum(firstNumber, secondNumber))
}
{% endhighlight %}
Ok, now we see few new things. First to mention is that because Golang is statically typed, you have to explicitly tell the function, what types it can take. Try to pass some string to this function and you will see, what happens. If you are doing this on the go playground you won't notice the difference, but if you're compiling it locally - you'll that it fails right on the compilation, not in the runtime. "Too obvious!" - will say me Java or .Net developer. "Nope!" - can answer a Ruby/Python/JS developer.
One more thing, you may notice above is a one more "int" in braces, going after the <code>sum(firstNumber int, secondNumber int)</code>. This way in Golang you should specify the output param of your function.
So you can imagine, that your function for the application is kind of a black box. The app knows, what it can put to the black box and what kind of result to expect, but don't want to have a clue about the internals.
In the next snippet, I will make two more changes, behold!
{% highlight go %}
package main
import (
	"fmt"
)
func sum(a int, b int) (sum int) {
	sum = a + b
	return
}
func main() {
	firstNumber := 40
	secondNumber := 2
	fmt.Println("Here is the answer, friend: ", sum(firstNumber, secondNumber))
}
{% endhighlight %}
Feel free to play with the snippet by yourself, using the <a href="https://play.golang.org/p/n4gJhZfUN0V" target="_blank" rel="noopener">Golang playground</a>.
First thing I changed is names of input params for the <code>sum</code> function. It illustrates us that the variable names for the called function should not necessarily be the same as for the caller. The second change I made is I gave a name to the variable I plan to return from the function, so I don't have to write <code>return sum</code>, because the <code>sum</code> variable is returned anyway. And it implicitly leads to the third change I made. Instead of <code>sum := ...</code> I wrote <code>sum = ...</code>. If you don't understand, why - go and check <a href="http://zonov.me/golang-for-rubyists-part-2-go-type-system/" target="_blank" rel="noopener">this post</a>.
Also important to mention the <code>return</code> word here. It is optional thing in Ruby or Javascript, but absolutely mandatory in Golang, even in this example when you already listed, what variable do you plan to return.
In Ruby we can pass an "unlimited" amount of arguments without explicit mentioning each one in the function definition:
{% highlight go %}
def sum(*numbers)
end
{% endhighlight %}
Do we have something similar in Golang? Yay, we do!
{% highlight go %}
package main
import (
	"fmt"
)
func sum(numbers ...int) (sum int) {
	for _, value := range numbers {
		sum += value
	}
	return
}
func main() {
	firstNumber := 40
	secondNumber := 2
	thirdNumber := 1
	fmt.Println("Here is the answer, friend: ", sum(firstNumber, secondNumber, thirdNumber))
}
{% endhighlight %}
Again, <a href="https://play.golang.org/p/RtnbPkH8-Ph" target="_blank" rel="noopener">here is the snippet</a>. Works exactly the same way as in Ruby. Just the name is different, these folks call it "Variadic functions". Exactly, a special name for such a basic thing!
There is almost nothing to cover in the Functions topic. One mindblowing thing I want to write about is private/public functions definition, but it will take a whole new post. So probably just one tiny listing to show is this one, which looks funny but still cool:
{% highlight go %}
package main
import (
	"fmt"
)
var firstNumber int = 40
var secondNumber int = 2
func sum(numbers ...int) (sum int) {
	for _, value := range numbers {
		sum += value
	}
	return
}
func main() {
	firstNumber = 1
	fmt.Println("Here is the answer, friend: ", sum(firstNumber, secondNumber))
}
{% endhighlight %}
What answer will you get? Right, it will be 3.
So you can define and assign variables outside of functions and then not only access them, but also modify. (Pssst, your functions won't be pure in this case!)
Probably this post was too basic, but it is one of the cornerstones of understanding a new language, so I hope it was useful. To help yourself understand it more, the next step can be to open some of my snippets and go wild changing them :)
See you next week!
 
UPD. Thanks MonkeeSage for pointing out in Reddit comments, <code>*args</code> in Ruby is also named by the same idea. Their name is "Variable-Length Argument Lists", and this is taken from the <a href="https://www.gnu.org/software/libc/manual/html_node/Variadic-Functions.html" rel="noopener" target="_blank">Variadic functions of C</a>.		
