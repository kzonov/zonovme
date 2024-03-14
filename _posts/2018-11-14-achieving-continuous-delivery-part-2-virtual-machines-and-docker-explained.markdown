---
layout: post
title: Achieving Continuous Delivery. Part 2. Virtual machines and Docker explained
date: 2018-11-14 21:43:53.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Development
- DevOps
tags:
- containers
- continuous delivery
- continuous integration
- development
- devops
- docker
meta:
  _edit_last: '1'
  _adinserter_block_exceptions: ''
  hefo_before: '0'
  hefo_after: '0'
  _aioseop_description: Even if you heard the words "Docker" and "containers" and
    even have an understanding of their place in the world, this article will help
    you to build a more solid understanding of these concepts.
  _aioseop_title: Virtual machines, containers, and Docker explained
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/achieving-continuous-delivery-part-2-virtual-machines-and-docker-explained/"
excerpt: "\n\t\t\t\t\t\t"
---
How are you doing, fellow developers? Ready to dockerize your first application? Or what about finding out, what
"dockerize" actually means? Let's start!

The structure of today's post will be the following:

1. Understanding the Virtualization
2. How containers are different from Virtual Machines
3. Introduction to Docker concepts

---

## Virtualization

![Virtualization](/assets/2018/11/photo5427257478980151812.jpg)
_Photo by NeONBRAND on Unsplash_

The idea of containers is not new, it had been around since the late 90s. It is a successor of *virtualization* and
*virtual machines (VMs).* If you have a MacOS (or Linux) as your home OS for long enough, most likely you ever tried to
run some Windows-based applications on it. Especially it was important when Steam wasn't around or when you migrated
from Windows and had a bunch of games and applications, which you got used to and wanted to run. In that situation, you
had a choice. Either install Windows as a second operating system or use it in a Virtual Machine. If you have been in
this situation, most likely you know some of the most famous VMs, like VirtualBox, Parallels Desktop, VMWare. You
usually install them on your host OS. To show an inverted example, I can recall the times when I was getting familiar
with Linux and was running a Mandriva distributive in a VirtualBox VM, running on Windows.

From the described setup we can now derive the understanding of what VMs are serving for. It helps you to run two or
more different OSs *in isolation* to leverage the power of both. Importantly, as you might have noticed, in this setup
you can run any combination of host OS and guest OSs. Furthermore, virtualization is not only useful for running your
old Warcraft III Windows-based game on the MacOS, but it is also heavily used in the world of servers. The simplest
example here can be some cloud provider. If you ever registered your *virtual private server (VPS)* on DigitalOcean, or
an application on Heroku, or an EC2 machine (in some configuration) on AWS, remember, you were not choosing a real
server from the server rack. Because I doubt that they would have a server with 512Mb RAM, but you still can choose it!
You probably already started to understand that they are using the virtualization to create a server for you. Yet, the
approach they take is a bit different than in the first example with games. They don't have a MacOS/Linux/Whatever OS,
on top of which they create a virtual machine for you, it would be inefficient. Instead, they use a different level of
virtualization. It is called the *hardware virtualization.*

Now it's time to introduce one more important concept. It is the *Hypervisor*. I think that this word sounds very cool
%)

![Two types of Hypervisors. Made by
https://advtka.myportfolio.com/](/assets/2018/11/photo5429509278793836878.jpg)
_Two types of Hypervisors_

Hypervisor is a software, that manages Virtual Machines. In case of the VirtualBox, Parallels Desktop and VMWare that
tools will play the hypervisor role. Using them, you can create a requires type of VM, allocate needed resources, watch
it, and remove it. The name of these and similar tools is **Type-2 Hypervisors**. Unlike the Type-2, the **Type-1
Hypervisors** run on bare metal and don't require any OS installed. Exactly they are used in the VPS/EC2 case described
above. One of the well known Type-1 Hypervisors is Xen. Here is an illustration of both types:

![Hypervisor types illustration](/assets/2018/11/docker.png)
_Hypervisor types illustration_

To be precise, I have to tell, that EC2 supports both virtualization types, Type-1 (hardware) and Type-2 (software or
paravirtualization). You can find more information about their setup in the links at the end of the post.

## Containers

The important feature of VM is that it contains everything required for its work inside itself. It allows you to run a
Windows VM on your Ubuntu machine and vice versa. Thus it means that each VM includes the required kernel and packages,
hence all these need some disk space and RAM. But at the same time, VMs help you to use the power of a machine on its
max. Containers work in a different way. Let's have a look at the illustration:

![Virtual Machines and Containers illustrated](/assets/2018/11/docker-2.png)
_VMs and Containers illustrated_

Containers share the host OS's kernel and so they need less disk space to exist and less RAM to run. However, on the
other hand, they cannot support completely different kernel types. Although I saw an option to run Linux containers on
Windows Server, it still uses a virtualization, as the Hyper-V underneath. I didn't see the vice versa solution,
however, it must be possible with the same approach.

Most likely you know the company and product Docker, which is a standard de-facto for the modern containers. However,
they weren't pioneers on containers space. The initial implementation had been created in 1982 and was called `chroot`.
It is not as comprehensive containerization technology as we see them now, but the idea is very similar. This command
allowed you to use different root folders for the different processes, thus providing a file-system "isolation". I put
the word "isolation" in quotes because it wasn't a real isolation. E.g. in Docker if you give your container access to a
certain directory, it is impossible by the Docker design for this container to access other parts of your host file
system. However, in the case of the `chroot` there was this possibility because it didn't have the root user scope
isolation.

I mentioned about VMs that using them, you can leverage the whole power of the machine, because of a strict resources
allocation. For containers, it works differently. Speaking about Docker, by default it doesn't limit your containers
from blindly consuming the whole pool of resources, including Ram, Filesystem, and CPU cycles. However, there is a
built-in mechanism, which allows you to set the hard constraints on the resources usage.

I will emphasize this once again. It is also very important to understand that even tho your processes are isolated
(contained) with Docker, they are still running on the same kernel.

## Docker

As I already said, Docker is not the first and the only one on the containerization market. It is the most common
containerization solution nowadays, but, everything may change in a few years, as it always happens in our fast-paced
world. Even now I can point you to the number of [existing containerization
solutions](https://en.wikipedia.org/wiki/Operating-system-level_virtualization#Implementations). Anyway, learning Docker
today makes the most sense. You should already have an understanding of the purpose and approach, which Docker uses to
deliver the containerization, and now let's define the basic concepts, what **images** and **containers** are.

**Image** - is an executable package that includes everything needed to run an application. Like the code, libraries,
environment variables, and configuration files.

**Container** - is a runtime instance of an **image**, which the **image** becomes when is executed. In other words, it
is an image with the state. Containers can be running or stopped.

It was easy, and now we're ready to have a closer look at the Docker internals. These should be valid now, based on
public information, however, it is a product, which keeps evolving. Thus some parts might become obsolete or change in
the future.

![Docker architecture](/assets/2018/11/docker-3.png)
_Docker architecture_

The cornerstone of everything is the **OS's Kernel**. It is your Unix-system's kernel, as mentioned above, it is used by
every container.

The second level, **Docker Engine**, is not just one tool. It is a big set of tools, which e.g. provide CLI to the end
user, and RestAPI for the containers to talk to its daemon, **dockerd**. Moreover, it manages the network calls and
isolated file systems for containers. Also, when you hear Docker Enterprise, it is actually the Docker Engine
Enterprise.

The next level is **containerd**. Containerd is a container manager. It manages containers' supervision, destruction,
and creation, based on the images. Also, images management, like pull/push is also implemented on this level.

And the fourth level is **runc**. It is exactly the runtime tool, which runs containers, which are OCI (Open Container
Initiative) compliant. Which basically means that runc potentially can be replaced by other tools, which follows the
same standard.

Next concepts are sort of optional, however, you most likely will hear about them in the future.

Docker Compose is a tool, which helps you to orchestrate a set of containers. Docker recommends the strategy that
containers should be as atomic as possible, so perform one specific functionality. E.g. if you are running a
microservices-based web application locally, you most likely would want to spin up at least 3-4 containers. Like
microservice-1 (probably more than just one), database, message queue and probably something else. And docker-compose
exists exactly to help you manage all them at once. Literally with one command, like `docker-compose up` and that's it.

Docker Swarm is an even more optional tool, and it is actually also a part of the Docker Engine. This tool is dedicated
to Docker clusters management. So, if you heard about AWS ECS or Kubernetes, they serve the same goal as Swarm, and
moreover, more popular nowadays.

## Why Docker?

Now, when we understand the Docker's place in the ecosystem, it is time to explain, why companies choose Docker and why
should you.

The first reason is the development mode usage. When you're working in a microservices architecture, usually you cannot
work with just one microservice up and running. In my experience, you also need a message queue, a database, most likely
another microservice, and chances are high that it will also have a different database. Like in E-Commerce website you
store your categories and products in a relational PostgreSQL for the Products microservice and in, e.g. MongoDB in
Orders microservice. If don't use Docker, you must have these two databases on your machine, RabbitMQ installed and
running and both microservices functioning at the same time as well. Can you imagine, how worse it may become if one of
those is written in Ruby and another in Java? Brrr...

With Docker, and in this case Docker Compose, you will need just one command to spin up everything, that is required to
your development setup. Sure, configuring it initially is not a trivial task, however, it is more than real and won't
require years to build. In the future articles, we will develop a similar setup together.

The second reason is the production mode usage. Docker containers are stateless and contain everything required to run
it. If in the old setup your deployment script had to ssh to your remote server, fetch the latest changes from git,
perform the `bundle install`, and then restart a server, now you just replace one container with another. Images for
these containers are built on your CI tool, which doesn't require any human interaction. This setup is about Ruby-based
projects, but it works the same for languages like JavaScript or Python. I could assume that for compiled languages,
e.g. Go, it is a less problematic process, however, Docker brings advantages for them as well.

The third, yet probably the most important is a production-development similarity. When you're using Docker, it is much
easier to keep your development mode as close to production as possible. No need to use SQLite instead of PostgreSQL,
and it is easy to spin up a microservice instead of mocking its behavior. Thus, you get a less error-prone development
process, because many production-only errors will be catchable already in development.

It is not a comprehensive list, using Docker you can improve the security of your production system, make it easier to
scale, and just provide you a clear definition of your project infrastructure dependencies in one place. It definitely
worth your time, once you try it full power, you will never want to work with the old, "pre-docker"-era, setup anymore.

## The End

Initially, I planned to include the hands-on part in this post, but it has grown already too big. Thus I dare to ask a
little patience, we will start with the dockerization very soon and you will find out that it is much easier than you
might think.

For today this is it; obviously, it is not the closest-possible look at Docker architecture, you can dive even deeper
following the links below. However, the information provided here is more than enough to feel comfortable with the
theoretical concepts.

Hope to see you next time! I will appreciate if you want to buy me a coffee, using a big orange button below or just
share my post.

## References and further information:

- [https://www.youtube.com/watch?v=wW9CAH9nSLs](https://www.youtube.com/watch?v=wW9CAH9nSLs) - first exposure of Docker to the world
- [https://www.youtube.com/watch?v=kwsJIH5TGIg](https://www.youtube.com/watch?v=kwsJIH5TGIg) - Docker webinar. Great explanation, but an awful audio
- [https://blog.docker.com/2017/09/preview-linux-containers-on-windows/](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/) - Linux-based Docker containers on Windows
- [https://en.wikipedia.org/wiki/Operating-system-level_virtualization#Implementations](https://en.wikipedia.org/wiki/Operating-system-level_virtualization#Implementations) - different OS-level virtualizations
- [https://docs.docker.com/config/containers/resource_constraints/](https://docs.docker.com/config/containers/resource_constraints/) - Constraining the containers' resources for Docker
- [https://github.com/darshanime/notes/blob/master/kubernetes.org#notes](https://github.com/darshanime/notes/blob/master/kubernetes.org#notes) - Some synopsis from Kubernetes docs, where the containerization part is described
- [https://caylent.com/containers-kubernetes-docker-swarm-amazon-ecs/](https://caylent.com/containers-kubernetes-docker-swarm-amazon-ecs/) - Docker Swarm, Kubernetes, and AWS ECS comparison
- [https://blog.docker.com/2015/06/runc/](https://blog.docker.com/2015/06/runc/) - runc introduction by Docker
- [https://blog.docker.com/2017/08/what-is-containerd-runtime/](https://blog.docker.com/2017/08/what-is-containerd-runtime/) - Containerd introduction by Docker
- [https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/virtualization_types.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/virtualization_types.html) - Virtualization types used at AWS EC2

# Other chapters:

1. [Achieving Continuous Delivery. Part 1. What are the CI/CD?](http://zonov.me/achieving-continuous-delivery-part-1-what-are-the-ci-cd/)
