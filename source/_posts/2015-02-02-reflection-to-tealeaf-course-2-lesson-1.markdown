---
layout: post
title: "Reflection to Tealeaf Course 2 Lesson 1"
date: 2015-02-02 08:05:05 +0000
comments: true
categories: [rails, tealeaf]
---

Finally, Rails is coming.

Before join Tealeaf, I've tried few times to follow some rails guide books. I just can type some magic codes to build simplest web app but didn't know why at all, because I even didn't know what ruby is.

This time, passing through course 1, I knew what the ruby is, then following [official tour guide](http://guides.rubyonrails.org/getting_started.html), it's much clear than other books I've seen. But I still got stuck on creating many-to-many relationship, because I made a stupid mistake here: *I modified migration file after running `rake db:migrate`*.

Then I dived into solution videos, and Chirs said that:

> Don't use `rails generate` except `migration`

I do love this way, because:

1. I never saw other's show this way to build migration, they always use `rails generate model` to create model and migration. And the problem is, I thought this is the only way to create models and migrations!! And I just thought this is Rails magic, I don't need to think too much. 

2. When I got stuck because I didn't know I can't modified migration files after running `rake db:migrate`. Make sure migration setting is all done before running `rake db:migrate` is very important. And this is just for ActiveRecord layer, database is another layer. It's much easier to understand how could be a disaster to modified/delete migration files working with database layer.

I make [a post to record my big step for Rails](http://www.tomohung.com/blog/2015/02/02/newbie-for-rails-migration/).

I also try to detect tealeaf discus panel, becuase I think it's better to `show who kudos this post`. Sometimes I just want to give a kudos for people reply my post without saying something more, but it's wierd for others to read this post, because they can't see kudos I already gave the post. Anyway, here is my ERD for discuss panel.

{% img http://i.minus.com/ibfrZjiBfElndg.jpg %}

I'm super satisfied that this week I found my mistake when using migrations, and I think there must be other mistake for controller's and view's. Moving to next lesson!!