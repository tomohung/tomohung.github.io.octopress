---
layout: post
title: "Tealeaf Course2 Lesson3 Quiz"
date: 2015-02-15 08:22:00 +0800
comments: true
categories: [rails, tealeaf]
---


> ####1. What's the difference between rendering and redirecting? What's the impact with regards to instance variables, view templates?

- rendering: render a template HTML code, like `partial`.
- redirecting: make a new request to controller.

<!--more-->

Regards to instance variables for showing view templates, instance variables only survive at the same request, so if we need some information like errors store in instance variables, we need to use `render` for this condition.

> ####2. If I need to display a message on the view template, and I'm redirecting, what's the easiest way to accomplish this?

Use `flash` to store error message. This is the easiest way to store `one message`.

> ####3. If I need to display a message on the view template, and I'm rendering, what's the easiest way to accomplish this?

Use `instance variable` to store errors, then `render`.

\* or use `flash.now` and render it.

> ####4. Explain how we should save passwords to the database.

Use `one way hash`. Since we can't promise that data in database never be stolen, the way to protect our password is using a `one way hash` to store the digest. If someone get this digest, it's hard for him to decyphor this digest to a plain text password.

> ####5. What should we do if we have a method that is used in both controllers and views?

Put the methods into controllers, and make them become helpers. Like:

```
helper_method :current_user
```

Now, method `current_user` can be used in both controllers and views.

> ####6. What is memoization? How is it a performance optimization?

Some repeatedly query action for database will reduce our app performance, using `memoization` to overcome this.

```
@comment ||= User.find(session[:user_id]) if session[:user_id]
```

Syntax `||=` do a `OR` combined action, if `@comment` is `nil`, then `User.find` invokes; if `@comment` exist, just return @comment without invoke `User.find`. This prevent to invoke database query again.

> ####7. If we want to prevent unauthenticated users from creating a new comment on a post, what should we do?

Add a helper method `required_login` in `before_action` for actions `create` and ''. Since this method is used in both `post` and `comment`, we can put this method into `ApplicationController.rb`.


> ####8. Suppose we have the following table for tracking "likes" in our application. How can we make this table polymorphic? Note that the "user_id" foreign key is tracking who created the like.

| id || user_id || photo_id  || video_id  || post_id|
|-----:||----:||-----:||------:||-------:|
|1 ||4 || || 12 || |
|2 ||7 ||  ||  ||3 |
|3 ||2 ||6 ||  || |


According to this table, there should be 5 tables in database:

- User
- Photo
- Video
- Post
- Like

And here are they might be in models:

```ruby User.rb
class User ActiveRecord::Base
  has_many :likes, as: likeable
end
```

```ruby Photo.rb
class Photo ActiveRecord::Base
  has_many :likes, as: likeable
end
```

```ruby Video.rb
class Video ActiveRecord::Base
  has_many :likes, as: likeable
end
```

```ruby Post.rb
class Post ActiveRecord::Base
  has_many :likes, as: likeable
end
```

```ruby Like.rb
class Like ActiveRecord::Base
  belongs_to :user_id
  belongs_to :likeable, polymorphic: true
end
```


> ####9. How do we set up polymorphic associations at the model layer? Give example for the polymorphic model (eg, Vote) as well as an example parent model (the model on the 1 side, eg, Post).

```ruby 
class Vote < ActiveRecord::Base
  belongs_to :voteable, polymorphic: true
end

class Post < ActiveRecord::Base
  has_many :votes, as: voteable
end
```



> ####10. What is an ERD diagram, and why do we need it?

Entity Relationship Dirgram(ERD) stands for tables in database relationship between each others. It's a convinient way to communicate with others what the tables relationship, even for ourselves under developming.