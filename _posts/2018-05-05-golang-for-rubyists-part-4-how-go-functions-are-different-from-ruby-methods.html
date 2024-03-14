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
excerpt: "\n\t\t\t\t\t\t"
---
<p>
				Good morning, my dear readers, today we will talk about a concept, without which no software can be developed! Ok, not only without this concept... And actually, it can be...<br />
Ok, never mind, it is still a very important topic if you wish to get the hang of Golang!</p>
<p><!--more--></p>
<p>As you already get from the title, today's topic is about functions or as we call them in Ruby, methods. I will not teach you, why do we need functions at all, as I assume you know at least one programming language, so my purpose here is to show you differences and gotchas about functions in Golang, comparing to similar structures in Ruby.</p>
<p>Let's start with a creation of a simple program, which will add two numbers for us.</p>
<p>[cpp]<br />
package main</p>
<p>import (<br />
	&quot;fmt&quot;<br />
)</p>
<p>func main() {<br />
	firstNumber := 40<br />
	secondNumber := 2<br />
	sum := firstNumber + secondNumber<br />
	fmt.Println(&quot;Here is the answer, master: &quot;, sum)<br />
}<br />
[/cpp]</p>
<p>It looks pretty similar to what you've seen before in my previous posts. We already have one function, which name is <code>main()</code>. It is a special function and I have written a more thorough explanation of it in the <a href="http://zonov.me/golang-osx-installation-and-getting-started/" target="_blank" rel="noopener">first post</a> of this series. But sum is a really generic function and you don't want to just have it in your <code>main()</code>, but instead move to some pure function, specifically dedicated for that. Here it is:</p>
<p>[cpp]<br />
package main</p>
<p>import (<br />
	&quot;fmt&quot;<br />
)</p>
<p>func sum(firstNumber int, secondNumber int) (int) {<br />
	sum := firstNumber + secondNumber<br />
	return sum<br />
}</p>
<p>func main() {<br />
	firstNumber := 40<br />
	secondNumber := 2<br />
	fmt.Println(&quot;Here is the answer, master: &quot;, sum(firstNumber, secondNumber))<br />
}<br />
[/cpp]</p>
<p>Ok, now we see few new things. First to mention is that because Golang is statically typed, you have to explicitly tell the function, what types it can take. Try to pass some string to this function and you will see, what happens. If you are doing this on the go playground you won't notice the difference, but if you're compiling it locally - you'll that it fails right on the compilation, not in the runtime. "Too obvious!" - will say me Java or .Net developer. "Nope!" - can answer a Ruby/Python/JS developer.</p>
<p>One more thing, you may notice above is a one more "int" in braces, going after the <code>sum(firstNumber int, secondNumber int)</code>. This way in Golang you should specify the output param of your function.</p>
<p>So you can imagine, that your function for the application is kind of a black box. The app knows, what it can put to the black box and what kind of result to expect, but don't want to have a clue about the internals.</p>
<p>In the next snippet, I will make two more changes, behold!</p>
<p>[cpp]<br />
package main</p>
<p>import (<br />
	&quot;fmt&quot;<br />
)</p>
<p>func sum(a int, b int) (sum int) {<br />
	sum = a + b<br />
	return<br />
}</p>
<p>func main() {<br />
	firstNumber := 40<br />
	secondNumber := 2<br />
	fmt.Println(&quot;Here is the answer, master: &quot;, sum(firstNumber, secondNumber))<br />
}<br />
[/cpp]</p>
<p>Feel free to play with the snippet by yourself, using the <a href="https://play.golang.org/p/n4gJhZfUN0V" target="_blank" rel="noopener">Golang playground</a>.</p>
<p>First thing I changed is names of input params for the <code>sum</code> function. It illustrates us that the variable names for the called function should not necessarily be the same as for the caller. The second change I made is I gave a name to the variable I plan to return from the function, so I don't have to write <code>return sum</code>, because the <code>sum</code> variable is returned anyway. And it implicitly leads to the third change I made. Instead of <code>sum := ...</code> I wrote <code>sum = ...</code>. If you don't understand, why - go and check <a href="http://zonov.me/golang-for-rubyists-part-2-go-type-system/" target="_blank" rel="noopener">this post</a>.</p>
<p>Also important to mention the <code>return</code> word here. It is optional thing in Ruby or Javascript, but absolutely mandatory in Golang, even in this example when you already listed, what variable do you plan to return.</p>
<p>In Ruby we can pass an "unlimited" amount of arguments without explicit mentioning each one in the function definition:</p>
<p>[ruby]<br />
def sum(*numbers)<br />
end<br />
[/ruby]</p>
<p>Do we have something similar in Golang? Yay, we do!</p>
<p>[cpp]<br />
package main</p>
<p>import (<br />
	&quot;fmt&quot;<br />
)</p>
<p>func sum(numbers ...int) (sum int) {<br />
	for _, value := range numbers {<br />
		sum += value<br />
	}<br />
	return<br />
}</p>
<p>func main() {<br />
	firstNumber := 40<br />
	secondNumber := 2<br />
	thirdNumber := 1<br />
	fmt.Println(&quot;Here is the answer, master: &quot;, sum(firstNumber, secondNumber, thirdNumber))<br />
}<br />
[/cpp]</p>
<p>Again, <a href="https://play.golang.org/p/RtnbPkH8-Ph" target="_blank" rel="noopener">here is the snippet</a>. Works exactly the same way as in Ruby. Just the name is different, these folks call it "Variadic functions". Exactly, a special name for such a basic thing!</p>
<p>There is almost nothing to cover in the Functions topic. One mindblowing thing I want to write about is private/public functions definition, but it will take a whole new post. So probably just one tiny listing to show is this one, which looks funny but still cool:<br />
[cpp]<br />
package main</p>
<p>import (<br />
	&quot;fmt&quot;<br />
)</p>
<p>var firstNumber int = 40<br />
var secondNumber int = 2</p>
<p>func sum(numbers ...int) (sum int) {<br />
	for _, value := range numbers {<br />
		sum += value<br />
	}<br />
	return<br />
}</p>
<p>func main() {<br />
	firstNumber = 1<br />
	fmt.Println(&quot;Here is the answer, master: &quot;, sum(firstNumber, secondNumber))<br />
}<br />
[/cpp]<br />
What answer will you get? Right, it will be 3.<br />
So you can define and assign variables outside of functions and then not only access them, but also modify. (Pssst, your functions won't be pure in this case!)<br />
Probably this post was too basic, but it is one of the cornerstones of understanding a new language, so I hope it was useful. To help yourself understand it more, the next step can be to open some of my snippets and go wild changing them :)<br />
See you next week!<br />
&nbsp;<br />
UPD. Thanks MonkeeSage for pointing out in Reddit comments, <code>*args</code> in Ruby is also named by the same idea. Their name is "Variable-Length Argument Lists", and this is taken from the <a href="https://www.gnu.org/software/libc/manual/html_node/Variadic-Functions.html" rel="noopener" target="_blank">Variadic functions of C</a>.		</p>
