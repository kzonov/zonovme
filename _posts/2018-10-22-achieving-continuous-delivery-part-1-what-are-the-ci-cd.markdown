---
layout: post
title: Achieving Continuous Delivery. Part 1. What are the CI/CD?
date: 2018-10-22 22:17:14.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Development
- DevOps
- Management
tags:
- aws
- CI/CD
- continuous delivery
- continuous integration
- development
- devops
meta:
  _edit_last: '1'
  _adinserter_block_exceptions: ''
  hefo_before: '0'
  hefo_after: '0'
  _aioseop_description: Continuous Integration, Continuous Delivery & Deployment explained.
    Tools, processes, tech and buisiness value described.
  _aioseop_title: Achieving Continuous Delivery. What are the CI/CD?
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/achieving-continuous-delivery-part-1-what-are-the-ci-cd/"
excerpt: "This will be the start of the series of blog posts with the title “Achieving Continuous Delivery”, which I plan to write about in the upcoming few months."
---
This will be the start of the series of blog posts with the title "Achieving Continuous Delivery", which I plan to write
about in the upcoming few months. Topics I plan to cover are:

1. What is the difference between CI and CD? Two meanings of CD. What is the business value in it? The tooling we will
be using in the series
2. Dockerization of a basic application
3. Dockerization of an application, which has external dependencies (we will have just a database)
4. Build service preparation. I will definitely cover TravisCI and probably some others for a comparison
5. Preparing Staging and Production environments, using Heroku and integrating them into the CI pipeline
6. Getting closer to the Continuous Delivery with feature toggles

After that, if I will still have an interest, I will also cover the AWS way of going from classical ELB+EC2 to the
containerized approach, but won't commit for this for now. Also, as you know, along the way people tend to change their
mind and forgotten steps may appear or in the opposite, some steps will be too easy, so they will be merged with one
another. Otherwise, all our estimations would be perfectly precise %)

However, let's get started with the definitions, to understand, what we're trying to tell and achieve when we're talking
about CI/CD.

![Bakery pipeline. Photo by Mink Mingle on
Unsplash](/assets/2018/10/mink-mingle-185523-unsplash-small.jpg)

CI stands for *Continuous Integration*, which tells us that something should be, khm, integrated... Continuously, yeah?
Ok... But what does that actually mean? Before explaining it, let's have a look back in history. A long long time ago
when computers were big, no MacBook Pro existed and the dominant apps were desktop based (and some people say that
dinosaurs were still hanging around), the usual release process looked like that.

![Traditional deployment process. Made by
https://advtka.myportfolio.com/](/assets/2018/10/IMG_20181022_214240_384.jpg)

If you ever heard of Waterfall approach - here it was. Long months analytics were getting requirements together and
writing technical specifications. Then even longer months developers were developing a new version. Then shorter, but
still months QAs were testing this new version and finally in the best case your software just got updated.
Unfortunately, despite that these processes are ancient, they still work in the "modern" web-oriented world in some
organizations. But they don't deserve to be covered in articles anymore.

Fortunately, at the beginning of the 21st century, some bright minds created the so-called Agile Manifesto. It was and
still is a big thing. However, the ideas were around since the early 1950s, when they emerged in the NASA. I will not be
describing the Agile Manifesto here, it is easily googlable, as well as all the following methodologies like Scrum and
Kanban (which actually also had been created much earlier and weren't first applied in the Software Development
industry). What is important for us here, is that Agile was created to speed up the development process and shipping
time. For us, for engineers, it means that the old software delivery approach doesn't work any longer and different
processes must be implemented and different tools and artifacts must be used to achieve that pace.

Teams started doing releases once a sprint (usually 1-2 weeks), having Production and Staging server. Some teams might
also become cross-functional, like doing testing without having QA specialists, having no formal Team lead and so on.

However, the world isn't "that slow" anymore, and companies should react on the market within days, not weeks, so smart
minds started thinking further. And a *concept* named Continuous Integration (CI) had been introduced. To be honest, it
is also not a new approach, it is around since the end of the previous century, it just got its popularity peak in the
past decade. In this approach, teams should be creating production-ready artifacts (builds, images, binaries) not once
per Agile sprint, but multiple times a day, and that artifacts must be easily releasable as well. So, special build
servers got popular. Among them are [TravisCI](https://travis-ci.org/), CircleCI, Jenkins, [DroneCI](https://drone.io/)
and many others. There is a brand new tool, Github Actions, but its functionality spreads beyond the classical CI/Build
service. This is how the new release process started looking like:

1. Developer creates a Pull Request from the feature branch to the master branch
2. CI/Build service runs lints, tests on this PR
3. Developer deploys his/her code to the Staging server from the feature branch
4. If everything is alright there, Developer merges code to the master branch and deploys it to the Production
5. Done, ready to start working on the next feature

This is a basic process, which is adopted by most of the companies, however, cannot be fully named "Continuous Delivery"
(CD), despite that it usually is ;) Those, who can invest more time and effort in development tooling and scalability,
choose a smoother approach:

1. Developer creates a Pull Request from the feature branch to the master branch
2. CI/Build service runs lints, tests on this PR
3. CI service builds an artifact, which can be a binary if you're working with compilable languages, or zip file if
you're building a Lambda function, or a Docker container in the general case
1. Depending on your processes, CI service may also automatically deploy the artifact to the Staging server(s).
4. If everything is alright there, Developer merges code to the master branch and the CI tool deploys it to the
Production
5. Done, ready to start working on the next feature

This second example may look a bit complicated, it is all because we have combined here all three concepts: CI, CD, and
CD. Ha-ha, two CDs, yeah. In general that example represents the *Continuous Delivery (CD)* process when it, by itself,
contains *Continuous Integration (CI)* and *Continuous Deployment (also CD).* Confusing, huh? Let's write it down in a
more structured way.

*Continuous Integration (CI)* - means that a team achieves a quick process of merging Pull Requests to the master
branch. And while PRs usually cannot be merged without having a review done, the CI tool's part in this process is to
simplify the review, by ensuring that tests are passing, linter found no style violations and, if you also covering this
part, test coverage or code quality, in general, hasn't dropped down. Tests here can be based on any testing framework
and any testing level you have, like RSpec, Jest, TestCafe. Some examples of linting tools are
[Rubocop](https://github.com/rubocop-hq/rubocop), [ESlint](https://eslint.org/). Test coverage is usually measured by
[SimpleCov](https://github.com/colszowka/simplecov) and [IstanbulJS](https://istanbul.js.org/), and the code quality by
e.g. [CodeClimate](https://codeclimate.com/). Main languages in my toolkit are Ruby, JS and Go, so if you're using
languages different from those, libraries and tools may also be different for you.

*Continuous Deployment (CD) -* means that not only PRs are frequently merged into the master, but also production
deployments are frequent, multiple times a day. And the CI tool creates a production-ready artifact, which developer can
deploy within a button click. As I already mentioned above, an artifact may be represented by a different entity here.
In general, it is a Docker image. However if you're deploying an AWS Lambda function, it will be just a zip file for
you. If you're using a compilable language, like Go, just a binary might be sufficient.

![Continuous Deployment process. Made by
https://advtka.myportfolio.com/](/assets/2018/10/IMG_20181022_214236_793.jpg)

*Continuous Delivery (CD)* goes even further and says that the developer shouldn't spend her/his time by clicking some
button. Once smth is merged to master - it should be deployed to the production ASAP and automatically.

If you got used to "slower" delivery processes, you may be wondering, how the hell are those companies can exist, when
they don't even have a thorough testing phase and proper "looking at that graph dashboard and checking for spikes" phase
after deployment? That's why there are some prerequisites for going all-in CD:

1. Extensive test coverage. And I'm talking here about a proper pyramid, where you have not only a solid unit-tests
level but also important functional tests and sufficient amount of end-to-end integration tests.
2. Monitoring and pager-duty in place. Your application should have monitoring on all the required levels. Starting from
hardware (if you use on-premise servers), to resources monitoring (like CPU and memory usage), to request/response time
measurement, to error count. And you should not only measure all these but have a [tool](https://www.pagerduty.com/),
which can urgently notify you if smth goes wrong.
3. Pager duty reaction processes. Even if you have a shiny modern tooling everywhere, but the developers just cancel a
PagerDuty call when an incident happens on the Production and continue drinking their beer in a pub - everything is in
vain. The cornerstone of the resilience here is to have reaction processes in place.
4. Backups and rollback mechanism. We don't live in a perfect world where unicorns are bringing us all the rainbow, joy
and happiness. It means that even with the perfect test suite, monitoring, quality and so on, you may still have
situations when you have to rollback. And there should be a mechanism and process for that. And just to mention, right
now there is a major Github outage happening
([https://www.theinquirer.net/inquirer/news/3064898/github-down-major-outage](https://www.theinquirer.net/inquirer/news/3064898/github-down-major-outage)),
which shows the necessity of backups. Their case is not about deployment, just the outage lasts for almost 24hrs now,
tough time for guys at Github.

One more thing to keep in mind when going CD is rolling update. Most likely if you're using tools like
[ECS](https://aws.amazon.com/ru/ecs/) or [Kubernetes](https://kubernetes.io/), you're already leveraging it. It
basically means that you don't deploy to all your nodes in the cluster at once and just in case if smth went wrong on
the newly deployed node, your old ones are still operating and only if everything is alright - they also got updated.
This is a separate concept and so we will not cover it in this article, but still worth to keep it in mind.

# Conclusion

As usual, the idea of CI/CD is simple, but if you want it to not damage your business, you have to implement it step by
step, keeping in mind the prerequisites and caring not only about tooling but also about processes and mindset. In the
following articles, we will dive deeper into the hands-on parts of it. Subscribe or follow me on Twitter to stay tuned!
