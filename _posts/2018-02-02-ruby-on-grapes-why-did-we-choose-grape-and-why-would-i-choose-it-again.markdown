---
layout: post
title: Ruby on Grapes. Why did we choose Grape and why would I choose it again
date: 2018-02-02 00:07:06.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Development
tags:
- development
- ruby
meta:
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  _adinserter_block_exceptions: ''
  hefo_before: '0'
  hefo_after: '0'
  _wp_old_slug: ruby-on-grapes-why-did-we-choose-grape-and-why-i-would-choose-it-again
  _aioseop_description: Why did we choose Grape framework in my team and why would
    I choose it again.
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/ruby-on-grapes-why-did-we-choose-grape-and-why-would-i-choose-it-again/"
excerpt: "\n\t\t\t\t\t\t"
---
<p>
				Today I gave a talk on Ruby User Group Berlin meetup, here is a recap of it in a readable format.</p>
<p>In my team, we are building a new and fast-evolving SPA product. We are small in terms of a number of developers and we are agile in terms of the market. We test the design of an idea, quickly implement it, test it with real users, then either keep and improve or change or remove. Quite a quick pace, so when we were choosing the web framework we wanted it to be more a helper for us rather than a box, out of which we cannot step.</p>
<p><!--more--><br />
<img class="aligncenter size-large wp-image-523" src="{{ site.baseurl }}/assets/2018/02/DU-TIc9XUAA1uXw-1024x576.jpg" alt="Performance of ruby web frameworks" width="640" height="360" /></p>
<p>About language, it had been decided that it should be Ruby because it's the fastest language for MVPs and fast pace development on pre-enterprise or pre-scale product steps. So the next we should choose a framework. What are the options?</p>
<p>Ruby on Rails!<br />
Nah... We're all tired of being a Rails developers and struggling with how to fit your app architecture into Rails, how to remove everything and keep just what you need. That's feeling when you take opinionated Rails, opinionated developer and their opinions just doesn't match %)</p>
<p>BTW, I don't say that Rails is bad. It begins to rot if don't know how to cook it, so it's all about developers, but we decided on the start we won't go with Rails. So what are the other options:</p>
<ul>
<li>Grape</li>
<li>Hanami</li>
<li>Roda</li>
<li>Rails API</li>
<li>Sinatra</li>
</ul>
<p>And here are our criteria:</p>
<ul>
<li>Comprehensiveness</li>
<li>Versioning support</li>
<li>Maturity</li>
<li>Performance</li>
<li>Personal preference</li>
</ul>
<h2>Comprehensiveness</h2>
<p>By this term, I mean that the framework must have all the crucial things in it. Like CORS support, params validation, exceptions handling and rendering, i18n etc.</p>
<p>Pretty obvious that Rails API as a project under the Rails ecosystem has everything needed.</p>
<p>Sinatra as the opposite side is pretty barebone and doesn't have much. You can add anything you'd like to, because it does not stop you from that, but it's a bit of disadvantage by me. Just the overall idea of Sinatra is to have a minimal web app framework, which is great and I used it a lot, but I would like to have more tools out of the box.</p>
<p>Other frameworks are somewhere in between, they all have enough of things out of the box as well as enough of side tools you can use with them, no much to say here.</p>
<h2>Versioning support</h2>
<p>Since we are evolving fast, it sometimes useful to build a new version of API and leave the old for some time, either to be able to still test it or just as some sort of a background to be able to revert back to. I see that path-based versioning makes perfect sense, so I consider it here (but you can also use header based).</p>
<p>All the mentioned frameworks support you in versioning, but all of them in a different way:</p>
<p>Sinatra, Hanami, Rails API:</p>
<p>[ruby]namespace '/api/v1' do<br />
  # routes goes here<br />
end[/ruby]</p>
<p>Roda:</p>
<p>[ruby]r.on &quot;api&quot; do<br />
  r.on &quot;v1&quot; do<br />
    r.is &quot;users&quot; do<br />
      r.get do end<br />
      r.post do end<br />
    end<br />
    r.get &quot;comments&quot; do end<br />
  end<br />
end[/ruby]</p>
<p>Grape:</p>
<p>[ruby]version 'v1', using: :path[/ruby]</p>
<p>Personally, I prefer the way how Grape is doing it. Despite it's more DSLish, but it's less verbose and clear enough, so does not create any magic around.</p>
<h2>Maturity</h2>
<p>By this word I mean that the framework had been created not in the last month, has a stable version and I personally have no doubts that it will still exist in the next couple of years.</p>
<p>Here are some numbers:</p>
<ul>
<li><strong>Grape. </strong>Had been released in 2010, has 4 merged PRs in the past month.</li>
<li><strong>Rails API.</strong> Had been released in 2012, has <strong>85</strong> merged PRs in the past month, but keep in mind that it had been merged into Rails in 2015, so it's not just Rails API PRs.</li>
<li><strong>Sinatra.</strong> Granddaddy here, had been released in 2008. Still evolving, 3 PRs in the last month. Personally, I don't see any changes in the past 5 years in it, but I didn't dig deep.</li>
<li><strong>Hanami.</strong> Shiny new, had been released in 2014, but in my view area entered just in the past year because they released 1.0 version in on 2017. Under the active development, but just 1 PR in the last month. To be fair, I'll say that Grape actually also had been released under 1.0.0 version in the past year.</li>
<li><strong>Roda.</strong> Started in 2014 and in the same year had first stable release. Has 1 merged PR in the last month.</li>
</ul>
<p>Should you use any of these numbers when you consider choosing some web framework - up to you. By me, it shouldn't be the main choice factor, but still nice to keep it in mind.</p>
<h2>Performance</h2>
<div class="mceTemp"></div>
<p>If you create a typical internet-startup product, web framework performance is the last thing you should think about. Even when you scale, the most bottlenecks you will have not with a framework, but with database queries or lack of caching, or inter-microservice communication or external providers which you by some reason touch in the main thread or whatever else.</p>
<p>If it's somehow important for you: a couple of numbers are:</p>
<ul>
<li>Grape development startup time is 0.81s on my machine</li>
<li>Rails API development startup time is 1.6s.</li>
<li>Sinatra expectedly the fastest with 0.27s. Surprise? Not at all!</li>
</ul>
<p>Should you take it into consideration? Up to you again.</p>
<h2>Personal preference</h2>
<p>As I said, I used to work with both Rails and Rails API a lot, would work with it again if needed, but this time we decided to go without Rails. Again, I don't say that Rails is bad, btw it's almost a monopoly, and if you are alright with it - please read <a href="http://solnic.eu/2016/05/22/my-time-with-rails-is-up.html">Piotr Solnica.</a> Still now if I would need to build some MVP, which would not have long-term plans and wouldn't be SPA/API for mobile - I most likely would choose Rails.</p>
<p>Sinatra is too barebone by me. It's good when already set up and you just need to develop a business logic around. But before that, you have to spend some time setting up the backbone.</p>
<p>Hanami is a bit immature for the production product as by me, despite that, I enjoyed it in my learning pet project.</p>
<p>About Roda, I just don't agree with the DSL they suggest for routing tree.</p>
<p>So the Grape by me is perfectly fine. Fast to develop enough, has enough features, mature enough and fast enough to run tests and the web server xD</p>
<h2>Some pigs among sheeps</h2>
<p>DSL. Grape has its own DSL. Sometimes it's weird, like in their grape-entities. F.e.</p>
<p>[ruby]expose :some_attribute, if: -&amp;gt;(element, _) { element.final? } do |element|<br />
  element.my_attribute<br />
end[/ruby]</p>
<p>Here I show you one presenter in which I want to expose some attribute which appears only on some condition and its name is different than it should be exposed in the API.</p>
<h2>Some code samples</h2>
<p>And just for you to understand better how does the API related code look like, here are two illustrations:</p>
<p>[caption id="attachment_779" align="aligncenter" width="600"]<img class="wp-image-779 " src="{{ site.baseurl }}/assets/2018/02/apirb.png" alt="api.rb" width="600" height="401" /> api.rb[/caption]</p>
<p>[caption id="attachment_780" align="aligncenter" width="596"]<img class="wp-image-780 " src="{{ site.baseurl }}/assets/2018/02/conversationsrb.png" alt="routers/conversation.rb" width="596" height="209" /> routers/conversation.rb[/caption]</p>
<p>As you may see, you have built-in params validations, error rendering, versions and etc.</p>
<h2>Conclusion</h2>
<p>Obviously, there is no silver bullet in software engineering, and choosing the framework is more like a religious question, it's hard to convince another person to switch to it and leave his cozy world. But I encourage you to check out anything from the list I mentioned if you never tried. But if you're already experienced not only in Rails but somehow missed Grape, take a look at it, it's worth your time.</p>
<p>Thank you!</p>
<p>P.S. Slides:<br />
<iframe style="border: 1px solid #CCC; border-width: 1px; margin-bottom: 5px; max-width: 100%;" src="//www.slideshare.net/slideshow/embed_code/key/4tpppmFi2f017J" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" allowfullscreen="allowfullscreen"> </iframe></p>
<div style="margin-bottom: 5px;"><strong> <a title="Ruby on Grapes. Why did we choose Grape and why I would choose it again" href="//www.slideshare.net/graffzon/ruby-on-grapes-why-did-we-choose-grape-and-why-i-would-choose-it-again" target="_blank" rel="noopener">Ruby on Grapes. Why did we choose Grape and why I would choose it again</a> </strong> from <strong><a href="https://www.slideshare.net/graffzon" target="_blank" rel="noopener">graffzon</a></strong></div>
