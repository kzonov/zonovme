---
layout: post
title: JS on Backend in 2018. Tutorial. Part 3. Relationships and Sequelize ORM tuning.
date: 2018-03-09 01:07:02.000000000 +01:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Development
tags:
- expressjs
- nodejs
- postgresql
- ruby
- tutorial
meta:
  _edit_last: '1'
  _adinserter_block_exceptions: ''
  hefo_before: '0'
  hefo_after: '0'
  _syntaxhighlighter_encoded: '1'
  _wp_old_slug: js-on-backend-in-2018-tutorial-part-3-relationships-and-tuning-of-sequelize-orm
  _aioseop_description: This part of the tutorial is about how to add validations
    and relationships to Sequelize models in ExpressJS.
author:
  login: graffzon
  email: graffzon@gmail.com
  display_name: graffzon
  first_name: ''
  last_name: ''
permalink: "/js-on-backend-in-2018-tutorial-part-3-relationships-and-sequelize-orm-tuning/"
excerpt: "Hello, my dear, today we will dive deeper into the abyss of ORM configuration.

We will add one more resource, set a relationship between this one and the one we already had after this post and will cover some useful tuning options. Spoiler: we will even change a bit the old one. So, fasten your seatbelts, let’s start!"
---

Hello, my dear, today we will dive deeper into the abyss of ORM configuration.
We will add one more resource, set a relationship between this one and the one we already had after <a href="http://zonov.me/js-on-backend-in-2018-tutorial-part-2-adding-a-database-and-sequelize-orm/" target="_blank" rel="noopener">this post</a> and will cover some useful tuning options. Spoiler: we will even change a bit the old one. So, fasten your seatbelts, let's start!
<!--more-->
<h2>Improving the previous model</h2>
Do you remember the command
{% highlight shell %}node_modules/.bin/sequelize db:migrate{% endhighlight %}
from the previous post? After model generation, we now have some default namings, like such fields as createdAt, so they are camelcased, but if you as me came from non-js world, you probably believe that your tables should be underscored! So let's open the migration file (you can find it in db/migrations) and edit it to make it look like this:
{% highlight javascript %}
'use strict';
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('books', {
      id: {
        allowNull: false,
        autoIncrement: true,
        primaryKey: true,
        type: Sequelize.INTEGER
      },
      title: {
        type: Sequelize.STRING
      },
      created_at: {
        allowNull: false,
        type: Sequelize.DATE
      },
      updated_at: {
        allowNull: false,
        type: Sequelize.DATE
      }
    });
  },
  down: (queryInterface, Sequelize) => {
    return queryInterface.dropTable('books');
  }
};
{% endhighlight %}
Here I changed our table name and createdAt & updatedAt fields. It is up to you which style to prefer, but I believe that camelcase should stay aside of a database.
Now you can go and change your model definition:
{% highlight javascript %}
module.exports = (sequelize, DataTypes) => {
  var Book = sequelize.define('Book', {
    title: DataTypes.STRING
  }, { tableName: 'books', underscored: true });
  Book.associate = function(models) {
    // associations can be defined here
  };
  return Book;
};
{% endhighlight %}
Here you not only explicitly set the tableName, which I find a good approach, but also pass the parameter `underscored`, which will perceive your DB fields like default timestamps as underscored. First roll back your previous migration:
{% highlight shell %}node_modules/.bin/sequelize db:migrate:undo{% endhighlight %}
Ok, now you're ready to re-migrate your migrations, with the same command as before:
{% highlight shell %}node_modules/.bin/sequelize db:migrate{% endhighlight %}
Lovely :)
<h2>Adding a new model with relationships</h2>
Let's move on to the new model. We're building a book club, where you have all sorts of books in your system and visitors can list them, and registered users can also discuss them (per se "leave comments"). In this part of the tutorial we will build a relationship between books and comments, so every book may have many comments. To achieve it we need a relationship "has_many" and "belongs_to". If you're absolutely unfamiliar with the concept of "relationships" between models - I will include the link to more thorough official documentation. Reading it is not required to build your first app, but if you want to know all possible config options - that's the only source of truth (better source is only the source code).
So, let's create our second model:
{% highlight shell %}node_modules/.bin/sequelize model:generate --name comment --attributes text:text,author:string,book_id:integer{% endhighlight %}
If you came from Rails world, you might be confused by the syntax, all that commas look strange for us. One more thing to notice is that I won't be able to find a proper way of generating migrations together with references data and underscored timestamp names. Because of that we should go to the newly created migration and change it to the following:
{% highlight javascript %}
'use strict';
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('comments', {
      id: {
        allowNull: false,
        autoIncrement: true,
        primaryKey: true,
        type: Sequelize.INTEGER
      },
      text: {
        type: Sequelize.TEXT
      },
      author: {
        type: Sequelize.STRING
      },
      book_id: {
        type: Sequelize.INTEGER,
        references: {
          model: 'books',
          key: 'id'
        },
        onUpdate: 'cascade',
        onDelete: 'cascade'
      },
      created_at: {
        allowNull: false,
        type: Sequelize.DATE
      },
      updated_at: {
        allowNull: false,
        type: Sequelize.DATE
      }
    });
  },
  down: (queryInterface, Sequelize) => {
    return queryInterface.dropTable('comments');
  }
};
{% endhighlight %}
I changed createdAt and updatedAt to created_at and updated_at respectively. Also I added such params to the `book_id` field:
{% highlight javascript %}
references: {
  model: 'books',
  key: 'id'
},
onUpdate: 'cascade',
onDelete: 'cascade'
{% endhighlight %}
Which as can be seen, says that `book_id` is not just a field, but a reference to the books model with the `id` key associated with this field. `Cascade` means that in case of book removal, all its comments will also be removed.
Now when the migrations part is done, feel free to migrate them, using the same command as in the previous post:
{% highlight shell %}node_modules/.bin/sequelize db:migrate{% endhighlight %}
Now go and change your model to make it look like this:
{% highlight javascript %}
'use strict';
module.exports = (sequelize, DataTypes) => {
  var Comment = sequelize.define('comment', {
    text: {
      type: DataTypes.STRING,
      validate: {
        notEmpty: true
      }
    },
    author: {
      type: DataTypes.STRING,
      validate: {
        notEmpty: true
      }
    }
  }, { tableName: 'comments', underscored: true });
  comment.associate = function(models) {
    Comment.belongsTo(models.Book, { foreignKey: 'book_id', allowNull: false })
  };
  return Comment;
};
{% endhighlight %}
Here I changed `comment`->`Comment`, because it makes sense to have Comment model to start with capital letter, but the table with downcased, which you may see in the `tableName` option. One new thing we have here is `associate` function. In it we set one-to-many relationship to the Book, using the key `book_id`. Also, you may notice that we have validations on this model. Confusing thing is that it will still allow you to pass `null` value. And even more weird thing is that Sequelize has options like `notNull: true`, but it is deprecated. So in order to have a real validation on null value, go back to your migration (yep, undo it first), and add `allowNull: false` to your fileds definitions, f.e.:
{% highlight javascript %}
text: {
  allowNull: false,
  type: Sequelize.STRING
}
{% endhighlight %}
<h2>How to test Sequelize models in console</h2>
As you may know, my main everyday language is Ruby, so I'm really have got used to `rails c` when I was using it on an everyday base. Now I'm working with Grape and I have pry-console set up for all my purposes and when I needed to test my Sequelize models, it confused me a lot. But it turned to be pretty easy.
First run `node` from your terminal, it will open node cli, the analog of Ruby's `irb`.
Then in it you can first require your models:
{% highlight javascript %}
const models = require('./src/models/index.js');
{% endhighlight %}
As you may notice, you should not require them individually, because your `models/index.js` file contains some glue code to make your models to be loaded. Then once your models loaded - you can go wild:
{% highlight javascript %}
// will return you the result query and empty response, because you don't have any comments yet
models.Comment.findAll().then(console.log);
// will return you a validation error because the book_id, you're referencing to, doesn't exist
models.Comment.create({ text: 'foo', book_id: 100 }).then(comment => console.log(comment))
// will create a book and return you it's data
models.Book.create({ title: 'War and peace' }).then(book => console.log(book))
// let's assume that your brand new book had an id 1, so now you can use its id to create a comment to it
models.Comment.create({ text: 'oh my gosh, such a book!', author: 'reader', book_id: 1 }).then(comment => console.log(comment))
{% endhighlight %}
Please keep in mind, that I'm assuming that you have <a href="http://zonov.me/js-on-backend-in-2018-tutorial-part-2-adding-a-database-and-sequelize-orm/" target="_blank" rel="noopener">a code from the previous post</a>.
That's it, I hope it was useful to you, my friends. Expect the next post in a couple of weeks. Last time I'm writing slower because I'm deep in my another blogging project - CodeRunFun, which you can find on Youtube: <a href="https://www.youtube.com/channel/UCjYpgu18Nd6Wngbwp47MHaA" target="_blank" rel="noopener">https://www.youtube.com/channel/UCjYpgu18Nd6Wngbwp47MHaA</a>. It is much less structured, but I try to post videos almost every day and tell you what interesting had I learn on a particular day. Please subscribe or just give your feedback :)
See you soon!		
