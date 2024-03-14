---
layout: post
title: About customer-centricity in Infrastructure/Platform team
date: 2019-09-06 08:50:34.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- DevOps
- Management
tags:
- devops
- infrastructure team
- retrospective
- tutorial
meta:
  _edit_last: '1'
  hefo_before: '0'
  hefo_after: '0'
  _wp_old_date: '2019-09-05'
  _aioseop_description: The post covers my journey and journey of the team I worked
    with from the frustration of not having a goal and lack of feedback to getting
    it back and changing the approach to work.
  _aioseop_title: About customer-centricity in Infrastructure/Platform team
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/about-customer-centricity-in-infrastructure-platform-team/"
excerpt: "\n\t\t\t\t\t\t"
---
Dear readers, in this post I will not touch upon the most technical topics, but the one I gonna talk about is important
for many engineers. Customer centricity has two main advantages. First of all, while being a customer-centric, you
always know that what you do has the greatest positive effect on the product. Secondly, you more often get positive
feedback from your colleagues or users, which is very valuable both for improving the product and for personal
development. If you are one of those developers who just want to work on technical tasks, without thinking, for whom and
for what purpose they are - then perhaps this post is not for you yet, although I think it will take several years and
you decide to rethink your approach. For those who already understand that customer centricity might be something
valuable for an engineer, let's get started!

*Before we start, there is a TL;DR. section at the end of the article. If you find it too big - you're welcome to jump
ahead =P*

## A few words about my way

I am a developer with over ten years of development experience. I have been in various roles, from a young trainee
semi-sysadmin/semi-developer (feel the DevOps spirit, huh?), to a CTO. For most of my career, I have worked in product
teams. It was mostly Backend development, although at different periods of life Frontend was my close friend too.
However, it’s still important to note that the work was with the final, customer-facing product. This means that having
developed some kind of feature, usually I immediately saw how users react to it. Either this happens during the a/b
testing, or already with a full release. I saw traffic, I saw users going through our registration forms, doing actions
on the site, making purchases and, which is not that often, but very valuable, leaving reviews.

Not that long time ago I delved into the backest back of the backend, I switched to working with Cloud Infrastructure.
And this means that now my work is very abstracted from what the end-user sees. And even my product manager is not the
manager of the final product, but only our infrastructure platform. Below I will tell you in more detail what unexpected
frustration I encountered in this role.

## Why does customer-centricity that important

As I mentioned above, I strongly believe that every company and every engineer should be customer-focused. Henry Ford
Quote "If I had asked people what they wanted, they would have said faster horses" has its place in history and could be
used to refute my claim. However, this approach only emphasizes Ford's customer-centricity, because although he didn’t
“ask people”, he created something for them, saw their positive reaction, improved and continued building it further.

While working in a product team it is quite easy to be customer-oriented, especially in small companies. You see product
metrics and have a firm grasp of the causality between features that you release and how better they can solve user
needs. There are a lot of ways to find out whether you are digging in the right direction. From everything that happens
before development, such as user interviews, UX research, to metrics in your Google Analytics or, which is closer to us,
the ELK stack.

Yet let's try to figure out why being customer-oriented should be important for you, as an engineer, and your team.

1. We all love challenges! And satisfied users => more users => more complex tasks.
2. We do not write code for the sake of code. We do not write code to keep processors warm and impact global warming. We
don’t write code to admire its beautiful design, or the coolly chosen variable names, or the fact that the request is
now executed not in 100ms, but in 10. Although I will not dissemble, this is all mind-blowing for any engineer. But we
must understand that we are part of the business, and it has a specific goal - to satisfy its users' needs. Therefore,
it's also OUR goal to help users.
3. If external motivation is as important for you as for me, the feeling of satisfaction that someone likes the result
of your work is a strong motivator. By the way, this is one of the reasons why I write on a blog, so every comment and
sharing in social networks is very important to me =)
4. When all team members and all teams understand that the main task is to build the best solution for the end-user,
then all go in the same direction, rather than everyone trying to pull a blanket over themselves.
5. Well, and in the end, even the DevOps philosophy requires us to be customer-oriented. We will talk about this below.

## DevOps

First, a small digression to familiarize yourself with (or recall if you are familiar with) what DevOps philosophy has
to say on customer focus. If we turn to The Project Phoenix and DevOps Handbook, the

idea of ​​The Three Ways goes through them. The Second Way talks about Improving the Right to Left Feedback Loop.

If the second book of DevOps Handbook focuses on the technical side, such as alerts, then the first describes the
aspect, which is closer to the content of this article. They talk about a feedback loop from customers and more
generally, users of your services. In the case of a platform or infrastructure team, these users are product developers.

I will not delve deep into retelling the Project Phoenix book, it's available on Amazon for you to get to know it. Hope
that this small digression was enough to get the idea.

## The story of my frustration

Having worked for half a year in my current team, I realized that I overcame the main technical difficulties. There was
still a lot ahead, but my body stopped producing that hormone of pleasure that was there before. I refactor a huge
module in Terraform, I make the connection to our staging infrastructure more secure, I contribute to open source. I get
satisfaction from this, I solve complex problems. But I don’t see that what I do affects the end-users of the company's
product!

Moreover, I do not receive feedback even from our direct "customers" - from product developers. I had to urgently make a
move.

To fix the situation, I took a number of steps, let me tell you more about each below.

1. Collected feedback from all major external stakeholders.
2. Organized a workshop where we reviewed this feedback and decided what our areas of action should be.
3. After the results of the workshop, we reorganized the team.

Now, as promised, I’m telling you more about each stage.

## Feedback from the stakeholders

In an ideal situation, this should be an ongoing process, otherwise, it can barely be called Feedback LOOP, as DevOps
takes it. Once a month, you can get together with Engineering managers or Team leads of other teams and hold a classic
retrospective, discussing why they want to thank your team and what aspects of the interaction can be improved.
Moreover, the part with gratitude should be as public as possible, while the part with the discussion of improvements
can be carried out in a more limited circle.

However, in a situation like ours, when we just decided to try this practice, the approach should have been somewhat
different. I have compiled a list of key stakeholders that our team has interacted with over the past months. These were
several Engineering Managers and Senior Engineers from other teams. I asked each of them the following questions:

1. What is the purpose of your team? What its global goal for the lifetime, or this year, or this quarter?
2. How do you think our team can serve to help you to fulfill your purpose at best?
3. What type of interactions would you prefer with our team?

If I received more or less distinct answers to the first two questions, then to the last I had my prejudice about how
our team should work. I was 100% sure that this is how the rest of the teams will see our interaction. Imagine my
amazement when I saw that opinions on this subject are drastically polarized. Some of the respondents wanted to see us
exclusively as a service team, for which it was enough to create a service ticket and we would do everything. Another
part wanted to do everything by themselves so that our participation was only at the consultation level.

In this article, we will not analyze these two types of interaction with an infrastructure team, but in my opinion, this
is a very interesting exercise on its own.

## Workshop structure

For the workshop, I created preparatory material for the team members and drew up the agenda. Because there is nothing
worse than a meeting, where the participants first time see the information and schedule only at the meeting itself. In
the preparatory material, I included the received feedback, Engineering Vision (kind of engineering-specific goals) and
Product Priorities.

In your case, it can be any relevant lists and documents. The main thing is to try not to overload people with
information during preparation. Otherwise, if reading your lovingly prepared documentation takes an hour, nobody will
have the strength to read it (not to mention the preparation). In my case, it was about five pages of very loose text,
which might already be too much, but my team heroically read through it =)

I will not focus on the workshop itself, you can structure it as you like. In our case, it was 5 stages:

1. Joint familiarization with input sources.
2. Based on our vision and the vision of our colleagues, we have compiled five phrases/sentences/mottos that describe
the principles of our work as we want to see them.
3. Exercise of a magic wand. Based on the results of this stage, we made 3 ideas about how our team should look
like/work if we live in a fairy tale filled with magic and small unicorns.
4. We have compiled a list of more achievable projects that bring us closer to this ideal state, which can later be prioritized and put to work.
5. In the end, we had a short retrospective on this workshop.

During the workshop, we used a variation of the [KJ approach](https://articles.uie.com/kj_technique/), but you can use
any process convenient for your team.

## Structural changes

The last thing we did was the reorganization of the team following the insights from the workshop. I will not go deep
into the format of work before the changes, but after, we began to work as follows:

1. Each engineer of our team is assigned to the project for the life of this project (usually from a couple of days to a
couple of months).
2. We made the documentation a necessary component of any new Terraform modules or architectural changes.
3. We prefer pair programming with product engineers than back-and-forth on their Pull Requests into the infrastructure
codebase.
4. After the completion of a project, if some new technology was introduced or the architecture of some part of the
system was changed, we agreed to conduct knowledge sharing sessions. Depending on the type of information, they are held
only for our team or open to all engineers who consider it useful to attend.
5. We reorganized our approach to service tasks, highlighting weekly shifts during which only one person works with
them, and the rest of the team focused on their main projects.

Structural changes were only a consequence of the workshop, not its main goal. Yet, after a few months, we are still
satisfied with them, although, after a couple of retrospectives, we already see some points to improve.

## Getting to the conclusion

This is the story of our company and the existential search of our team in it. It is too early to judge how big is the
positive impact of these changes. We all agreed that the overall experience with the ideation process and resulted
changes were useful. Now we better understand how our team is seen from the outside, how we see it, and, most
importantly, how we can work so that our actions are most effective for the company.

## TL;DR.

If the post seemed too large, here I have put together a list of tips that I can give so that your
backend/platforms/ops/infrastructure team can become more efficient in delivering values to its customers.

1. Always remember that your customers are both users of your services (other developers) and end-users of the company's
product.
2. Being customer-oriented is important. If it is not clear why, see the list in the section “Why customer centricity is
important” above.
3. Ask other teams about which aspects of your work and interaction they would like to praise and what can be improved.
Moreover, the most valuable gratitude is always public, and improvement tips should be carried out only between members
of these teams or their managers.
4. It is never too late to change the structure of the team, its approach to work or interaction with other teams.
5. Don't lose an opportunity to praise your colleagues! They do a great job! Babbel engineers, you're the best, thank
you all =)
