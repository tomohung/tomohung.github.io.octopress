---
layout: post
title: "Reflection to Tealeaf Course3 Week4"
date: 2015-04-07 13:11:30 +0800
comments: true
categories: [rails, tealeaf]
---

### Three kinds of BDD

In week 4 beginning, we got a brief intro for 3 kinds of BDD:

- meet in the middle

  I think this should be the popular one for modern development. The front-end engineer build the pages from mockups, then back-end developer connect UI with controllers and models.

- inside out

  It develops from models to controllers till views and integrations. It's hard to decide what models you really need in the beginning.

- outside in

  It devlops from integrations to controllers and models. It start from a big vision, and close to what user's need. But for developer, it's hard to code from this big vision, especially for integration test in the development beginning.

### Self Referential Associations

We can use **self referential association** to let user track other users. For example:

```
# t.integer :follower_id, :leader_id
class Relationship < ActiveRecord::Base
  belongs_to :follower, class_name: 'User'
  belongs_to :leader, class_name: 'User'
end
```

Then, model `User` can have these virtual attributes:

```
  has_many :following_relationships, class_name: 'Relationship', foreign_key: :follower_id
  has_many :leading_relationships, class_name: 'Relationship', foreign_key: :leader_id
```

### Sending Emails

Rails also offer `ActionMailer` for sending emails.

- create files in `app/mailers/app_mailer.rb`

```
class AppMailer < ActionMailer::Base

  def send_welcome_email(user)
    @user = user
    mail to: user.email, from: 'info@myflix.com', subject: 'Welcome to Myflix'
  end
end
```

- `View` template in `app/views/app_mailers/send_welcome_email`:

```
%html(lang='en-US')
  %body
    %p Welcome to MyFlix, #{@user.full_name}
```

- Config for `config/environments/development.rb`

```
config.action_mailer.delivery_method = :letter_opener
```

And 

```
group :development do
  gem 'letter-opener'
end
```

> This is only for `development` setting, we also need to set for `test` and `production` independent

### Handling Sensitive Account Info

It's dangerous to upload config setting file to github or other public storage, there is someone update AWS key to GitHub and got a unbelievable credit bill.

Use `ENV` to protect secret data, we can use gem `Figaro`.
[Figara](https://github.com/laserlemon/figaro) is the simplest way to keep our key value safe.

There are two posts talking about this topic:

1. [Rails Environment Variables](http://railsapps.github.io/rails-environment-variables.html)
2. [Configuration and config Vars](https://devcenter.heroku.com/articles/config-vars)

> Update: Tealeaf also have a post about [Managing Environment Configuration Variables in Rails](http://www.gotealeaf.com/blog/managing-environment-configuration-variables-in-rails)

### Test Email sending

Remember to clear email deliveries before test:

```
before { ActionMailer::Base.deliveries.clear }

```

Then we can validate email in the test by this

```
post :create
expect(ActionMailer::Base.deliveries.last.to).to eq([user_attributes["email"]])
# expect(ActionMailer::Base.deliveries.last.body).to include(user_attributes["full_name"])
```

### Update database, use rake tasks

An advice from TA Tomtomecek, I update existing data in model User, and he suggest me to use [custom rake tasks](http://guides.rubyonrails.org/command_line.html#custom-rake-tasks) to do that. I've not taste that yet, just leave a memo here to remember.