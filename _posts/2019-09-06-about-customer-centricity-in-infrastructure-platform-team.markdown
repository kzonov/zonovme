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
excerpt: "Dear readers, in this post, I will steer away from highly technical subjects to discuss a topic crucial for many engineers: customer centricity. This concept offers two primary benefits. Firstly, embracing a customer-centric approach ensures your work profoundly and positively impacts the product. Secondly, it often leads to receiving constructive feedback from colleagues or users, invaluable for both product enhancement and personal growth. For those who recognize the potential value of customer centricity in engineering, let's dive in!"
---
Dear readers, in this post, I will not delve into highly technical topics. However, the subject I am going to discuss is crucial for many engineers: customer centricity. This concept has two primary advantages. Firstly, adopting a customer-centric approach ensures that your efforts significantly positively impact the product. Secondly, it often leads to receiving valuable positive feedback from colleagues or users, which is useful in both product improvement and personal development. If you're a developer who prefers focusing solely on technical tasks without considering the end-user or the purpose behind your work, this post might not resonate with you yet. Nevertheless, I believe that, give yourself a few years, you might reconsider your stance. For those who already recognize the potential value of customer centricity for engineers, let's dive in!

*Before we start, note that there is a TL;DR section at the end of this article. If you find the content too lengthy, feel free to skip ahead =P*

## A Few Words About My Journey

I am a developer with over ten years of experience, having assumed various roles — from a young trainee blending sysadmin and developer duties (embracing the DevOps spirit before the word even existed, right?) to a CTO. My career has predominantly been within product teams, focusing mainly on backend development, though I've also spent considerable periods working on frontend tasks. Importantly, my roles have always involved direct engagement with the final, customer-facing product. This exposure means I've often witnessed firsthand how users respond to new features (not always positively!) — whether through A/B testing or after a full release. I've monitored traffic, observed users navigating our registration forms, interacting on the site, making purchases, and occasionally, leaving valuable feedback.

Not too long ago, I transitioned to the deepest realms of backend work, focusing on Cloud Infrastructure. This shift meant my work became highly abstracted from the end-user experience, even to the extent that my product manager oversees only our infrastructure platform, not the final product itself. Below, I'll share an unexpected challenge I encountered in this new role.

## The Significance of Customer-Centricity

I firmly believe that all companies and engineers should adopt a customer-focused mindset. The famous Henry Ford's Quote “If I had asked people what they wanted, they would have said faster horses” has its place in history and could be used to refute my claim. However, this approach only emphasizes Ford’s customer-centricity, because although he didn’t “ask people”, he created something for them, saw their positive reaction, improved and continued building it further.

Being part of a product team, especially in smaller companies, makes it easier to stay aligned with customer needs. You’re directly connected to product metrics, understanding the cause-and-effect relationship between your features and their ability to meet user demands. Numerous methods exist to gauge if you’re on the right track, ranging from pre-development activities like user interviews and UX research to post-launch analytics, such as using Google Analytics or RUM (Real-user monitoring).

Let’s explore why maintaining a customer-focused perspective is essential for you as an engineer and for your team:

1. We all thrive on challenges! Happy users lead to more users, which translates to more complex and engaging tasks.
2. Our coding isn't just to keep the gears turning; it’s aimed at fulfilling real business objectives—meeting the users' needs.
3. External motivation, like the satisfaction of seeing others benefit from your work, can be a powerful drive.
4. When everyone understands that the ultimate goal is to deliver the best possible solution for the end-user, the entire team moves in unison, rather than pulling in disparate directions.
5. Moreover, the DevOps philosophy itself advocates for customer-orientation, a point we’ll discuss further below.

## DevOps and Customer Focus

First, a small detour to familiarize yourself with (or recall if you are familiar with) what DevOps philosophy has to say on customer focus. If we turn to The Project Phoenix and DevOps Handbook, the idea of ​​The Three Ways goes through them. The Second Way talks about Improving the Right to Left Feedback Loop.

If the second book of DevOps Handbook focuses on the technical side, such as alerts, then the first describes the aspect, which is closer to the content of this article. They talk about a feedback loop from customers and more generally, users of your services. In the case of a platform or infrastructure team, these users are product developers.

I will not delve deep into retelling the Project Phoenix book, it’s available on Amazon for you to get to know it. Hope that this small digression was enough to get the idea.

## My Personal Challenge

After half a year in my new role, the initial thrill of technical problem-solving began to wane. I was refining major Terraform modules, strengthening security, and contributing to open-source projects. Despite these achievements, I felt disconnected from the direct impact of my work on the end-users of our product.

This realization prompted me to take decisive action. I'll outline the steps I undertook to realign my work with our core user-focused values:

1. Gathering feedback from key external stakeholders.
2. Conducting a workshop to analyze this feedback and identify actionable areas.
3. Restructuring the team based on our findings.

Now, let’s delve into the specifics of each phase.

## Feedback from Stakeholders

Ideally, feedback collection is a continuous process. When it becomes sporadic, it no longer serves as a true feedback LOOP, as emphasized in DevOps. A monthly retrospective with engineering managers or team leads can foster a constructive exchange of commendations and areas for improvement.

For our initial foray into this practice, I reached out to key stakeholders who had interacted with our team. I posed three questions to each, aiming to understand their goals, how our team could support them, and their preferred form of interaction.

The diversity of responses was enlightening, particularly regarding how they envisioned our team’s role. This feedback was crucial for informing our subsequent workshop and actions.

## Workshop Insights

Preparation is key for a successful workshop. I provided team members with pre-read materials and a clear agenda to avoid on-the-spot surprises. The workshop was structured in five phases, designed to crystallize our team’s principles, envision our ideal state, and plan tangible steps toward that vision.

This process, while specific to our circumstances, can be adapted to fit any team’s needs. It’s a powerful way to align team members with shared objectives and actionable plans.

## Implementing Changes

The workshop insights led to a thoughtful reorganization of our team. We shifted to a project-based assignment model, emphasized the importance of documentation, and adopted pair programming practices with product engineers. These changes were geared towards enhancing our effectiveness and ensuring our contributions had a meaningful impact on both our colleagues and the end-users.

## In Conclusion

Our journey is a testament to the evolving nature of engineering teams within a business. The impact of our structural changes is still unfolding, but the positive shifts in our team dynamics and clarity of purpose are undeniable.

## TL;DR

For those seeking a concise summary, here are key takeaways for enhancing the efficiency and customer-centricity of your backend/platforms/ops/infrastructure team:

1. Acknowledge that your “customers” include both fellow developers and the end-users of the company’s product.
2. Embrace customer-orientation; it’s crucial for delivering genuine value.
3. Regularly solicit feedback on your team’s performance and areas for improvement.
4. Stay open to restructuring your team and its processes to better align with user needs.
5. Never underestimate the power of acknowledging your colleagues' efforts. A little appreciation goes a long way!
