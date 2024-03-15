---
layout: post
title: Golang for Rubyists. Part 1. Go 1.10 OSX installation and getting started
date: 2018-03-15 23:13:00.000000000 +01:00
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
- tutorial
meta:
  _edit_last: '1'
  _adinserter_block_exceptions: ''
  hefo_before: '0'
  hefo_after: '0'
  _syntaxhighlighter_encoded: '1'
  _aioseop_description: Introduction to why Golang can be worth to learn; how to install
    and start with it.
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/golang-osx-installation-and-getting-started/"
excerpt: "Hey folks, I’m starting getting my feet wet with Golang and plan to document the process so it would be easy for me to recall it in the future and probably useful for some of you as well!
In this first post, I will cover some basic things like what Golang is for, how to install it and how to write and run your first program."
---

Hey folks, I'm starting getting my feet wet with Golang and plan to document the process so it would be easy for me to recall it in the future and probably useful for some of you as well!
In this first post, I will cover some basic things like what Golang is for, how to install it and how to write and run your first program.
<!--more-->
If you've been a developer for about a decade you've probably got your hands dirty with many different languages, so what this one is suitable for, you may ask? Can you use it for your pet-project blog? No. Can you build a typical e-commerce website on it? Nope. Okay, just some web project? Hmm, most likely you wouldn't. Probably mobile app? Yeah, you know, there are some solutions, but it's not a good idea.
But what is it useful for? Actually plenty of things. AWS recently introduced Golang support for their Lambdas. A lot of popular open-source tools are written in Golang, such as Terraform and Docker. Go is very useful for concurrent applications development and for fast and not too low-level applications.
Now if you see the interest for you in this language (or you probably already have it, otherwise why would you open this link), let's move on to the installation process.
I'm an OSX user for a long time now and don't remember much about Linux family as a desktop, btw the installation process is usually pretty similar to all the Unix based machines. So for OSX your first step is:
<code>brew install golang</code>
For Ubuntu, it probably would be smth like <code>apt-get install golang</code>.
UPD. Thanks ChristophBerger for pointing out in Reddit comments, the original Homebrew's command is <code>brew install go</code>, however <code>golang</code> is an alias fore brew and you can use it as well.
This will most likely install the latest version for you, which in my case is 1.10. Also, it will automatically (at least for OSX) add a path to the executable into your PATH. To make sure that everything went alright, type <code>go -v</code> in your terminal and you should be able to see your version then, for me it says <code>go version go1.10 darwin/amd64</code>. You should get something similar. No additional packages are needed in order to start developing with Golang.
Create a fresh new folder and a file there, named <code>hello.go</code>. Did you get, what our app will be about? Yeah, hello world! We will enhance it just a little bit, but it still will be the old and true "Hello world" thing.
Here is the listing of our first function. Put it into your file and let's move on to the explanation.
{% highlight go %}
package main
import "fmt"
func main() {
  fmt.Println("Hello, World!")
}
{% endhighlight %}
First let's execute it and then I will explain this listing line by line. Because if you like me come from Ruby, JS or similar worlds, some things may look wrong or strange for you.
Save the file, go back to your console and just type
<code>go run hello.go</code>
And your function will greet the world.
Nothing fancy, so let's go to the explanation. The first line is <code>package main</code>. Package is a <strong>thing</strong> in Golang, which contains functions, constants and etc. Go is not an OOP language, so it doesn't have Classes concept and these packages are the things, which helps you follow the DRY principle, so reuse your code as well as separate different domain code one from another, as you would do it in Ruby with modules.
The important thing to know here is that your entry point package should always be <strong>main</strong>. If you will try to change it - you'll get an error (go wild and try it).
The second line is hmm... importing something, yup? And it is importing exactly a package, which name is "fmt", which stands for "formatting".
The next block is a function definition and its name is <code>main()</code>. And this is again a mandatory thing. Your entry-point function should always be named main. You can try to change it and the compiler will argue at you.
The body of the function is pretty straightforward, nothing to say here.
Now let's modify our code a little bit:
{% highlight go %}
package main
import "fmt"
func main()  {
  const hello, world = "Hello", "World"
  fmt.Println(hello, world, '*')
}
{% endhighlight %}
And here we can see two interesting features. First one is multi-assignment. As in Ruby, you can assign multiple variables, separated by a comma. The second thing you will notice if you will try to run your code. It will give you <code>Hello World 42</code>. Ta-daaaam. Difference between single and double quotes in Golang is much more significant than in languages like Ruby, Python and JS. In Golang it converts your character (yep, <strong>character</strong>, not a string) into it's UTF code.
I think it is enough for the first post, looking forward to your comments and other reactions (tweets, shares, blaming, donation, whatever). Stay tuned!		
