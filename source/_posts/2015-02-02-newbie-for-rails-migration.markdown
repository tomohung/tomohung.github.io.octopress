---
layout: post
title: "Newbie for Rails Migration"
date: 2015-02-02 21:46:35 +0800
comments: true
categories: [rails, tealeaf]
---

As a newbie for Rails, It's normal to do something stupid.

## Modified migration files after `rake db:migrate`

It's useless!! Be carful that every migration filename including a timestamp, after running `rake db:migrate` will re-generate schema.rb. Data in schema.rb will show that the lastest timestamp, if there is no any new migration files newer than this timestamp, `rake db:migrate` will do nothing. Yes! it's nothing!!

``` text normally you should see
==  CreateArticles: migrating ==================================================
-- create_table(:articles)
   -> 0.0019s
==  CreateArticles: migrated (0.0020s) =========================================
```

``` text schema.rb has been updated, rake db:migrate do nothing
$ rake db:migrate
$ rake db:migrate
```
<!--more-->

That's why I got stuck on it for a long time, because I make such a stupid mistake.

So, how about using `rake db:drop` or `rake db:rollback`. This is also a not smart choice, because you should consider that there may be some other co-workers are working on the same project. If they also run their migration on their computer, then you modified yourself migration files, or do some database operation like `rake db:rollback`. These actions will lead to some data error for database.

## Create another new migration file for modified database

Using `rake generate migration do_some_change` for chaning database layer. Using another file to show the modified history is much clear. Avoid ambiguous and mess up all update contect in migration files is super important.

```text Add column for posts table
class AddColumnToPosts < ActiveRecord::Migration
  def change
    add_column :posts, :user_id, :interger
  end
end

```


## Only use `rails generate migration`

Don't use `rails generate` for models or controllers etc. It contains a lot of rails magic. Although most of guide tour (including official guide) use this way. But it's easy to misunderstanding what is wrong for operating database layer and ActiveRecord layer. Take care of this!