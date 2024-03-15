---
layout: post
title: JS on Backend in 2018. Tutorial. Part 1. App server. Getting started with Node.js
  using Express.js.
date: 2018-02-11 16:59:18.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Development
tags:
- backend
- development
- es6
- expressjs
- javascript
- js
- nodejs
- tutorial
meta:
  _edit_last: '1'
  _adinserter_block_exceptions: ''
  hefo_before: '0'
  hefo_after: '0'
  _syntaxhighlighter_encoded: '1'
  _aioseop_description: Entry-level article, showing how to use Node Package Manager,
    how to develop a basic Node.js application using Express.js and how to follow
    modern practices, like ES6 syntax. Also covered code linting topic.
  _aioseop_title: JS on Backend in 2018. Tutorial. Part 1. Getting started with Express.js.
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/js-on-backend-in-2018-tutorial-part-1-app-server/"
excerpt: "I recently started my new pet-project using ExpressJS as a backend API server, so it's a good time to document each step and help node.js newcomers to start building real things using this great technology."
---

I recently started my new pet-project using ExpressJS as a backend API server, so it's a good time to document each step and help node.js newcomers to start building real things using this great technology.
<!--more-->
Before starting an app development and all dependencies installation, we should first install the Nodejs itself. If you're on Mac it can be done as usual using homebrew: <code>brew install node</code> (if you don't - it's probably just <code>sudo apt-get install node</code>). As you may know, JS in particular and Nodejs in general is famous of its frameworks-zoo. But whichever framework you choose, first you have to install a package manager. And already at the start, you have to choose, which one would you prefer, classic NPM or created in the Facebook's basements Yarn.
In the past times NPM used to be terrible, slow and glitchy, but it improved a lot since that. F.e. one of the killing features of Yarn was `yarn.lock` file, which allowed you to lock your dependencies' versions. Not an advantage anymore! NPM has `package-lock.json`, which solves the same problem. So for my app I choose NPM. And one more good thing is that you have NPM installed out of the box. To make absolutely sure that you have it, run <code>npm -v</code>. :)
Start with creating a folder, then perform `npm init` there. It will ask you few questions about your app. Nothing strange, I would not sweat it, because you can change it at any time from your package.json file. Just one thing, when it will ask you for <code>entry point:</code> , type <code>app.js</code> there. You'll see, why you need it in a few minutes.
Next thing is choosing the web framework. I came from Rails world and used to use this heavy all-in-one framework for quite a while, but recently I started picking other options for my projects and found them even better, you can read about my experience with Grape <a href="http://zonov.me/ruby-on-grapes-why-did-we-choose-grape-and-why-would-i-choose-it-again/" target="_blank" rel="noopener">here</a>. So to develop Node.js app I choose Express.js
The main reason is that it's the most light-weight and at the same time perfectly ready to develop with. Just FYI, there are other options, like Sails.js, which is big MVC brother, Meteor.js, which promise you to give a smooth experience not only on backend side, but on mobile and desktop as well. There is also Socket.io, which I never used, but by the description, it makes a lot of sense when your main focus planned to be on WebSocket connections, which I won't use in my future app.
So, for pure lightweight API Express.js still would be the best fit. Let's move on to its installation, which is pretty straightforward.
<code>npm install express --save</code>
Flag --save means that you save the package into your cache folder, which is <code>node_modules</code> in your app folder.
I suggest installing ESLint at the start. If you're familiar with linting tools - it's just the one for JS (both BE and FE). If you don't - it's a tool which checks your code for basic style violations. In any production project it's crucial to have one, but for the pet project it would also make sense, at least to just force yourself to follow good practices. So, you can install it using <code>npm install eslint --save-dev</code>. Flag <code>--save-dev</code> means that the package will be saved locally, added to the project dependencies, but only into the development environment. Now let's configure ESLint, perform <code>eslint --init</code> (if you're using Fish shell, you may have an error <code>fish: Unknown command 'eslint'</code>. In this case, as a workaround just run <code>node_modules/.bin/eslint --init</code>). This command will create a file <code>.eslintrc.json</code> which you also can modify in the future. During the installation it will ask you few questions, here are my recommended answers:
{% highlight shell %}? How would you like to configure ESLint? Use a popular style guide
? Which style guide do you want to follow? Airbnb
? Do you use React? No
? What format do you want your config file to be in? JSON{% endhighlight %}
Ok, we're about ready to start writing our first app. Do you remember when doing <code>npm init</code> it asked you about your entry-point file and we specified <code>app.js</code>? So now it's time to create this file with such code:
{% highlight javascript %}const express = require('express');
const app = express();
app.get('/', (req, res) => {
  res.send('Hello World!')
})
app.listen(3000);
{% endhighlight %}
I expect that you have a basic understanding of JS syntax, so the first couple of lines should be descriptive but their own. With `app.get...` block we're defining a root route. Function `get` (as well as put, post and delete) expects two arguments: query-string and callback-function. The first attribute is your HTTP route, it can be just '/' as a root route, or smth terribly-long, like 'users/:user_id/posts/:id', so it means that application server will be listening to such URL path and once called will invoke the callback function. If you're familiar with JS you may found that callback function is written using ES6. It's rather new ECMAScript standard, which is widely used now in both FE and BE JS app development. And the style we're using here is named <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions" target="_blank" rel="noopener">Arrow-function</a>. So this callback function accepts two parameters, request object and response object. From the first one you may take different input attributes, like params, headers etc. To the response object you'll send your response in (normally) JSON format. The last line says that app server should be started on the port 3000.
It's time to check it: <code>node app.js</code>. It won't give you any response because pure ExpressJS has nothing to tell you about itself xD But once you performed this command - go to your browser and open <code>http://localhost:3000/</code> and you'll see computer saying hello. Now you can go back to the terminal and make <code>ctrl+c</code> to kill this speechless thing.
The first aspect you may want to improve is to add some kind of response to your console when you run a server and receive a request.
Basically, it's two separate questions. Let's first solve the one when you want to have a message on application server start. It can be solved pretty easily. Just pass a callback to your <code>app.listen</code> function:
{% highlight javascript %}app.listen(3000, () => console.log('Master, I started the server for you!'));
{% endhighlight %}
Empty brackets here needed also for Arrow function. So oldie way to write it would be:
{% highlight javascript %}app.listen(3000, function() { console.log('Master, I started the server for you!') });
{% endhighlight %}
A bit extra verbose, huh? So if we have ES6 expressiveness, let's use it.
The second thing we may want to add is requests logging. And this may be performed by another plugin, Morgan. To install it run <code>npm install morgan --save</code>. Then in your code add following lines:
{% highlight javascript %}const morgan = require('morgan')
app.use(morgan('dev'))
{% endhighlight %}
after your <code>const app = express();</code> line.
Now start the server. First, you'll see the welcome message right after your server successfully started. Then go to browser and request <code>http://localhost:3000/</code> again. Check the console, you'll see smth like <code>GET / 304 4.515 ms - -</code>, which says that your request returned status 304 and took 4ms to complete. Now try to request smth, we don't have yet, f.e <code>http://localhost:3000/blerg</code>, go to the console and you'll see there <code>GET /blerg 404 3.069 ms - 142</code>.
One thing could look unfamiliar for you so far is <code>app.use...</code> line. It means that we add middleware Morgan to our app. I'll cover this topic more thoroughly in the next NodeJS post. For now let's remember that we have linter, which must keep our code clean if we cannot and let's go to console and perform it there: <code>eslint .</code>. It will tell you that you have few errors, because I intentionally skipped some semicolons. This tool wouldn't be so widely used if it would not help us fixing all the errors (sarcasm, it CAN help you, however couldn't fix all of them). Run <code>eslint . --fix</code> and it will fix errors it found for you. You'll still have one warning about console.log statement, which is kinda ok %)
This is it for now. In the next post we will get more familiar with "Middleware" concept and build a basic REST API.		
