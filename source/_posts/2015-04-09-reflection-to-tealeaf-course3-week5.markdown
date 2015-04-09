---
layout: post
title: "Reflection to Tealeaf Course3 Week5 (1/2)"
date: 2015-04-09 14:39:06 +0800
comments: true
categories: [rails, tealeaf]
---

###Feature test for sending email

Following by [previous week](http://www.tomohung.com/blog/2015/04/07/reflection-to-tealeaf-course3-week4/), this week we have a feature test for sending email.

Install gem,

```
$ gem 'capybara-email'
```

And added this line to `spec_helper.rb`

```
require 'capybara/email/rspec'
```

Then

```
require 'spec_helper'

feature 'User resets password' do
  scenario 'user successfully resets the password' do
    clear_emails

    user = Fabricate(:user)
    visit sign_in_path
    click_link 'Forgot Password?'

    fill_in 'Email Address', with: user.email
    click_button 'Send Email'

    open_email(user.email)
    current_email.click_link("Reset Password")

    fill_in 'New Password', with: 'new_password'
    click_button 'Reset Password'

    fill_in 'Email Address', with: user.email
    fill_in 'Password', with: 'new_password'
    click_button 'Sign in'
    expect(page).to have_content("Welcome, #{user.full_name}")
  end
end
```

Especially notice for `open_email`, `current_email` functions are offered by `capybara-email` gem.

###Invite Friend and Registeration

We also have a work on inviting friends through email, the key point here is how to use a secure `token` to tracking data.

- create an invitations data, including inviter, recipient email, name, and `token`.
- when user get the email, following the link provided to server, page will direct to the register page with existing data stored with this token.

  And set the routes for path

```
get 'register/:token', to: 'users#new_with_invitation_token'
```

- we can have a hidden data in the register form for new#users to use.

```
= hidden_field_tag :token, @invitation_token
```

we don't use form_for element, becuase this hidden attribute does not belong to backend modeled form.

###Concerns

To Dry our code, we can use `concern` with `module`. Here is a [post by DHH](https://signalvnoise.com/posts/3372-put-chubby-models-on-a-diet-with-concerns) why we should do this.

We can merge the same function feature into concern file. For example: `Tokenable`

```
#/lib/tokenable.rb
module Tokenable
  extend ActiveSupport::Concern
  included do
    before_create :generate_token
  end

  private
  def generate_token
    self.token = SecureRandom.urlsafe_base64
  end
end
```

Then add to model file

```
require 'tokenable'

class User < ActiveRecord::Base
  include Tokenable
  ...
end
```

I prefer to specify require file name, or we can use [auto loading library files](http://stackoverflow.com/questions/19098663/auto-loading-lib-files-in-rails-4).

###Email Service Providers

Since we have test email sending on ENV:development and EVN:test, now we have test it in the real world. That's why we need a real email service provider. Google has it but with many restriction, Tealeaf recommend `Mailgun` or `Postmark`. Here we have a taste for `Mailgun'.

To setting `config/environments/production.rb`, add these lines:

```
config.action_mailer.default_url_options = { :host => "tomo-myflix.herokuapp.com" }
  config.action_mailer.smtp_settings = {
    :port           => ENV['MAILGUN_SMTP_PORT'],
    :address        => ENV['MAILGUN_SMTP_SERVER'],
    :user_name      => ENV['MAILGUN_SMTP_LOGIN'],
    :password       => ENV['MAILGUN_SMTP_PASSWORD'],
    :domain         => 'tomo-myflix.herokuapp.com',
    :authentication => :plain,
  }
  config.action_mailer.delivery_method = :smtp
```

`tomo-myflix.herokuapp.com` is my app name on Heroku. And ENV about Mailgun are maintained by Heroku.

I aslo can use this command to check `Heroku configurations`.

```
$ heroku config
```

Then push it to heroku and test for a real email address, it works!!

###Background Jobs

Sometimes, some actions do not need to be synchrous, like sending email, we don't need to wait email sending action completed. That's why we need to use a background job to do sending email to speed up the flow.

`Sidekiq` and `Resque` are some of this concept product. And Tealeaf recommend to use [Sidekiq](https://github.com/mperham/sidekiq/wiki).

Install gem Sidekiq

```
gem 'sidekiq'
```

Only a tiny change for using it by delay method

```
AppMailer.delay.notify_on_new_todo(current_user, @todo) # new 
AppMailer.notify_on_new_todo(current_user, @todo).deliver #older one

```

Add this line in `spec/spec_helper.rb`

```
require 'sidekiq/testing/inline'
```

Here we setup sidekiq, but we still need start `Redis` server:

```
$ brew install redis
$ redis-server /usr/local/etc/redis.conf
```

And this start sidekiq with another terminal:

```
$ bundle exec sidekiq
```

So far, this makes `local` works fine with `sidekiq` for background jobs. If we want to use background jobs in ENV:production, Heroku take charges if dyno is more than 1, or try [this way to simulate](https://coderwall.com/p/fprnhg/free-background-jobs-on-heroku)

I have not implemented this yet, but I'll try it later.

There is also [an article talking about Procfile and Foreman](https://devcenter.heroku.com/articles/procfile#deploying-to-heroku).

If sidekiq version >= 3.0, you might also run this line
```
heroku config:set REDIS_PROVIDER=REDISTOGO_URL
```

###Monitor Production Error

Here we have a try for [Sentry](https://getsentry.com/welcome/).

Tracking errors is also a key for automation deployment.
