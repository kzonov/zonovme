---
layout: post
title: Building an Amazon Lambda function to write to the DynamoDB
date: 2017-12-16 16:52:15.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- AWS
- Databases
- DevOps
tags:
- amazon
- aws
- database
- development
- devops
- dynamodb
- lambda
- nosql
- serverless
meta:
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  hefo_before: '0'
  hefo_after: '0'
  _adinserter_block_exceptions: ''
  classic-editor-remember: classic-editor
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/building-an-amazon-lambda-function-to-write-to-the-dynamodb/"
excerpt: "\n\t\t\t\t\t\t"
---
<p>
				In this post, we will create a Lambda function which can write to the Amazon DynamoDB table. For this, we will create a table, modify existing function and set up IAM roles. Log in to your AWS account and let's get started!</p>
<p><!--more--></p>
<p>In <a href="http://zonov.me/amazon-dynamodb-introduction/" target="_blank" rel="noopener">the previous post</a> I gave you an introduction to the Amazon DynamoDB, now it's time to try it out by yourselves. To make it we will use a Greeter Lambda function <a href="http://zonov.me/amazon-lambda-api-gateway-introduction/" target="_blank" rel="noopener">from this post</a>. So please make sure that the configuration from that post works for you and then proceed with this lesson.<br />
First, let's go to the Amazon DynamoDB page in the AWS Console.<br />
Then create a new table there. I will name it "kzonovGreetedVisitors".</p>
<p><img class="aligncenter size-large wp-image-479" src="{{ site.baseurl }}/assets/2017/12/joxi_screenshot_1513427869250-1024x779.png" alt="create amazon dynamodb table" width="640" height="487" /></p>
<p>That's it for now for the DynamoDB configuration. We just keep everything to be a default, it's suitable for most of the basic use cases. As you may see, I added one partition key, which is Name for me. It's intentionally not an auto-generated field, because our Name will be unique.</p>
<p>You can go to the <strong>Items</strong> tab, to make sure that your table is empty.</p>
<p><img class="aligncenter wp-image-747 " src="{{ site.baseurl }}/assets/2017/12/items-tab.png" alt="" width="503" height="225" /></p>
<p>Now let's open our previously created (if you don't have it - follow my <a href="http://zonov.me/amazon-lambda-api-gateway-introduction/" target="_blank" rel="noopener">Lambda introduction post</a>) and slightly modify the code.</p>
<p>[javascript]<br />
const AWS = require('aws-sdk');<br />
const dynamodb = new AWS.DynamoDB({apiVersion: '2012-08-10'});</p>
<p>exports.handler = (event, context, callback) =&gt; {<br />
    dynamodb.putItem({<br />
        TableName: &quot;kzonovGreetedVisitors&quot;,<br />
        Item: {<br />
            &quot;name&quot;: {<br />
                S: event.queryStringParameters[&quot;name&quot;]<br />
            }<br />
        }<br />
    }, function(err, data) {<br />
        if (err) {<br />
            console.log(err, err.stack);<br />
            callback(null, {<br />
                statusCode: '500',<br />
                body: err<br />
            });<br />
        } else {<br />
            callback(null, {<br />
                statusCode: '200',<br />
                body: 'Hello ' + event.queryStringParameters[&quot;name&quot;] + '!'<br />
            });<br />
        }<br />
    })<br />
};<br />
[/javascript]</p>
<p>Let me explain to you what do we do here.</p>
<p>First, we require the AWS Nodejs SDK, then don't be confused by the API version, how my colleague said, "they just implemented so great API, so no need to change it for five years now" :)</p>
<p>In the handler function, we invoke <code>putItem</code>, which accepts two params: the action object and the callback.<br />
The object should be clear for you, just one key may be confusing is the <code>S</code>. It means that the thing you put into the table has a String type. So if it would be a boolean, you would specify <code>B</code>, f.e.</p>
<p>Callback function also should be graspable, if you read my Lambda introduction post, which I already mentioned.</p>
<p>But now your function won't work because it doesn't have sufficient permissions. To give it - scroll down and find the "Execution role" block. In it, you will see your function role, remember it.</p>
<p><img class="aligncenter wp-image-750 " src="{{ site.baseurl }}/assets/2017/12/execution-role.png" alt="" width="542" height="311" /></p>
<p>Go to Services (on the top) -&gt; IAM -&gt; Roles (will appear in the left column). Then search for your role name and you will get smth like this:</p>
<p><img class="aligncenter wp-image-751 " src="{{ site.baseurl }}/assets/2017/12/roles-list.png" alt="" width="459" height="203" /></p>
<p>Then open your role and you will see a link at the bottom "Add inline policy", click on it.</p>
<p><img class="aligncenter size-large wp-image-484" src="{{ site.baseurl }}/assets/2017/12/joxi_screenshot_1513430550752-1024x405.png" alt="IAM Role main page" width="640" height="253" /></p>
<p>There you will have already selected <strong>Policy generator</strong>, click on <strong>Select</strong> there.</p>
<p><img class="aligncenter wp-image-752 " src="{{ site.baseurl }}/assets/2017/12/policy-generator.png" alt="" width="464" height="339" /></p>
<p>In order to fill in the ARN field, you need to open your DynamoDB config in the separate tab. Copy the ARN from there and paste it here.</p>
<p><img class="aligncenter size-large wp-image-487" src="{{ site.baseurl }}/assets/2017/12/joxi_screenshot_1513434954345-1024x836.png" alt="ARN for DynamoDB" width="640" height="523" /></p>
<p>As a rule of thumb, your policies work on to deny all. So you should manually specify, which actions do you want to allow. Here I suggest you add allowance only to one action, <strong>PutItem</strong>. Don't select <strong>all</strong>, because it will mean that your function will have too broad permissions. After filling all fields as on the image above, press <strong>Add Statement</strong>. After that <strong>Next Step</strong> and you will see smth like that:</p>
<p>[javascript]<br />
{<br />
    &quot;Version&quot;: &quot;2012-10-17&quot;,<br />
    &quot;Statement&quot;: [<br />
        {<br />
            &quot;Sid&quot;: &quot;Stmt1513430700000&quot;,<br />
            &quot;Effect&quot;: &quot;Allow&quot;,<br />
            &quot;Action&quot;: [<br />
                &quot;dynamodb:PutItem&quot;<br />
            ],<br />
            &quot;Resource&quot;: [<br />
                &quot;arn:aws:dynamodb:eu-west-1:123123123123::table/kzonovGreetedVisitors&quot;<br />
            ]<br />
        }<br />
    ]<br />
}<br />
[/javascript]</p>
<p>After clicking on the <strong>Add policy</strong> you will return to the <strong>Role</strong> page and you'll have a new policy attached there.</p>
<p>Now your Lambda function should be able to put a line into your table. If you followed my previous lesson, now you can go to the API Gateway to test it.</p>
<p><img class="aligncenter size-large wp-image-489" src="{{ site.baseurl }}/assets/2017/12/joxi_screenshot_1513435483635-1024x505.png" alt="test it with ApiGateway" width="640" height="316" /></p>
<p>Click test, in the new window enter <strong>Query strings</strong>, so it can greet you by name :)</p>
<p>After that click <strong>Test</strong> and hopefully, you'll get smth like this:</p>
<p><img class="aligncenter wp-image-755 " src="{{ site.baseurl }}/assets/2017/12/test-result-1.png" alt="" width="633" height="226" /></p>
<p>It means that at least you received <strong>200 ok</strong> and your body is correct. Now let's go to your table in the DynamoDB and make sure that the next item had been added.</p>
<p>Yay!!!</p>
<p><img class="aligncenter wp-image-756 " src="{{ site.baseurl }}/assets/2017/12/db-list.png" alt="New record in DynamoDB" width="438" height="289" /></p>
<p>Thank you for reading and see you on the third lesson, where we will add a trigger for our DynamoDB table!		</p>
