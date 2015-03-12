---
layout: post
title: "Reflection to Tealeaf Course3 Week2"
date: 2015-03-11 11:21:17 +0800
comments: true
categories: [rails, tealeaf]
---

There are 3 kinds of test: `Unit`, `Functional`, `Integration`. In [week 1](http://www.tomohung.com/blog/2015/03/06/reflection-to-tealeaf-course3-week1/), we test **Model**, this should belong to `Unit test` . And this week we test **Controller**, this should belong to `Functional test`. I guess it should be `Integration test` when we move into part of **View** test.

I have to heavily rely on solution videos, there are two main reasons:

1. I'm not familiar with `what to test`, because I'm a starter for TDD. It's a big conversion **from test laster to test driven**.

2. I'm not familiar with `RSpec`. There are some tricks for test controllers when using RSpec.

For example, when we test `QueueItemsController#create', there are 7 tests:

- it 'redirects to my queue page'
- it 'creates a queue item'
- it 'creates the queue item associated with video'
- it 'creates the queue item associated with current user'
- it 'puts the video as the last one in the queue'
- it 'does not add the video in the queue if already existed in the queue'
- it 'redirects to sign in page if unauthenticated'

A simple action needs to do such lots of tests!!

Or here is for **Model** methods: `search_by_title`:

- it 'returns empty array if no match'
- it 'returns an array if one video'
- it 'returns an array if partial match'
- it 'returns an array of all matches ordered by DESC'
- it 'returns an empty array for search an empty string'

To test every possible situations is not an easy work.

Brandon also metioned me that care about using `let` and `before`. Because of lots of tests in spec files, we may miss some pre-declare vars by using `let` or `before`. Sometimes, it may be better do not clean duplicated codes for code readability.

Second, extract methods into **Model**. ex:

```ruby user.rb
  def queue_include?(video)
    queue_items.map(&:video).include?(video)
  end
```

then, I can use this method everywhere in my app.

###Conclusion

Still on the way to be more familiar to do TDD. :-)