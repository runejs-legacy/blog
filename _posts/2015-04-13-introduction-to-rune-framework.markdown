---
layout: post
title:  "Introduction to Rune Framework"
date:   2015-04-13 01:00:00
---

One month ago, I started to develop a new API framework for Node.js inspired by
Ruby on Rails using Ecmascript 7 (Babel.js) and now I want to give some
information and examples about the framework.


### Routing

```config/routes.js```
{% highlight javascript %}
export default (root) => {
  root.resources('posts', (posts) => {
    posts.resources('comments');
  });

  root.to('home#index');
}
{% endhighlight %}


### Controllers
```controllers/posts.js```
{% highlight javascript %}
export class PostsController extends Rune.Controller {
  async index() {
    let posts = await Post.fetch();
    this.$reply(posts);
  }

  async show() {
    let post = await Post.find(this.$params.id);
    this.$reply(post);
  }

  async create() {
    let post = new Post(this.$params);
    await post.save();
    this.$reply(post);
  }

  async destroy() {
    let post = Post.find(this.$params.id);
    await post.destroy();
    this.$reply({ message: 'deleted' });
  }
}
{% endhighlight %}

This is an example request:
{% highlight javascript %}
GET /posts
=> [{ _id: '...', title: '...', content: '...' }]
{% endhighlight %}

### Models

Rune also has an ORM framework for MongoDB.
Here is an example file ```models/post.js```
{% highlight javascript %}
export class Post extends Rune.Model {
  slugify() {
    this.slug = slug(this.title);
  }
}

Post.fields = {
  title: String,
  content: String,
  slug: String
}

Post.validations = {
  title: { required: true, maxLenght: 300 }
}

Post.before = {
  validation: ['slugify']
}
{% endhighlight %}