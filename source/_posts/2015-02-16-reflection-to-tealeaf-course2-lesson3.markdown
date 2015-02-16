---
layout: post
title: "Reflection to Tealeaf Course2 Lesson3"
date: 2015-02-16 00:37:25 +0000
comments: true
categories: [rails, tealeaf]
---

In Course2(Rapid Prototyping with Ruby on Rails), [first week][] I learned about `Models and database`, [second week][] I learned about `Controllers and Views`. This week I learned to add some features into my app, and make it vivid!!

[first week]: http://www.tomohung.com/blog/2015/02/02/reflection-to-tealeaf-course-2-lesson-1/
[second week]: http://www.tomohung.com/blog/2015/02/10/reflection-to-tealeaf-course2-lesson2/

<!--more-->

###Authentication
Rails offer built-in function to help us add authentication. Here are steps:

- install gem 'Bcrypt'.

```
gem 'bcrypt', '~> 3.1.7' # use gem `bcrypt-ruby`, `=3.0.1` if any problem
```

- run `bundle install` to install gem.
- add migration for attribute:`password_digest`, like:

  ```
  class AddPasswordDigestToUsers < ActiveRecord::Migration
    def change
      add_column :users, :password_digest, :string
    end
  end
  ```
- add method to `Use Model`

```ruby User.rb
class User < ActiveRecord::Base
  has_secure_password
end
```

`has_secure_password` built-in some validations, if we want to customize validation, add parameter:

```ruby
has_secure_password validations: false
```

here are some test from Rails API:
```ruby
user = User.new(name: 'david', password: '', password_confirmation: 'nomatch')
user.save                                                       # => false, password required
user.password = 'mUc3m00RsqyRe'
user.save                                                       # => false, confirmation doesn't match
user.password_confirmation = 'mUc3m00RsqyRe'
user.save                                                       # => true
user.authenticate('notright')                                   # => false
user.authenticate('mUc3m00RsqyRe')                              # => user
User.find_by(name: 'david').try(:authenticate, 'notright')      # => false
User.find_by(name: 'david').try(:authenticate, 'mUc3m00RsqyRe') # => user
```

###Login / Logout

We can use `session` to store our login information. First add routes

```ruby
  get '/register', to: 'users#new'
  get '/login', to: 'sessions#new'
  post '/login', to: 'sessions#create'
  delete '/logout', to: 'sessions#destroy'
```

And `SessionsController`:

```ruby SessionController.rb
class SessionsController < ApplicationController
  
  def new 
    @user = User.new
  end
  
  def create
    user = User.find_by username: params[:username]
    if user && user.authenticate(params[:password])
      flash[:notice] = 'Login Success.'
      session[:user_id] = user.id
      redirect_to root_path
    else
      flash[:error] = "Username or Password may not correct. Try again."
      render :new
    end
  end
  
  def destroy
    session[:user_id] = nil
    flash[:notice] = 'Log out'
    redirect_to root_path
  end
  
end
```

###Polymorphic table

Sometimes many tables have relationship with the same one table, like `User` has many gravatar images, and `Post` has many images. So `User` and `Post` are using the same table `Picture`. We can do `Polymorpic table` association like this:

```ruby
class Picture < ActiveRecord::Base
  belongs_to :imageable, polymorphic: true
end
 
class User < ActiveRecord::Base
  has_many :pictures, as: :imageable
end
 
class Post < ActiveRecord::Base
  has_many :pictures, as: :imageable
end
```

Then Rails will automatically assign two virtual attribute for us:`imageable_type` and `imageable_id`

`type` is stored which object type use this Picture, and `id` is for that object's table id.

Then, we can save data like this:

```ruby
@picture = Picture.create(imageable: post, user_id: current_user_id)
```

###Deploy to Heroku

Last step, deploy to heroku makes this app engage with people!!

The only thing I'll record here is `make sure to run **heroku run rake db:migrate**`. Otherwise heroku will not generate a database for us.


###Conclusion

Seems that my app becomes more and more closer to real world app we used everyday. There're still a lot of things to combine them together for improvement, like `tab control`, `Ajax`, `friendly routes path name`, etc.

Security is one of the most imporatant thing when we using internet, it's absolutely not safe if we store our password by plain text. Use a `one way has/ password digest` to instead of plain text password. There is one more thing, `MD5` has been broken too.