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
excerpt: "If you followed my 3 my previous posts - you already created your first Amazon Lambda function, made it able to write to DynamoDB and be accessible from the outside world, using API Gateway."
---

If you followed my 3 my previous posts - you already created your first Amazon Lambda function, made it able to write to DynamoDB and be accessible from the outside world, using API Gateway.
In this post, I will guide you on how to implement the same but without touching the AWS Management Console, which is barely understandable and very volatile by the interface. Instead, we will be using Terraform, which I also covered in the past blog post.
Let's get started:
<!--more-->
If you don't have Terraform installed or you have no clue what is it and how to use it - please <a href="http://zonov.me/terrafom-introduction-with-github/" target="_blank" rel="noopener">read first this post</a>, it's pretty explanatory. If you have - please create a new file with whatever name you like, because it will be the only file. For me, it will be `zonov.tf`. For sure you wouldn't like to use that name in your company.
First, let's make sure that you have your AWS provider installed for Terraform. Add a basic config to your file:
{% highlight terraform %}# Provider
provider "aws" {
  region = "eu-west-1"
}{% endhighlight %}
And then go to the console, to the folder you saved your file and perform <code>terraform plan</code>.
Probably it will tell you smth like:
<img class="aligncenter wp-image-758 " src="{{ site.baseurl }}/assets/2018/01/terraform-plan.png" alt="terraform plan fails" width="543" height="250" />

It means that you just need to perform <code>terraform init</code>, which will install the needed provider:
<img class="aligncenter wp-image-759 " src="{{ site.baseurl }}/assets/2018/01/terraform-init.png" alt="terraform init installs aws provider" width="521" height="291" />

Then you can try to "plan" again and it will be green:
<img class="aligncenter wp-image-761 " src="{{ site.baseurl }}/assets/2018/01/terraform-plan-2.png" alt="terraform plan succeeds" width="544" height="182" />

Ok, cool, let's start adding real resources in there.
Add the following to your TF file:
{% highlight terraform %}# Lambda
resource "aws_iam_role" "lambda" {
  name = "kzonovGreeterRoleTF"
  assume_role_policy = "${data.aws_iam_policy_document.lambda-assume-role.json}"
}
data "aws_iam_policy_document" "lambda-assume-role" {
  statement {
    actions = ["sts:AssumeRole"]
    principals {
      type = "Service"
      identifiers = ["lambda.amazonaws.com"]
    }
  }
}{% endhighlight %}
A couple of words, about what is happening here. In the first block, we create an IAM Role, which we name "lambda" and we say that it can be assumed by service <code>lambda.amazonaws.com</code>. Basically, it means that we give AWS's Lambda service the ability to work with permissions of given IAM role.
Now you already can create your role on AWS. Don't hesitate, it's free. Again:
<code>terraform plan</code>

<img class="aligncenter wp-image-762 " src="{{ site.baseurl }}/assets/2018/01/terraform-plan-3.png" alt="terraform plan with iam role" width="525" height="352" />

And now you can actually create it: <code>terraform apply</code>

<img class="aligncenter wp-image-766 " src="{{ site.baseurl }}/assets/2018/01/terraform-apply-1.png" alt="terraform apply with iam role" width="571" height="164" />

Yay! Ok, now it's time to create a Lambda function. Here we will go by the easiest Hello world example, if you missed the explanation post - <a href="http://zonov.me/amazon-lambda-api-gateway-introduction/" target="_blank" rel="noopener">take a look here</a>, in this one I'll just paste this code into the new file <code>lambda.js</code> which you should put into the same folder.

{% highlight terraform %}exports.handler = (event, context, callback) => {
  callback(null, {
    statusCode: '200',
    body: 'Hello ' + event.queryStringParameters["name"] + '!'
  });
};{% endhighlight %}
When you deploy your Lambda function to AWS, it should be packed into .zip. So we add following to our `.tf` file:
{% highlight terraform %}data "archive_file" "lambda" {
  type = "zip"
  source_file = "lambda.js"
  output_path = "lambda.zip"
}{% endhighlight %}
With this, we use terraform's tool <code>archive_file</code> which tadaaaam, archives provided file to the specified archive type. And the next block to your TF file.
{% highlight terraform %}resource "aws_lambda_function" "greeter-lambda" {
  filename = "${data.archive_file.lambda.output_path}"
  function_name = "kzonovGreeterLambdaTF"
  role = "${aws_iam_role.lambda.arn}"
  handler = "lambda.handler"
  runtime = "nodejs6.10"
  source_code_hash = "${base64sha256(file(data.archive_file.lambda.output_path))}"
}{% endhighlight %}
Here we finally create our Lambda function, using the archive created by the last block, gives a described on the top role and tells that it should be interpreted using nodejs v6.10.
Next, I want you to give your lambda function ability to write to Cloudwatch, it's AWS log service. If you create a function from Management console - you'll have it by default, but using terrafrom you should specify it manually.
{% highlight terraform %}
resource "aws_iam_role_policy" "lambda-cloudwatch-log-group" {
  name = "kzonov-cloudwatch-log-group"
  role = "${aws_iam_role.lambda.name}"
  policy = "${data.aws_iam_policy_document.cloudwatch-log-group-lambda.json}"
}
data "aws_iam_policy_document" "cloudwatch-log-group-lambda" {
  statement {
    actions = [
      "logs:CreateLogGroup",
      "logs:CreateLogStream",
      "logs:PutLogEvents"
    ]
    resources = [
      "arn:aws:logs:::*",
    ]
  }
}
{% endhighlight %}
The second block is a policy document, where you specify that the role, to which you will attach it, will be able to create log stream and put logs into it. And in the first one, you attach this policy document to your lambda's role.
Lambda's part is over, you can try to apply your config now. Don't forget to make <code>terraform plan</code> first, it's a good manner, like making <code>git diff</code> before adding files to the commit.
I expect after plan step you'll have an error because you have to perform <code>terraform init</code> in order to install archive_file.
If you're such kind of error:

<img class="aligncenter wp-image-765 " src="{{ site.baseurl }}/assets/2018/01/terraform-error.png" alt="No permissions aws lambda error" width="591" height="129" />

check that your user, you're using for logging in, has <code>AWSLambdaFullAccess</code> permissions (can be checked in IAM/Users/Permissions). After applying configuration you can go to the Management Console and check that your function had been created.
Next step for us is to create an API Gateway, let's do it.
First just add one block, which describes the fact that API should be created with a specific name:
{% highlight terraform %}
resource "aws_api_gateway_rest_api" "api" {
  name = "kzonovGreeterApiTF"
}
{% endhighlight %}
Every API has some resources and methods for them, so let's add such instructions:
{% highlight terraform %}
resource "aws_api_gateway_resource" "api-resource" {
  path_part = "greetings"
  parent_id = "${aws_api_gateway_rest_api.api.root_resource_id}"
  rest_api_id = "${aws_api_gateway_rest_api.api.id}"
}
resource "aws_api_gateway_method" "method" {
  rest_api_id = "${aws_api_gateway_rest_api.api.id}"
  resource_id = "${aws_api_gateway_resource.api-resource.id}"
  http_method = "GET"
  authorization = "NONE"
}
{% endhighlight %}
Here we create an API resource "greetings", which will have one get method. So our path will be smth like <code>https://someapi.com/greetings</code>. Keep in mind that you should specify both rest_api_id and resource_id for API method, even if it sounds redundant for you (for me it is).
The last thing we will create today is an integration of our API to the function and give permission to invoke it:
{% highlight terraform %}
resource "aws_api_gateway_integration" "integration" {
  rest_api_id = "${aws_api_gateway_rest_api.api.id}"
  resource_id = "${aws_api_gateway_resource.api-resource.id}"
  http_method = "${aws_api_gateway_method.method.http_method}"
  integration_http_method = "POST"
  type = "AWS_PROXY"
  uri = "arn:aws:apigateway:eu-west-1:lambda:path/2015-03-31/functions/${aws_lambda_function.greeter-lambda.arn}/invocations"
}
resource "aws_lambda_permission" "greeter-permissions" {
  statement_id = "AllowExecutionFromAPIGateway"
  action = "lambda:InvokeFunction"
  function_name = "${aws_lambda_function.greeter-lambda.arn}"
  principal = "apigateway.amazonaws.com"
}
{% endhighlight %}
Here we first create an integration of the specific function to the specific API method. Pay attention, the integration_http_method is POST here despite that for external HTTP it was GET.
The type I chose AWS_PROXY because it's the new and convenient type of integration AWS provides.
On the second block, we just give API Gateway ability to InvokeFunction.
After adding it, make plan and apply again and you should see a success message. (If you have 403 error, add <span class="ng-scope system-policy-name">AmazonAPIGatewayAdministrator permission for your user).</span>
After successful apply go to the Management console -> APIGateway -> your_api_name -> Actions -> Deploy API. Add a new stage and press Deploy. It will be deployed within a second and on the top you'll see a URL, like https://msid0tkout.execute-api.eu-west-1.amazonaws.com/production. Don't forget that your API resource is named "greetings" so copy the URL, add resource name and also add a query param "name" (like <code>https://msid0tkout.execute-api.eu-west-1.amazonaws.com/production/greetings?name=Kirill</code>). You've seen that we use it in our lambda, did you? So, this is your deployed API URL. Try it with your name and you'll get: <code>Hello Kirill!</code> (or smth else if your name by some reason is not Kirill)
Tadaam!
As you see, no rocket science is here, but I hope it was helpful for you!
 		
