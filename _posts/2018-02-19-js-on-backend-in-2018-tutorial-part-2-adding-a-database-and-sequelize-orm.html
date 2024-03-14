---
layout: post
title: JS on Backend in 2018. Tutorial. Part 2. Adding a database and Sequelize ORM.
date: 2018-02-19 22:31:57.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Databases
- Development
tags:
- database
- development
- expressjs
- nodejs
- postgresql
meta:
  _edit_last: '1'
  _adinserter_block_exceptions: ''
  hefo_before: '0'
  hefo_after: '0'
  _syntaxhighlighter_encoded: '1'
  _aioseop_description: In this post, we cover the topic of integrating a persistence
    level to your Node.js application. And in order to do this, we will use Sequelize
    package.
  _aioseop_title: JS on Backend in 2018. Tutorial. Part 2. Adding a database and Sequelize
    ORM.
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/js-on-backend-in-2018-tutorial-part-2-adding-a-database-and-sequelize-orm/"
excerpt: "\n\t\t\t\t\t\t"
---
<p>
				After <a href="http://zonov.me/js-on-backend-in-2018-tutorial-part-1-app-server/" target="_blank" rel="noopener">the previous post</a>, our application server works, can receive requests and even return some kind of responses, which is already great and you can go into production with it! But probably it would make sense to add some more functionality to it. And in this post, we will integrate a database and implement a RESTful API.</p>
<p><!--more--></p>
<p>As a database, I picked PostgreSQL, because for the app I'm building SQL solution is more suitable than NoSQL and among others, PostgreSQL is the best choice by mine opinion. Take it with a grain of salt, because your cases could be different and f.e. MongoDB is almost standard de-facto for Node.js apps. And again, don't take Mongo just because it's an almost standard de-facto :)</p>
<p>Basically, when you want to use a database for your application you always have the choice: ORM vs plain adapter. If you are the type of person, who wants to build all the repository/entity structure by his own - you probably would choose Adapter approach, and for you, there is a solution: <a href="https://github.com/vitaly-t/pg-promise" target="_blank" rel="noopener">https://github.com/vitaly-t/pg-promise</a>. This library not just a plain adapter, but makes it easier to connect to a database and provides a promise-based interface for queries. But for most cases picking the right ORM is the way to go. And I found that Sequelize is really useful ORM. It provides you comprehensive query API, migrations and even basic generators, which could be useful at the start (btw don't misuse them). Just to mention, Sequelize also supports managing read replicas, which can be quite useful if you develop your app database layer with a distributed nature.</p>
<p>Let's start with adding it as a dependency.</p>
<pre><code>npm install sequelize --save</code>
<code>npm install --save pg pg-hstore</code></pre>
<p>First command will install the Sequelize package and the second one will add you PostgreSQL provider and HStore package, which is necessary if you would want to use hstore data type in postgres. If you're not familiar with this data type - you can check it here: <a href="https://www.postgresql.org/docs/current/static/hstore.html" target="_blank" rel="noopener">https://www.postgresql.org/docs/current/static/hstore.html</a>. It is a data type, which gives you kind of a schemaless data format.</p>
<p>Next we can setup everything manually, or use the generators! I will give a preference to generators in this case, but will explain you everything step by step. First, let's install the command line utility from Sequelize:</p>
<pre><code>npm install --save sequelize-cli</code></pre>
<p>Then create a file .sequelizerc and insert there such config:<br />
[javascript]<br />
const path = require('path');</p>
<p>module.exports = {<br />
  'config': path.resolve('config', 'database.json'),<br />
  'models-path': path.resolve('src', 'models'),<br />
  'seeders-path': path.resolve('db', 'seeders'),<br />
  'migrations-path': path.resolve('db', 'migrations')<br />
}<br />
[/javascript]</p>
<p>Here we modify the default paths to have more clean structure than the one they suggest. Your models will be in <code>src/models</code> folder, config in <code>config/</code> and db related stuff in <code>db/</code>. Convenient, isn't it?</p>
<p>Next let's automatically create all these folders:</p>
<pre><code>node_modules/.bin/sequelize init</code></pre>
<p>Nothing fancy here, this command will create folders for you, plus database.json file, which you need to open now and fill with smth similar:<br />
[javascript]<br />
{<br />
  &quot;development&quot;: {<br />
    &quot;username&quot;: &quot;kirillzonov&quot;,<br />
    &quot;password&quot;: null,<br />
    &quot;database&quot;: &quot;worder&quot;,<br />
    &quot;host&quot;: &quot;127.0.0.1&quot;,<br />
    &quot;dialect&quot;: &quot;postgres&quot;<br />
  }<br />
}<br />
[/javascript]</p>
<p>The file contents should be clear, but I'll quickly explain it.</p>
<ul>
<li>here we have only <code>development</code> section, which means we don't have any config for test or production. I believe it makes sense to create them only when you'll need them.</li>
<li><code>username</code> is a name of your database user, you plan to access with.</li>
<li><code>password</code> is obviously his password, which is empty for me.</li>
<li><code>database</code> is the database name. If you don't have it yet - no worries, we'll create it in the next step.</li>
<li><code>host</code> is your localhost (127.0.0.1). At least for development environment.</li>
<li><code>dialect</code> can be one of postgres, mysql, sqlite or mssql. So for us it should be postgres.</li>
</ul>
<p>This command will create the database with the name you specified above:</p>
<pre><code>node_modules/.bin/sequelize db:create</code></pre>
<p>If you already have it - you'll get an error.</p>
<p>Now when you have everything configured, it's time to create your first model! <a href="https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller" target="_blank" rel="noopener">Wiki quote</a>:</p>
<p>"The <i>model</i> is the central component of the &lt;...MVC...&gt; pattern. It expresses the application's behavior in terms of the problem domain, independent of the user interface. It directly manages the data, logic and rules of the application."</p>
<pre><code>node_modules/.bin/sequelize model:generate --name Book --attributes title:string</code></pre>
<p>The command should be clear for you if you came from any of plenty MVC frameworks. Here we generate a model, named Book and having an attribute title with a type of string. At the same time, this command creates a migration for us, which then can be executed by:</p>
<pre><code>node_modules/.bin/sequelize db:migrate</code></pre>
<p>and the database table books will be added as well.</p>
<p>Let's take a look at what did these commands created for us. First thing is the migration file.</p>
<p>[javascript]<br />
'use strict';<br />
module.exports = {<br />
  up: (queryInterface, Sequelize) =&gt; {<br />
    return queryInterface.createTable('Books', {<br />
      id: {<br />
        allowNull: false,<br />
        autoIncrement: true,<br />
        primaryKey: true,<br />
        type: Sequelize.INTEGER<br />
      },<br />
      title: {<br />
        type: Sequelize.STRING<br />
      },<br />
      createdAt: {<br />
        allowNull: false,<br />
        type: Sequelize.DATE<br />
      },<br />
      updatedAt: {<br />
        allowNull: false,<br />
        type: Sequelize.DATE<br />
      }<br />
    });<br />
  },<br />
  down: (queryInterface, Sequelize) =&gt; {<br />
    return queryInterface.dropTable('Books');<br />
  }<br />
};<br />
[/javascript]</p>
<p>It is using the <code>createTable</code> command of Sequelize to create a table "Books" with set of default parameters (auto incrementable ID, and auto settable Updated At and Created At) and your specified attribute, "title".<br />
You may also notice that it has <code>up</code> and <code>down</code> blocks. The first one specifies what will happen when you execute the migration, the second one, what will happen if you "rollback" the migration (<code>db:rollback</code> task). And on your migration filename, you will notice a long prefix-number, which is a unique timestamp of your migration, which is needed to distinguish the order of migrations.</p>
<p>And here is the code of our model, book.js:</p>
<p>[javascript]<br />
module.exports = (sequelize, DataTypes) =&gt; {<br />
  var Book = sequelize.define('Book', {<br />
    title: DataTypes.STRING<br />
  }, {});<br />
  Book.associate = function(models) {<br />
    // associations can be defined here<br />
  };<br />
  return Book;<br />
};<br />
[/javascript]</p>
<p>This is a very thin file, in which we define the mapping of our DB table attributes into our app and also able to add some associations, which we will talk about in some later posts.</p>
<p>And just to test it, let's add seed file. Seed files are files with predefined data which you can you for setting up a development environment, for instance. Let's create one:</p>
<pre><code>node_modules/.bin/sequelize seed:generate --name demo-book</code></pre>
<p>Then open the newly created file and paste there following code:<br />
[javascript]<br />
'use strict';<br />
const models = require('../../src/models')</p>
<p>module.exports = {<br />
  up: (queryInterface, Sequelize) =&gt; {<br />
    return models.Book.create({<br />
      title: 'War and peace'<br />
    })<br />
  },</p>
<p>  down: (queryInterface, Sequelize) =&gt; {<br />
    return queryInterface.bulkDelete('Books', null, {});<br />
  }<br />
};<br />
[/javascript]</p>
<p>Here we define two interfaces, like in migrations files, one for performing the seed and one for rolling it back.<br />
So then go back to your command line and perform:</p>
<pre><code>node_modules/.bin/sequelize db:seed:all</code></pre>
<p>It will tell you smth like "migrated (0.051s)", which means success. Since we don't have any public interface to check our books, let's just go to <code>psql</code> and perform there:<br />
[sql]<br />
worder=# \c worder<br />
You are now connected to database &quot;worder&quot; as user &quot;kirillzonov&quot;.<br />
worder=# select * from &quot;Books&quot;;<br />
 id |  title        |         createdAt          |         updatedAt<br />
----+---------------+----------------------------+----------------------------<br />
  5 | War and peace | 2018-02-19 21:50:03.897+01 | 2018-02-19 21:50:03.897+01<br />
(1 row)<br />
[/sql]</p>
<p>Which means that the record have been created, voila!</p>
<p>I found this to be quite enough for one post because our next topic will require some explanation as well and I don't want to have one huge post, which none of you will be able to finish :)<br />
BTW, I promise you to publish it asap.</p>
<p>Thank you all, I'll appreciate your feedback if you leave any!		</p>
