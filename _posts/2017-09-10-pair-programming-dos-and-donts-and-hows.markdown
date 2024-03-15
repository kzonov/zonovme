---
layout: post
title: Pair programming. Do's and don'ts. And hows.
date: 2017-09-10 20:47:06.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Development
- Management
tags:
- clean code
- development
- pair programming
- process
- XP
meta:
  _edit_last: '1'
  hefo_before: '0'
  hefo_after: '0'
  _aioseop_description: When to use pair programming and when do not. Also here I
    try to answer why do you need it and how to make this process smooth and comfortable.
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/pair-programming-dos-and-donts-and-hows/"
excerpt: "\n\t\t\t\t\t\t"
---
<p>
				Past weeks I was in process of moving to another city and had no time to spend it for cool technical things, but I had a time for past experience reflection. About one thing I used frequently in past time I want to write here. As you can see, the topic is about Pair Programming.</p>
<p><!--more--></p>
<p>As Wikipedia says about it:</p>
<blockquote><p><b>Pair programming</b> is an agile software development technique in which two programmers work together at one workstation. One, the <i>driver</i>, writes code while the other, the <i>observer</i> or <i>navigator</i>, <sup id="cite_ref-1" class="reference"></sup>reviews each line of code as it is typed in. The two programmers switch roles frequently.</p></blockquote>
<p>If first been introduced in XP (Extreme Programming) together with other great concepts, like TDD, Continuous Refactoring and etc. If you, my beloved readers are interested in my opinion or discussions or even holy wars about these topics - tell me and I then will know that I should write more about it.</p>
<p>As you may understand, there is no silver bullet and pair programming is just a technique, which may help you, but also can hurt. So no doubts, that if the technique has some followers, it will have opponents. Let's mention some of the adversaries thoughts here:</p>
<blockquote><p>The short story is that pair programming doesn’t work for me as the main way of developing software. I can pair program for a day, or maybe a week, especially if we’re focused on a particular problem. But after that? I’m done. Toast. I don’t want to see anyone, talk to anyone, and I need at least a couple of days in a cave until I’m fit for human company again. <em>- @wsargent</em></p>
<p>Perhaps my biggest critique of pair programming is this overriding need to work as a single enlightened individual rather than as two members of a team. Why can't both people be coding at the same time? Why can't one be writing tests and the other functional code? Why can't one be doing some research and the other tracking down a defect? <em>- @mortoray</em></p>
<p>There is one and only one situation, where pair programming makes sense — knowledge transfer from the more knowledgeable person to the less knowledgeable person. Other than that, you have just wasted twice the development power for <i>at best </i>+20% benefit. <em>- @carnaedy</em></p></blockquote>
<p>And a lot of others. So let me explain my position now.</p>
<h2>When you really need pair programming?</h2>
<p>You definitely can avoid this stupid waste of time where you will lose 20% or even 80% of productivity of one developer. But in some cases, it will give you much more benefit. And here they are:</p>
<ul>
<li>When you introduce your project to a new teammate. Doesn't matter if he above your level, the same or below. When you just show few UMLs and give some docs, honestly, don't expect that the guy/girl will have a clue about what's going on in your project, what conventions, processes, and ideas do you have in the team. Even great docs will not be as powerful here as one week of pair programming.</li>
<li>When you need to invest a great chunk of a time in refactoring or architecturally complex things. In my previous job I've seen multiple times situations when one person did his best, doing the refactoring, then spent days fixing complaints after PR review or when he finished, had to listen as people said that "who did this piece of s..code?!". In case, when two people do it - they can barely do something really stupid, but in opposite probably they will produce much more robust and long-living code.</li>
<li>When you think that your teammate is stupid and doing a smelly code (or he thinks you are). It is really useful for the expert-expert level of teammates. It can either help you improve each others skill level or just make you feel that he/she is smarter but just probably thinks in another way. If you are a fast coder, the other person can be more accurate one. If you are meticulous tests writer you may see that your teammate is really great in finding better architectural solutions. I must mention here that it may make things worse, but it's only up to you, what result you'll have.</li>
</ul>
<h2>Important rules</h2>
<ol>
<li>Use Pomodoro. If you don't know, what is it - google and then use it. Long story short, change with your partner every half an hour. Even if you are in process of creation, it will not hurt.</li>
<li>If you are "writer" in this time, don't create faster than your "thinker" thinks. Your job now - write.</li>
<li>If you don't agree with what your partner says - discuss it. If you cannot come to the mutual agreement - "thinker" dictates his opinion.</li>
<li>If you need time to discuss or plan or analyze - stop your pair programming session and do what you need.</li>
<li>Prepare everything you need for work (coffee, water, cookies) to not interrupt the process.</li>
<li>If it's plain and stupid work - think twice, do you really need pair programming here?</li>
</ol>
<h2>When do not?</h2>
<p>And here is the answer, when do not use this technique.</p>
<ol>
<li>When it's too simple, like just coding already discussed and set in stone things.</li>
<li>When it's too complicated and first need to be investigated or discussed.</li>
<li>When you are 100% disagree with your colleague on this particular question and see no possibility to collaborate. Bad to you guys, if so.</li>
</ol>
<h2>What to do if your colleague doesn't want to pair program?</h2>
<p>Do not do pair program =)</p>
<h2>Conclusion</h2>
<p>Pair programming is a powerful technic, which should be used in daily routine. It's like a drill at home. You should not use a screwdriver to make holes in your wall, but you also shouldn't use a drill for everything. Soup is usually better when you use a spoon rather than a drill to mix it.		</p>
