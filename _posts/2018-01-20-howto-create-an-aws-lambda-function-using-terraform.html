---
layout: post
title: 'HOWTO: Create and integrate AWS Lambda function using Terraform'
date: 2018-01-20 19:00:27.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- AWS
- DevOps
tags:
- amazon
- aws
- devops
- lambda
- serverless
- terraform
meta:
  _edit_last: '1'
  hefo_before: '0'
  hefo_after: '0'
  _syntaxhighlighter_encoded: '1'
  _adinserter_block_exceptions: ''
  _aioseop_description: Article describing the process of adding AWS Lambda function
    with API Gateway integration using Terraform.
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/howto-create-an-aws-lambda-function-using-terraform/"
excerpt: "\n\t\t\t\t\t\t"
---
<p>
				If you followed my 3 my previous posts - you already created your first Amazon Lambda function, made it able to write to DynamoDB and be accessible from the outside world, using API Gateway.</p>
<p>In this post, I will guide you on how to implement the same but without touching the AWS Management Console, which is barely understandable and very volatile by the interface. Instead, we will be using Terraform, which I also covered in the past blog post.</p>
<p>Let's get started:</p>
<p><!--more--></p>
<p>If you don't have Terraform installed or you have no clue what is it and how to use it - please <a href="http://zonov.me/terrafom-introduction-with-github/" target="_blank" rel="noopener">read first this post</a>, it's pretty explanatory. If you have - please create a new file with whatever name you like, because it will be the only file. For me, it will be `zonov.tf`. For sure you wouldn't like to use that name in your company.</p>
<p>First, let's make sure that you have your AWS provider installed for Terraform. Add a basic config to your file:</p>
<p>[javascript]# Provider</p>
<p>provider &quot;aws&quot; {<br />
  region = &quot;eu-west-1&quot;<br />
}[/javascript]</p>
<p>And then go to the console, to the folder you saved your file and perform <code>terraform plan</code>.</p>
<p>Probably it will tell you smth like:</p>
<p><img class="aligncenter wp-image-758 " src="{{ site.baseurl }}/assets/2018/01/terraform-plan.png" alt="terraform plan fails" width="543" height="250" /></p>
<p>It means that you just need to perform <code>terraform init</code>, which will install the needed provider:</p>
<p><img class="aligncenter wp-image-759 " src="{{ site.baseurl }}/assets/2018/01/terraform-init.png" alt="terraform init installs aws provider" width="521" height="291" /></p>
<p>Then you can try to "plan" again and it will be green:</p>
<p><img class="aligncenter wp-image-761 " src="{{ site.baseurl }}/assets/2018/01/terraform-plan-2.png" alt="terraform plan succeeds" width="544" height="182" /></p>
<p>Ok, cool, let's start adding real resources in there.</p>
<p>Add the following to your TF file:</p>
<p>[javascript]# Lambda</p>
<p>resource &quot;aws_iam_role&quot; &quot;lambda&quot; {<br />
  name = &quot;kzonovGreeterRoleTF&quot;<br />
  assume_role_policy = &quot;${data.aws_iam_policy_document.lambda-assume-role.json}&quot;<br />
}</p>
<p>data &quot;aws_iam_policy_document&quot; &quot;lambda-assume-role&quot; {<br />
  statement {<br />
    actions = [&quot;sts:AssumeRole&quot;]</p>
<p>    principals {<br />
      type = &quot;Service&quot;<br />
      identifiers = [&quot;lambda.amazonaws.com&quot;]<br />
    }<br />
  }<br />
}[/javascript]</p>
<p>A couple of words, about what is happening here. In the first block, we create an IAM Role, which we name "lambda" and we say that it can be assumed by service <code>lambda.amazonaws.com</code>. Basically, it means that we give AWS's Lambda service the ability to work with permissions of given IAM role.</p>
<p>Now you already can create your role on AWS. Don't hesitate, it's free. Again:</p>
<p><code>terraform plan</code></p>
<p><img class="aligncenter wp-image-762 " src="{{ site.baseurl }}/assets/2018/01/terraform-plan-3.png" alt="terraform plan with iam role" width="525" height="352" /></p>
<p>And now you can actually create it: <code>terraform apply</code></p>
<p><img class="aligncenter wp-image-766 " src="{{ site.baseurl }}/assets/2018/01/terraform-apply-1.png" alt="terraform apply with iam role" width="571" height="164" /></p>
<p>Yay! Ok, now it's time to create a Lambda function. Here we will go by the easiest Hello world example, if you missed the explanation post - <a href="http://zonov.me/amazon-lambda-api-gateway-introduction/" target="_blank" rel="noopener">take a look here</a>, in this one I'll just paste this code into the new file <code>lambda.js</code> which you should put into the same folder.</p>
<p>[javascript]exports.handler = (event, context, callback) =&gt; {<br />
  callback(null, {<br />
    statusCode: '200',<br />
    body: 'Hello ' + event.queryStringParameters[&quot;name&quot;] + '!'<br />
  });<br />
};[/javascript]</p>
<p>When you deploy your Lambda function to AWS, it should be packed into .zip. So we add following to our `.tf` file:</p>
<p>[javascript]data &quot;archive_file&quot; &quot;lambda&quot; {<br />
  type = &quot;zip&quot;<br />
  source_file = &quot;lambda.js&quot;<br />
  output_path = &quot;lambda.zip&quot;<br />
}[/javascript]</p>
<p>With this, we use terraform's tool <code>archive_file</code> which tadaaaam, archives provided file to the specified archive type. And the next block to your TF file.</p>
<p>[javascript]resource &quot;aws_lambda_function&quot; &quot;greeter-lambda&quot; {<br />
  filename = &quot;${data.archive_file.lambda.output_path}&quot;<br />
  function_name = &quot;kzonovGreeterLambdaTF&quot;<br />
  role = &quot;${aws_iam_role.lambda.arn}&quot;<br />
  handler = &quot;lambda.handler&quot;<br />
  runtime = &quot;nodejs6.10&quot;<br />
  source_code_hash = &quot;${base64sha256(file(data.archive_file.lambda.output_path))}&quot;<br />
}[/javascript]</p>
<p>Here we finally create our Lambda function, using the archive created by the last block, gives a described on the top role and tells that it should be interpreted using nodejs v6.10.<br />
Next, I want you to give your lambda function ability to write to Cloudwatch, it's AWS log service. If you create a function from Management console - you'll have it by default, but using terrafrom you should specify it manually.</p>
<p>[javascript]<br />
resource &quot;aws_iam_role_policy&quot; &quot;lambda-cloudwatch-log-group&quot; {<br />
  name = &quot;kzonov-cloudwatch-log-group&quot;<br />
  role = &quot;${aws_iam_role.lambda.name}&quot;<br />
  policy = &quot;${data.aws_iam_policy_document.cloudwatch-log-group-lambda.json}&quot;<br />
}</p>
<p>data &quot;aws_iam_policy_document&quot; &quot;cloudwatch-log-group-lambda&quot; {<br />
  statement {<br />
    actions = [<br />
      &quot;logs:CreateLogGroup&quot;,<br />
      &quot;logs:CreateLogStream&quot;,<br />
      &quot;logs:PutLogEvents&quot;<br />
    ]</p>
<p>    resources = [<br />
      &quot;arn:aws:logs:::*&quot;,<br />
    ]<br />
  }<br />
}<br />
[/javascript]</p>
<p>The second block is a policy document, where you specify that the role, to which you will attach it, will be able to create log stream and put logs into it. And in the first one, you attach this policy document to your lambda's role.</p>
<p>Lambda's part is over, you can try to apply your config now. Don't forget to make <code>terraform plan</code> first, it's a good manner, like making <code>git diff</code> before adding files to the commit.</p>
<p>I expect after plan step you'll have an error because you have to perform <code>terraform init</code> in order to install archive_file.</p>
<p>If you're such kind of error:</p>
<p><img class="aligncenter wp-image-765 " src="{{ site.baseurl }}/assets/2018/01/terraform-error.png" alt="No permissions aws lambda error" width="591" height="129" /></p>
<p>check that your user, you're using for logging in, has <code>AWSLambdaFullAccess</code> permissions (can be checked in IAM/Users/Permissions). After applying configuration you can go to the Management Console and check that your function had been created.</p>
<p>Next step for us is to create an API Gateway, let's do it.</p>
<p>First just add one block, which describes the fact that API should be created with a specific name:</p>
<p>[javascript]<br />
resource &quot;aws_api_gateway_rest_api&quot; &quot;api&quot; {<br />
  name = &quot;kzonovGreeterApiTF&quot;<br />
}<br />
[/javascript]</p>
<p>Every API has some resources and methods for them, so let's add such instructions:</p>
<p>[javascript]<br />
resource &quot;aws_api_gateway_resource&quot; &quot;api-resource&quot; {<br />
  path_part = &quot;greetings&quot;<br />
  parent_id = &quot;${aws_api_gateway_rest_api.api.root_resource_id}&quot;<br />
  rest_api_id = &quot;${aws_api_gateway_rest_api.api.id}&quot;<br />
}</p>
<p>resource &quot;aws_api_gateway_method&quot; &quot;method&quot; {<br />
  rest_api_id = &quot;${aws_api_gateway_rest_api.api.id}&quot;<br />
  resource_id = &quot;${aws_api_gateway_resource.api-resource.id}&quot;<br />
  http_method = &quot;GET&quot;<br />
  authorization = &quot;NONE&quot;<br />
}<br />
[/javascript]</p>
<p>Here we create an API resource "greetings", which will have one get method. So our path will be smth like <code>https://someapi.com/greetings</code>. Keep in mind that you should specify both rest_api_id and resource_id for API method, even if it sounds redundant for you (for me it is).</p>
<p>The last thing we will create today is an integration of our API to the function and give permission to invoke it:</p>
<p>[javascript]<br />
resource &quot;aws_api_gateway_integration&quot; &quot;integration&quot; {<br />
  rest_api_id = &quot;${aws_api_gateway_rest_api.api.id}&quot;<br />
  resource_id = &quot;${aws_api_gateway_resource.api-resource.id}&quot;<br />
  http_method = &quot;${aws_api_gateway_method.method.http_method}&quot;<br />
  integration_http_method = &quot;POST&quot;<br />
  type = &quot;AWS_PROXY&quot;<br />
  uri = &quot;arn:aws:apigateway:eu-west-1:lambda:path/2015-03-31/functions/${aws_lambda_function.greeter-lambda.arn}/invocations&quot;<br />
}</p>
<p>resource &quot;aws_lambda_permission&quot; &quot;greeter-permissions&quot; {<br />
  statement_id = &quot;AllowExecutionFromAPIGateway&quot;<br />
  action = &quot;lambda:InvokeFunction&quot;<br />
  function_name = &quot;${aws_lambda_function.greeter-lambda.arn}&quot;<br />
  principal = &quot;apigateway.amazonaws.com&quot;<br />
}<br />
[/javascript]</p>
<p>Here we first create an integration of the specific function to the specific API method. Pay attention, the integration_http_method is POST here despite that for external HTTP it was GET.<br />
The type I chose AWS_PROXY because it's the new and convenient type of integration AWS provides.<br />
On the second block, we just give API Gateway ability to InvokeFunction.</p>
<p>After adding it, make plan and apply again and you should see a success message. (If you have 403 error, add <span class="ng-scope system-policy-name">AmazonAPIGatewayAdministrator permission for your user).</span></p>
<p>After successful apply go to the Management console -&gt; APIGateway -&gt; your_api_name -&gt; Actions -&gt; Deploy API. Add a new stage and press Deploy. It will be deployed within a second and on the top you'll see a URL, like https://msid0tkout.execute-api.eu-west-1.amazonaws.com/production. Don't forget that your API resource is named "greetings" so copy the URL, add resource name and also add a query param "name" (like <code>https://msid0tkout.execute-api.eu-west-1.amazonaws.com/production/greetings?name=Kirill</code>). You've seen that we use it in our lambda, did you? So, this is your deployed API URL. Try it with your name and you'll get: <code>Hello Kirill!</code> (or smth else if your name by some reason is not Kirill)</p>
<p>Tadaam!</p>
<p>As you see, no rocket science is here, but I hope it was helpful for you!</p>
<p>&nbsp;		</p>
