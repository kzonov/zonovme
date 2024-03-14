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
excerpt: "\n\t\t\t\t\t\t"
---
<p>
				Welcome back, my fellow learners.<br />
I still on my way of learning Golang and recently stumbled upon some listing, where I've found asterisks and ampersands, which I couldn't get the hang of and had to google. So I think it is a great topic for the third post in the series.</p>
<p><!--more--></p>
<p>Let's first recall, where can you find asterisks (*) and ampersands (&amp;) in Ruby? Obviously we don't speak about logical or math functions here, it is the same in any (oh, such a broad word, ok, in most of) programming languages. So, asterisks, here is the code, which any Ruby developer understands:</p>
<p>[ruby]<br />
2.2.1 :001 &gt; def meaningful_method(var1, var2, var3)<br />
2.2.1 :002?&gt;   p var1<br />
2.2.1 :003?&gt;   p var2<br />
2.2.1 :004?&gt;   p var3<br />
2.2.1 :005?&gt;   end<br />
 =&gt; :meaningful_method<br />
2.2.1 :006 &gt;<br />
2.2.1 :007 &gt;<br />
2.2.1 :008 &gt;   meaningful_method(['a', 'b', 'c'])<br />
ArgumentError: wrong number of arguments (1 for 3)<br />
	from (irb):1:in `meaningful_method'<br />
	from (irb):8<br />
	from /Users/kirillzonov/.rvm/rubies/ruby-2.2.1/bin/irb:11:in `&lt;main&gt;'<br />
2.2.1 :009 &gt;<br />
2.2.1 :010 &gt;   meaningful_method(*['a', 'b', 'c'])<br />
&quot;a&quot;<br />
&quot;b&quot;<br />
&quot;c&quot;<br />
 =&gt; &quot;c&quot;<br />
[/ruby]</p>
<p>So, we name this operator "splat", it kinda opens an array. It can work the similar way in method arguments:</p>
<p>[ruby]<br />
2.2.1 :015 &gt;   def another_meaningful_method(*vars)<br />
2.2.1 :016?&gt;     p vars<br />
2.2.1 :017?&gt;   end<br />
 =&gt; :another_meaningful_method<br />
2.2.1 :018 &gt;<br />
2.2.1 :019 &gt;<br />
2.2.1 :021 &gt; another_meaningful_method(['a', 'b', 'c'])<br />
[[&quot;a&quot;, &quot;b&quot;, &quot;c&quot;]]<br />
 =&gt; [[&quot;a&quot;, &quot;b&quot;, &quot;c&quot;]]<br />
2.2.1 :022 &gt; another_meaningful_method('a', 'b', 'c')<br />
[&quot;a&quot;, &quot;b&quot;, &quot;c&quot;]<br />
 =&gt; [&quot;a&quot;, &quot;b&quot;, &quot;c&quot;]<br />
[/ruby]</p>
<p>We usually use it to pass some additional arguments, amount or existing of which we're not sure about. So, long story short, in Ruby we use asterisk to open an array. Or multiply numbers. Or multiply a number and a string. Or something else. And you can use two asterisks and have a different behaviour. Ok, Ruby is weird sometimes. What about ampersand?</p>
<p>The first thing comes to our mind is double ampersand as a logical operator. Move on, what else?</p>
<p>[ruby]<br />
2.2.1 :026 &gt; [1, 2, 3] &amp; [2, 3, 4]<br />
 =&gt; [2, 3]<br />
[/ruby]</p>
<p>Ok, arrays' intersection. Anything else?</p>
<p>[ruby]<br />
2.2.1 :036 &gt;   def even_more_meaningful_method(var1, &amp;block)<br />
2.2.1 :037?&gt;     p var1<br />
2.2.1 :038?&gt;     block.call('bar')<br />
2.2.1 :039?&gt;   end<br />
 =&gt; :even_more_meaningful_method<br />
2.2.1 :040 &gt; even_more_meaningful_method('foo') { |block_argument| p block_argument }<br />
&quot;foo&quot;<br />
&quot;bar&quot;<br />
 =&gt; &quot;bar&quot;<br />
[/ruby]</p>
<p>Yeah, our beloved blocks. I remember when I was just starting doing Ruby that syntax could blow my mind. So we use ampersand here to tell that the argument we're expecting is a block, so it will be passed in different way than plaun arguments. Cannot recall any other usages of ampersand in Ruby from the top of my head, so let's check out, how does Golang suggest us to use ampersands.</p>
<p>If you have an engineering education, you're most likely have heard about pointers from your C/C++ course. And if you're like me - you've never worked with them in a real life outside of a university. So let's refresh our memory.<br />
We assume that we have a drawer, where there is some object on each shelf. Then you look into one drawer, see that there is an orange and say to your friend, hey dude, remember "an orange". This is a typical variable assignment. You just assigned to your friend's memory value "an orange". If you then will eat this delicious juicy orange and put some disgusting thing like broccoli onto that shelf - your friend will still remember "an orange". BUT. Instead of telling your friend "please remember "an orange", you can tell him, "dude, there is something on the second shelf of that drawer, please remember it". He will remember <b>where is "it" located</b>, not the object itself. And this is a pointer assignment. Probably my real-world-explanation convoluted things just more, so let's simply take a look at the following listing:</p>
<p>[cpp]<br />
package main</p>
<p>import &quot;fmt&quot;</p>
<p>func main() {<br />
	variable1 := 1<br />
	variable2 := 2</p>
<p>	pointer1 := &amp;variable1<br />
	// set pointer1 to address of variable1<br />
	// pointer1 address: 0x10414020<br />
	// pointer1 value:   1<br />
	// variable1 value:  1</p>
<p>	*pointer1 = 3<br />
	// change the value at address &amp;variable1 to 3<br />
	// pointer1 address: 0x10414020<br />
	// pointer1 value:   3<br />
	// variable1 value:  3</p>
<p>	variable1 = 4<br />
	// change the value of variable1 to 4<br />
	// pointer1 address: 0x10414020<br />
	// pointer1 value:   4<br />
	// variable1 value:  4</p>
<p>	pointer1 = &amp;variable2<br />
	// set pointer1 to the address of variable2<br />
	// pointer1 address: 0x10414024<br />
	// pointer1 value:   2<br />
	// variable1 value:  4<br />
	// variable2 value:  2</p>
<p>	pointer2 := pointer1<br />
	// set pointer2 to the address in pointer1<br />
	// pointer1 address:  0x10414024<br />
	// pointer1 value:    2<br />
	// pointer2 address: 0x10414024<br />
	// pointer2 value:   2<br />
	// variable1 value:  4<br />
	// variable2 value:  2</p>
<p>	*pointer1 = 5<br />
	// change the value at the address &amp;variable2 to 5<br />
	// pointer1 address:  0x10414024<br />
	// pointer1 value:    5<br />
	// pointer2 address: 0x10414024<br />
	// pointer2 value:   5<br />
	// variable1 value:  4<br />
	// variable2 value:  5</p>
<p>	pointer1 = &amp;variable1<br />
	// change pointer1 to address of variable1<br />
	// pointer1 address:  0x10414020<br />
	// pointer1 value:    4<br />
	// pointer2 address: 0x10414024<br />
	// pointer2 value:   5<br />
	// variable1 value:  4<br />
	// variable2 value:  5<br />
}<br />
[/cpp]</p>
<p>This code may look cumbersome but just because of comments, don't be afraid, just go through it without hurry.<br />
Long story short, if you assign some variable to another variable, it will copy its value.</p>
<p>[ruby]<br />
2.2.1 :043 &gt;   a = 1<br />
 =&gt; 1<br />
2.2.1 :044 &gt; b = a<br />
 =&gt; 1<br />
2.2.1 :045 &gt; a = 2<br />
 =&gt; 2<br />
2.2.1 :046 &gt; b<br />
 =&gt; 1<br />
[/ruby]</p>
<p>If you assign an memory address of some variable (pointer) to another variable, it will be pointing onto the same memory cell. And if you change the value of the original variable and then will try to access it from a second variable - you will get an updated value.</p>
<p>And as you all know, in Ruby you cannot operate with pointers. And it is pass-by-value language (sarcasm). Let's make a last small experiment to illustrate it:</p>
<p>[ruby]<br />
2.2.1 :054 &gt;   a = [1, 2, 3]<br />
 =&gt; [1, 2, 3]<br />
2.2.1 :055 &gt; b = a<br />
 =&gt; [1, 2, 3]<br />
2.2.1 :056 &gt; b &lt;&lt; 42<br />
 =&gt; [1, 2, 3, 42]<br />
2.2.1 :057 &gt; a<br />
 =&gt; [1, 2, 3, 42]<br />
[/ruby]</p>
<p>[caption id="" align="alignnone" width="604"]<img class="size-large" src="{{ site.baseurl }}/assets/2018/04/85afd1ebc17194788d7581af4cf1a29f7dfc7b08b922f50dbc67bd64f08fa370.jpg" alt="Wait a minute" width="604" height="453" /> Wait a minute[/caption]</p>
<p>Ok, that's another story, however, if you're curious about how Ruby actually works with objects (this is a cue, this array is not just an address in memory, it is an object) and their assignments and passing - I can recommend <a href="https://robertheaton.com/2014/07/22/is-ruby-pass-by-reference-or-pass-by-value/" target="_blank" rel="noopener">this article</a>.<br />
I hope you enjoyed reading this article and get the hang of how you can use pointers in Golang and how working with variables is different from Ruby. See ya!</p>
<p>P.S. If you missed previous posts in this series, here they are, right for you!</p>
<ul>
<li><a href="http://zonov.me/golang-for-rubyists-part-2-go-type-system/" target="_blank" rel="noopener">Golang for Rubyists. Part 2. Go type system</a></li>
<li><a href="http://zonov.me/golang-osx-installation-and-getting-started/" target="_blank" rel="noopener">Golang for Rubyists. Part 1. Go 1.10 OSX installation and getting started</a></li>
</ul>
<p>UPD. Stooop, but what about references and pass by reference, 'cause you mentioned it in the title? Ah, Golang doesn't have that, actually. You can either have a pointer-variable or copy the variable value to a new variable and so to a new memory cell. I can point you to <a href="https://dave.cheney.net/2017/04/29/there-is-no-pass-by-reference-in-go" target="_blank" rel="noopener">this article</a>, where you can find a deeper explanation.		</p>
