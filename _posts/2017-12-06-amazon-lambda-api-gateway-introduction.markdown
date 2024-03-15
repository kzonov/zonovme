---
layout: post
title: Amazon Lambda + API Gateway introduction
date: 2017-12-06 23:21:45.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- AWS
- Development
- DevOps
tags:
- aws
- development
- devops
- lambda
- nodejs
- serverless
meta:
  _edit_last: '1'
  _oembed_1698811835af73d21ff4d0569745c9e8: "{{unknown}}"
  _syntaxhighlighter_encoded: '1'
  _oembed_d526a7c25caf49a576986f89a5b79b73: "{{unknown}}"
  classic-editor-remember: classic-editor
  _adinserter_block_exceptions: ''
  hefo_before: '0'
  hefo_after: '0'
  _aioseop_description: Hands-on introduction, how to create a basic Lambda function,
    AWS API Gateway, couple them, and then even deploy!
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/amazon-lambda-api-gateway-introduction/"
excerpt: "Hi everyone,
I just started AWS Learning Sessions in the company, I’m currently working in, and want to share with you our first lesson."
---

Hi everyone,
I just started AWS Learning Sessions in the company, I'm currently working in, and want to share with you our first lesson.
We have built small Lambda function, named Greeter, and integrated it to the AWS API Gateway.
If you want to get started with this tools and get some hands-on experience - this article will help you.

<!--more-->
In Babbel we hire (<a href="http://grnh.se/o0gmkj1" target="_blank" rel="noopener">backend</a>, <a href="http://grnh.se/cshrdq1" target="_blank" rel="noopener">frontend</a>, <a href="http://grnh.se/lpdqak1" target="_blank" rel="noopener">fullstack</a>, <a href="http://grnh.se/agxcfs1" target="_blank" rel="noopener">infrastructure</a>) :) and also use AWS a lot. Our infrastructure there is complex and solid, but still, there are some people, who don't work a lot with it. F.e. frontend engineers, mobile developers, data analysts, they shouldn't have AWS hands-on experience for everyday work, but even if it's not required it's a nice-to-have skill and so we started these sessions.

Currently, we introduced three lessons:
1. Lambda + API Gateway,
2. First lesson + DynamoDB and IAM Roles,
3. First and second lessons implemented using Terraform, also we will need Swagger here.

The first session is just passed, so I going to share it with the world :)
<h2>AWS Lambda</h2>
You probably heard the buzzword "serverless"? Here is a quote from Martin Fowler's blog:
"<i>Serverless architectures refer to applications that significantly depend on third-party services (knows as Backend as a Service or "BaaS") or on custom code that's run in ephemeral containers (Function as a Service or "FaaS"), the best-known vendor host of which currently is AWS Lambda. By using these ideas, and by moving much behavior to the front end, such architectures remove the need for the traditional 'always on' server system sitting behind an application."</i>
 Why use it?
The general idea is that you pay only for resources, which you are really using. You never pay for your infrastructure, when it's idle. You invoked a function - you pay some small fraction of a cent. This makes much sense, because your app becomes a de-facto microservice-like. And you may require some parts of your application to be invoked constantly (let's assume items information microservice in an e-commerce website), but some will be needed just a few times a day (newsletters dispatching in the same e-commerce website). You still can configure this imbalance using more/less powerful instances for your microservices, but there is still no need to keep the whole newsletter microservice sitting idle almost all the time. Obviously, it's just a synthetical example, but the idea should be clear.
Another cool thing is that AWS Lambda (and others) provides you auto scalability (to thousands per second) and high availability. Let's say that on our e-commerce site we sell goods, related to Star Wars, LotR, GoT and other popular films/series. Usually, we have 1000 visitors per day, because people buy Darth Vader masks for their friends' birthdays, or on another occasion. But Christmas and New Year are coming, and Black Friday and all that events, which will make our website to be visited by 10000-50000 persons per day! It's already not a huge deal if we use cloud infrastructure. AWS EC2 or Heroku instances can be scaled easily. But still, you will need to manage it manually and to know, when to increase and decrease resources (to be fair, with EC2 it's already possible to scale automatically). If most of your architecture is in Lambdas - you can just rely on it, it can be scaled automatically by invoking it from once per week, to 1000 per second.
And good to know is that lambda starts in a container. You can specify RAM needed from 128MB to about 1.5GB.
Currently, it supports Node.js, Java, C#, and Python. So, unfortunately, you cannot use Ruby to write your functions, but it's no biggie since your functions will be pretty small and you won't need to maintain complex internal architecture.

 Lambdas can be used/invoked in three different ways:

1. As a backend to an API Gateway endpoint;
2. Directly from an app with AWS SDK (can be invoked from both frontend and backend parts);
3. As a callback for some AWS service, like S3 or DynamoDB.
<h2>API Gateway</h2>
As it should be clear from the name - it’s an api gateway ?
To understand, what is AWS API Gateway it's better to first understand, what is a VPC.
VPC (Virtual Private Cloud) is a virtual network, which is isolated from other user's of the cloud provider and from the outside world, and servers in VPC have an access to all of the AWS internal resources, like S3, DynamoDB, EBS etc.
And now imagine it as a closed military city with all the facilities inside, like barracks, kitchen, and warehouse. And the API Gateway in this analogy will be a checkpoint - entrance. So it can provide external apps a RESTful access to your VPC. I find this analogy very colorful and precise.
<h2>Construction time (Lambda)</h2>
I told you that you'll have hands-on experience with AWS Lambda today, so have at it!
I assume you already have an AWS account and can start playing around with it.
So first you should go to the AWS Lambda chapter in AWS Console.

<img class="wp-image-435 size-large" src="{{ site.baseurl }}/assets/2017/12/joxi_screenshot_1512588289321-1024x556.png" alt="select aws lambda in the console" width="640" height="348" />

<img class="size-large wp-image-436" src="{{ site.baseurl }}/assets/2017/12/joxi_screenshot_1512588524645-1024x191.png" alt="push create function" width="640" height="119" />

<img class="size-large wp-image-437" src="{{ site.baseurl }}/assets/2017/12/joxi_screenshot_1512588741876-1024x728.png" alt="basic aws lambda settings" width="640" height="455" />

When you will be on the Lambda function creation screen - you will have to specify some basic config options:
<ol>
<li>Name. Pretty obvious field.</li>
<li>Runtime. My example will be implemente using Node.js, because I have hands on experience with it, so please choose it as well.</li>
<li>Role. Choose "Create new role from template(s). If you read my post some time after it had been written, probably some naming changed, because AWS keeps evolving, but the idea should still be the same.</li>
<li>Role name. This field will appear after you selected the Role option. This field is also should be clear to you.</li>
<li>Policy templates. Because we are building very basic lambda function - just choose default template and then needed policies will be added automatically when we will need it. You should choose here Basic Edge Lambda permissions.</li>
</ol>
Then push "create", it will take up to couple of mintes (because it also goes via AWS Lambda xD) and then your function will be created.
It's funny that when I made a workshop, the interface was different, then now, so as I said, don't be confused if you have another appearance :)

<img class="size-large wp-image-438" src="{{ site.baseurl }}/assets/2017/12/joxi_screenshot_1512589221107-1024x556.png" alt="newly created lambda function" width="640" height="348" />

We are currently interested in <strong>Function code </strong>block.
Here you have very basic code, but it's already can be tested. First, few words about structure of the function.
{% highlight javascript %}
exports.handler = (event, context, callback) =&gt; {
    // TODO implement
    callback(null, 'Hello from Lambda');
};
{% endhighlight %}

Handler is a function, which Lambda will invoke to execute your code. So it should be an entry point. Actually you can give it another hame, just don't forget to put it also into the <strong>Handler</strong> input field above the code block.
Lambda handler expects three params, event, context, callback.
"Event" is an object containing external params your Lambda use to pass vars/data to your function. F.e. our query/body params from a request will be passed here.
"Context" is a runtime information. For instance, when the function will be terminated by timeout, CloudWatch log groups, Cognito identity.
"Callback" - you use it to return information to your caller. First param of it is an error, last is a result. As you see in the example above, first param is null, so it means that no error had been invoked.
Let’s try to test it!
Push the <strong>Test</strong> button above:

<img class="aligncenter size-large wp-image-439" src="{{ site.baseurl }}/assets/2017/12/joxi_screenshot_1512589624921-1024x308.png" alt="click the test button" width="640" height="193" />

It will ask you to configure a new test event:

<img class="aligncenter size-large wp-image-440" src="{{ site.baseurl }}/assets/2017/12/joxi_screenshot_1512589758919-918x1024.png" alt="configure a test event" width="640" height="714" />

Don't change anything there, just press create, it will be created and you'll be back on the previous screen. Tap <strong>Test</strong> again there.
Great it works!

<img class="aligncenter size-large wp-image-441" src="{{ site.baseurl }}/assets/2017/12/joxi_screenshot_1512589864080-1024x509.png" alt="successful test" width="640" height="318" />

Funny thing is that AWS changed it's hello world function, but didn't change hello world test event. So you could notice on my screenshot, that by default we have some params passed to the function, but don't use them in the code. Don't worry, we will change it anyway :)
<h2>Construction time (API Gateway)</h2>
 Go into <strong>Services -> API Gateway.</strong>
Then click on the button <strong>Create new API</strong>, and you will end up on the following page:

<img class="aligncenter size-large wp-image-443" src="{{ site.baseurl }}/assets/2017/12/joxi_screenshot_1512590898506-1024x685.png" alt="api gateway creation page" width="640" height="428" />

All options should be selected as in my screenshot.

<img class="aligncenter size-large wp-image-444" src="{{ site.baseurl }}/assets/2017/12/joxi_screenshot_1512590987371-1024x116.png" alt="empty gateway" width="640" height="73" />

So here is our brand new API Gateway, let's make it real!
Click <strong>Actions -> Create Resource</strong>.

<img class="aligncenter size-large wp-image-445" src="{{ site.baseurl }}/assets/2017/12/joxi_screenshot_1512591093761-1024x379.png" alt="aws api gateway creation" width="640" height="237" />

No need to select any checkbox, just add your resource name <strong>"greeter"</strong>.
After that you need to click again on <strong>Actions</strong>, then <strong>Create method</strong>, select <strong>Get</strong> there and create it. Then you will see following page:

<img class="aligncenter size-large wp-image-446" src="{{ site.baseurl }}/assets/2017/12/joxi_screenshot_1512591214252-1024x687.png" alt="api gateway method creation" width="640" height="429" />

Here are fields you need to select:
<ol>
<li>Integration Type. As it's obvious - Lambda Function. If you use a backend server, you may want to use HTTP, if you want to test your comprehensive gateway - you may choose Mock, but for our lesson, you need exactly Lambda Function.</li>
<li>User Lambda Proxy Integration. Select it. It means that the whole request object will be passed to your function and you can work with it directly together with building the response. This is a cool new feature, so I encourage you to use it.</li>
<li>Lambda Region. It may be tricky for you to remember it, but you can find it on the same page in the browser address line. Choose it for Lambda Region, I bet it's the same as API Gateway region.</li>
<li>Lambda Function. This input appears after selecting the Lambda Region. Here you should be able to find your lambda function. If there is no your function - you probably have chosen the wrong region, try another.</li>
<li>User Default Timeout. Just use the default option here.</li>
</ol>
Click <strong>Save</strong>, it will ask you to automatically give a new IAM Role permission, just click <strong>OK</strong>, because it's OK.
Voila! It's almost ready! Let's try to test our shiny new gateway!

<img class="aligncenter size-large wp-image-447" src="{{ site.baseurl }}/assets/2017/12/joxi_screenshot_1512591694908-1024x396.png" alt="test api gateway" width="640" height="248" />

Don't pass any params on the next screen, just push <strong>Test</strong>.
Baaaam, doesn't work, you should get such result:
{% highlight javascript %}
{
  "message": "Internal server error"
}
{% endhighlight %}
Time to go back to your Lambda Function and change it a little bit:
{% highlight javascript %}
exports.handler = (event, context, callback) => {
    callback(null, {
        statusCode: '200',
        body: "Hello, " + event.queryStringParameters["name"] + "!"
    })
};
{% endhighlight %}
Here in our function, we return a callback, which has no error, but the response is actually an HTTP response object. As you see, you can pass a status code and a body. I think the code is pretty straightforward, so no need to explain it.
We are close to the finish line!
Go back to your <strong>API Gateway</strong>, click the <strong>Test</strong> button, you will again be on this page:

<img class="aligncenter size-large wp-image-449" src="{{ site.baseurl }}/assets/2017/12/joxi_screenshot_1512594355837-1024x520.png" alt="aws api gateway test lambda" width="640" height="325" />

pass <strong>Query Strings </strong>param <code>name=Yourname</code> and press <strong>Test</strong>. On the right part you should get the response from your API, that it greets you by name =)
It isn't hard, is it?
The last thing is to really deploy your API. Bear with me, it's just couple of clicks!

<img class="aligncenter wp-image-745 size-full" src="{{ site.baseurl }}/assets/2017/12/deploy-api-gateway.png" alt="" width="724" height="870" />

Click to <strong>Actions</strong> button above your API endpoints list and just select <strong>Deploy API </strong>from there.
You will get a small dialog window, just enter a new stage name (usually it will be staging or production) there and click deploy:

<img class="aligncenter size-full wp-image-452" src="{{ site.baseurl }}/assets/2017/12/joxi_screenshot_1512594892339.png" alt="select api stage" width="1000" height="688" />

Yay, here is a link to your deployed API:

<img class="aligncenter size-large wp-image-453" src="{{ site.baseurl }}/assets/2017/12/joxi_screenshot_1512594972284-1024x222.png" alt="deployed API Gateway" width="640" height="139" />

You can click on it, but it will open with an error because you don't have a root action. So to actually get your desired result, add a path and a param there, like:
https://9lm8uufwr7.execute-api.eu-west-1.amazonaws.com/production/greeter?name=Kirill
That is it!
Just to recap, now you have a complete knowledge, not only about how to create a new Lambda function, but also about how to create an API Gateway, point it to a Lambda and how to deploy it as well!
So the post is huge, but I hope you've got a new knowledge from it :)		
