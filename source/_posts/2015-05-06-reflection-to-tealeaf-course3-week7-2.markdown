---
layout: post
title: "Reflection to Tealeaf Course3 Week7 (2/3)"
date: 2015-05-06 15:42:37 +0800
comments: true
categories: [rails, tealeaf]
---

#Beyond MVC

Rails built on MVC (Model / View / Controller) structure. And something get complex if we got more code and logic. This is why we need design patterns.

##Decorators

Some tips from gem `Draper`:

> Without Draper, this functionality might have been tangled up in procedural helpers or adding bulk to your models. With Draper decorators, you can wrap your models with presentation-related logic to organise - and test - this layer of your app much more effectively.

Or

> Decorators are the ideal place to:

> - format complex data for user display
> - define commonly-used representations of an object, like a `name` method that combine `first_name` and `last_name` attributes.
> - mark up attributes with a little semantic HTML, like turning a url fiedl into a hyperlink.

First, install gem

```
gem 'draper'
```

```ruby app/decorator/video_decorator.rb
class VideoDecorator < Draper::Decorator
  delegate_all
  
  def rating
    "Rating: " + (object.rating.present? ? "#{object.rating}/5.0" : "NA")
  end
end
```

And use it in controller

```ruby videos_controller.rb
  def show
    @video = VideoDecorator.decorate(Vidoe.find(params[:id]))
  dne
```

In view, just a clear line code.

```ruby app/views/videos/show.html.haml
  = @video.rating
```

The key point here is that we have a model data (rating) associated with view, it's not suitable to put to `view helper` (some logic with model). That's why here using decorator to encapsulate presentation logic.

##Policy Objects

Define what is premium user:

```ruby models/user_level_policy.rb
class UserLevelPolicy
  attr_reader :user
  def initialize(user)
    @user = user
  end

  def premium?
    user.created_at < Date.new(2010,1,1) || user.plan.premium?
  end
end
```

In Controller

```ruby todo_controller.rb
###some code

  if UserLevelPolicy.new(current_user).premium?
    #do something
  else
    #do another thing
  end
```

Different with Decorators, Policy Ojbects only concern judgement logic into one place, without presentation concerned.

##Domain Objects

if `User` has an attribute `credit_balance`. Then merge those code relative to `credit` into Domain Object.

```ruby models/credit.rb
class Credit
  attr_accessor :credit_balance, :user

  def initialize(user)
    @credit_balance = user.current_credit_balance
    @user = user
  end

  def -(number)
    credit_balance = credit_balance - number
  end

  def save
    user.current_credit_balance = credit_balance
    user.save
  end

  def depleted?
    credit_balance < 0
  end

  def low_balance?
    credit_balance < 10
  end
end
```

Thus `Credit.new(current_user)` can call methods about credit, and don't need to put these code in model `User`, or `UsersController`.

##Service Objects

We abstract an action as `sign up` in `UsersController#create`. 

```ruby
class UsersController < ApplicationController
  def create    
    @user = User.new(user_params)
    user_signup_service = UserSignup.new(@user)
    result = user_signup_service.sign_up(params[:stripeToken], params[:token])

    if result.successful?
      session[:user_id] = @user.id
      redirect_to home_path
    else
      flash.now[:danger] =  user_signup_service.error_message
      render :new
    end
  end
end
```

Then create an service object `UserSignup` to handle this.

```ruby app/service/user_signup.rb
class UserSignup

  attr_reader :error_message

  def initialize(user)
    @user = user
  end

  def sign_up(stripe_token = nil, invitation_token = nil)
    if @user.valid?      
      charge = customer_charge_user_with_stripe(stripe_token)
      if charge.success?
        @user.customer_token = charge.customer_token
        @user.save
        set_invitation_following_relationship(invitation_token)
        AppMailer.delay.send_welcome_email(@user)
        @status = :success
        self
      else
        @status = :failed
        @error_message = charge.error_message
        self
      end
    else
      @status = :failed
      @error_message = "User info is invalid."
      self
    end
  end

  def successful?
    @status == :success
  end
end
```

There is an article from Code Climate [7 Patterns to Refactor Fat ActiveRecord Models](http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/)

As it metion when to use Service Object:

- The action is complex (e.g. closing the books at the end of an accounting period)
- The action reaches across multiple models (e.g. an e-commerce purchase using Order, CreditCard and Customer objects)
- The action interacts with an external service (e.g. posting to social networks)
- The action is not a core concern of the underlying model (e.g. sweeping up outdated data after a certain time period).
- There are multiple ways of performing the action (e.g. authenticating with an access token or password). This is the Gang of Four Strategy pattern.

##Some Concept for Object Oriented Design

- `Fat model, skinny controllers` makes model uncontrollrable. That's why we need Object Oriented Design

- YAGNI

YAGNI means `You Aren't Gonna Need It!`

Don't write the code you haven't need it.

Here is aother post talking about `YAGNI`, `KISS`, `DRY`.
[Software Development: KISS, YAGNI & DRY. 3 Principles to simplify your life.](http://www.itexico.com/blog/bid/99765/Software-Development-KISS-YAGNI-DRY-3-Principles-to-simplify-your-life)  
