---
layout: post
title: How to install and use PostgreSQL (or whatever you want) using Docker
date: 2017-10-08 15:45:05.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Development
tags:
- beginners
- devops
- docker
- tutorial
meta:
  _edit_last: '1'
  _wp_old_slug: how-to-dockerize-database-or-whatever
  _aioseop_description: How to install the database (f.e. PostgreSQL) using Docker
    Hub.
  _aioseop_title: How to install and use PostgreSQL using Docker
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/how-to-install-postgresql-using-docker/"
excerpt: "Today I'll talk about really trivial thing, but unexpectedly I found myself living without it for a while, but now I started using it and cannot stop anymore %)
The thing is Docker."
---

Today I'll talk about really trivial thing, but unexpectedly I found myself living without it for a while, but now I started using it and cannot stop anymore %)
The thing is Docker.
<!--more-->
For those who lived past years on the Moon and have no idea what is it - short explanation. Docker is a tool, which helps you run some application(s) in an isolated environment <em>(container).</em>
It means that it doesn't pollute your global directories, namespaces and if you want - you may have various versions of one app in different containers. F.e. you have to maintain two versions of Redis because one of your microservices uses really out-of-date version and here Dockers is your best friend. All this speaking about ready to use containers, but you also can create your own containers with your app/microservice. It becomes really helpful when you want to give backend to your frontend developers and don't want them to struggle with Ruby version managers, image magics, and other pesky tools.

<img class=" wp-image-369 alignleft" src="{{ site.baseurl }}/assets/2017/10/docker-269x300.jpg" alt="" width="354" height="395" />

In the current article, I'll give a short introduction of how to install a DB locally using Docker containers hub. I think I'm late for about 3 years with this topic xD But I think there are still plenty of people who don't use this approach and probably it will be the last drop for them and they'll start using it.
<h2>1. Install the Docker itself.</h2>
Just go <a href="https://docs.docker.com/docker-for-mac/install/#download-docker-for-mac">here</a> to the official website and get the stable version. I found it's the easiest and the most convenient way to install it. It installs like any OSX app. Just don't forget to open it after install and you can also make it auto loadable on the system start up.
<h2>2. Install the DB.</h2>
I use <a href="http://zonov.me/postgresql-transactions-isolation-levels/">PostgreSQL</a> on one of my projects <em>(honestly I'd like to use it almost everywhere)</em> and will show you how to install and use it with Docker. Actually, on my daily job, I have Mongo, Postgres and Redis as databases and it makes it really convenient to use them together with the Docker.
So first we go to the dockerhub to find the suitable image, <a href="https://hub.docker.com/_/postgres/">here it is</a>. Installation is straightforward:
{% highlight shell %}$ docker pull postgres{% endhighlight %}
It takes some time to download the image, be patient. Then to start it you can simply run:
{% highlight shell %}$ docker run --name postgres -d postgres:latest{% endhighlight %}
Where --name specifies the container name, you can write whatever you want here, <code>-d</code> specifies the image and the <code>latest</code> in my case means that I want to run the latest available version of the image, if there will be a new one on Dockerhub, it will be downloaded.
<h2>3. Managing the container</h2>
Here are few commands you need to know to work with Docker:
How to list all running containers:
{% highlight shell %}$ docker ps{% endhighlight %}
There you can see only those containers which are running at the moment. If you need to see all registered containers - simply add the flag <code>-a</code>:
{% highlight shell %}$ docker ps -a{% endhighlight %}
It can be useful for starting containers after system restart. Let's look at the most important attributes we have there:
<code>CONTAINER ID</code> - your container id, I'll show later how to use it to start and stop containers.
<code>STATUS</code> - here you may see is your container running or not.
Let's now stop and start container again:
{% highlight shell %}$ docker stop{% endhighlight %}
{% highlight shell %}$ docker start 0714498eefff{% endhighlight %}
As you understand, <code>0714498eefff</code> is my container id.
Checking logs is just as elementary:
{% highlight shell %}$ docker logs 0714498eefff{% endhighlight %}
It can be really useful if you have a problem with container start, I personally had it few times.
<h2>4. Ports mapping</h2>
The last thing you have to know when starting using a Docker is port mapping or port exposing. When you start a docker you can see some port displaying to you, but keep in mind that it's an internal port, you cannot connect to it. To be able to do so you have to explicitly map it to your machine's port.
Let's first remove our container:
{% highlight shell %}$ docker rm <span class="s3">0714498eefff</span>{% endhighlight %}
Then start it again but with port mapping:
{% highlight shell %}$ docker run --name postgres -d postgres:latest <span class="com">-p 5432:5432</span>{% endhighlight %}
Hope you'll have a fun time with it!		
