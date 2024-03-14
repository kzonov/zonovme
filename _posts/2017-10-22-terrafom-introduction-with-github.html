---
layout: post
title: Introduction to Terraform. Terraform + Github
date: 2017-10-22 12:41:27.000000000 +02:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Development
- DevOps
tags:
- development
- devops
- github
- infrastructure
- infrastructure as a code
- terraform
meta:
  _edit_last: '1'
  _syntaxhighlighter_encoded: '1'
  _adinserter_block_exceptions: ''
  hefo_before: '0'
  hefo_after: '0'
  _aioseop_description: When should you use Terraform and how to start leveraging
    its power. Including an example of Github repositories management automatization.
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/terrafom-introduction-with-github/"
excerpt: "\n\t\t\t\t\t\t"
---
<p>
				Not a long time ago I had no idea about what is the tool named Terraform. It was just beyond my bounds of interests and problems. But a month ago I changed a company and then had to deal with it. And guys, it's wow. It's so amazing thing so I even started liking DevOps job.</p>
<p>In a few words, it's a tool to do infrastructure as a code. As they say on their website "Terraform enables you to safely and predictably create, change, and improve production infrastructure".</p>
<p>In this post, I'll cover why may you (as a developer or a DevOps) need it and how to get started. Getting started will be not just a hello world, but a real example, you can try right away.</p>
<p><!--more--></p>
<p>You as me can work for a decade with small-to-middle startups, even maybe have devops engineers, even 15+ developers in the team, even probably be a devops guy yourself, but still, know nothing about Terraform. Another thing may be that you work with Chef and it's absolutely enough for you. The thing is that you just didn't need it. But if you have 50+ repositories, 40+ developers, tons of lambda functions, EC2 instances, plenty of S3 buckets, you just cannot survive without it.</p>
<p>The good part here is that you don't need to wait until you get to this size and start codifying your infrastructure right now. Worth to mention that Terraform is not the only tool, there are also CloudFormation and Heap. I never tried them, but as I know one real competitor is CloudFormation, but it doesn't support so many providers as Terraform and afaik, also has nothing to do with so to speak "state" of a provider. If you just starting with it as me - no worries, just use Terraform, it's great, trust me %)</p>
<p>Just to summarize, you should use Terraform in case you don't want to manually manage repositories/servers/buckets/DNS and <a href="https://www.terraform.io/docs/providers/" target="_blank" rel="noopener">other things</a>, but wish code do it for you.</p>
<p>Let's start!</p>
<p><img class="wp-image-771 size-full alignleft" src="{{ site.baseurl }}/assets/2017/10/cat-239x300.png" alt="octocat with terraform" width="239" height="300" /></p>
<h2>Install Terraform on OSX</h2>
<p>If you use Homebrew, things go the same as usual:</p>
<pre><code>$ brew install terraform</code></pre>
<p>&nbsp;</p>
<p>It will install the terraform binary for you. Then just to check that it installed properly, try:<br />
[javascript]<br />
$ terraform<br />
Usage: terraform [--version] [--help] &amp;lt;command&amp;gt; [args]<br />
&lt;/code&gt;&lt;/pre&gt;<br />
&amp;nbsp;<br />
&lt;h2&gt;Write basic configuration&lt;/h2&gt;<br />
Create a file, let it be &lt;code&gt;github.tf&lt;/code&gt; and add there following code:<br />
&lt;pre&gt;&lt;code&gt;resource &quot;github_repository&quot; &quot;terraform-example&quot; {<br />
  name        = &quot;terraform-example&quot;<br />
  description = &quot;My example repo for terraform workshop&quot;</p>
<p>  private = false<br />
}[/javascript]<br />
&nbsp;</p>
<p>The format you use here is a Terraform-specific syntax. You can also use JSON, but they actually don't recommend it. It's a bit weird to write such constructions, but after some time they look pretty convenient.<br />
Let's see, what did we write here. Resource means that it's something you want to create for a remote provider. Provider, in this case, is Github, which as Terraform knows, works with the type you mentioned here - "github_repository". "terraform-example" is just an internal name you give to your future repository. It won't be displayed anywhere on Github, you need it just for yourself to work with it in the future (f.e. for access configuring etc.). Name and Description are actually name and description, which will be displayed on Github itself. Private is also a pretty clear option.</p>
<p>Next, try to see, is it basically proper syntaxed and what it will try to change on the remote server. Don't be afraid, this command is only local, it won't send anything to the outside world.</p>
<pre><code>$ terraform plan
Plugin reinitialization required. Please run "terraform init".
Reason: Could not satisfy plugin requirements.
</code></pre>
<p>&nbsp;</p>
<p>Wow, it doesn't work! But don't worry, it says pretty clear what should you do.</p>
<pre><code>$ terraform init</code></pre>
<p>&nbsp;</p>
<p>It will download and install a plugin for Github provider. No issues should be there after it finished - try again <code>terraform plan</code><br />
It will ask you for the organization name. Here is confusion, you cannot manage your personal repos, weeeeeird. So you should first create an organization on Github. And one more weird thing is that you cannot do it via Terraform, so go to Github and <a href="https://github.com/organizations/new" target="_blank" rel="noopener">do it manually</a>. After you are done - write its name in the input field in your terminal. For me, organization name is RacoonsGroup (yeah, I still own the repo ^_^). Then you'll need to input your personal Github access token. Be careful, it's not a password, it's a token, which you can generate from <a href="https://github.com/settings/tokens/new" target="_blank" rel="noopener">here</a>. Mark only first block, like here:<br />
<img class="aligncenter size-medium wp-image-382" src="{{ site.baseurl }}/assets/2017/10/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA-%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0-2017-10-22-%D0%B2-12.27.35-300x246.png" alt="" width="300" height="246" /><br />
Then generate the token, copy it and paste into your terminal prompt.<br />
Then you'll get some response like this:</p>
<pre><code>Terraform will perform the following actions:

  + github_repository.terraform-example
      id:                 
      allow_merge_commit: "true"
      allow_rebase_merge: "true"
      allow_squash_merge: "true"
      default_branch:     
      description:        "My example repo for terraform workshop"
      full_name:          
      git_clone_url:      
      http_clone_url:     
      name:               "terraform-example"
      private:            "false"
      ssh_clone_url:      
      svn_url:            


Plan: 1 to add, 0 to change, 0 to destroy.</code></pre>
<p>It says you that terrafom is going to create one repository with such params. As you may see - there are other options, you can specify in your <code>github.tf</code> file. Like allow_squash_merge, f.e.<br />
Ok, now it's time, to become super-serious and run:</p>
<pre><code>$ terraform apply</code></pre>
<p>As you may understand, this operation actually makes a request to the GitHub. You then will need to specify your GitHub options once more, don't be irritated by this.</p>
<pre><code>github_repository.terraform-example: Creating...
  allow_merge_commit: "" => "true"
  allow_rebase_merge: "" => "true"
  allow_squash_merge: "" => "true"
  default_branch:     "" => ""
  description:        "" => "My example repo for terraform workshop"
  full_name:          "" => ""
  git_clone_url:      "" => ""
  http_clone_url:     "" => ""
  name:               "" => "terraform-example"
  private:            "" => "false"
  ssh_clone_url:      "" => ""
  svn_url:            "" => ""
github_repository.terraform-example: Creation complete after 2s (ID: terraform-example)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.</code></pre>
<p>Yay! This is the result you want! Now you can go to the Github and check that you actually have this repo created.<br />
As you see, Terraform is a pretty simple tool, which can give you the power to manage at least Github repositories ;)<br />
P.S. If this post worth to share - don't be afraid to do it :)</p>
<p>&nbsp;		</p>
