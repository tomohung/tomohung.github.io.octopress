---
layout: post
title: "Reflection to Tealeaf Course2 Lesson4"
date: 2015-02-22 21:22:10 +0800
comments: true
categories: [rails, tealeaf]
---

In Course2(Rapid Prototyping with Ruby on Rails), [first week][] I learned about `Models and database`, [second week][] I learned about `Controllers and Views`. The [third week] I learned to add `Authentication` and `Polymorphic table`.

And this week, also the last week, I completed all basic functions I need to know. Including Ajax for voting, extract methods into gem, build and publish gem, display time info considering user's timezone.

[first week]: http://www.tomohung.com/blog/2015/02/02/reflection-to-tealeaf-course-2-lesson-1/
[second week]: http://www.tomohung.com/blog/2015/02/10/reflection-to-tealeaf-course2-lesson2/
[third week]: http://www.tomohung.com/blog/2015/02/16/reflection-to-tealeaf-course2-lesson3/

<!--more-->

###Ajax for voting

Ajax is a clean way to change View template that is necessary changed, and we don't need to redirect and render full page. Rails offer a way to achieve this.

- in Controller

```ruby
respond_to do |format|
  format.html do
    redirect_to :back
  end
  format.js
end
```

- in View

`format.js` will lead to the class#action View file, for example: `/votes/create.js.erb`

```ruby
$("#voteable_<%= @vote.voteable_type %>_<%= @vote.voteable_id %>").
  html("<%= j render 'shared/show_dislike_link', vote: @vote %>");
```

notice that in `js.erb` file, render should specify format by using `j render`.

- HTML ID

ID is the key to do Ajax.

```ruby
<span id = 'vote_<%=vote.id%>'>
  <%= link_to vote_path(vote), remote: true, method: :delete do %>
    <i class='icon-thumbs-up'></i>
    Cancel
  <% end %>
</span>
```

Add `id` for Ajax, and add `remote: true` for activate Ajax.


###Extract methods

Using `ActiveSupport::Concern` to manage module, info from Rails API:

```ruby
module M
  extend ActiveSupport::Concern

  included do
    scope :disabled, -> { where(disabled: true) }
  end

  class_methods do
    ...
  end
end
```

When I using for vote methods:

```ruby
module Voteable
  extend ActiveSupport::Concern
  included do
    has_many :votes, as: :voteable
  end

  def like_votes
    self.votes
  end
end
```

Or Sluggable.rb

```ruby
module Sluggable
  extend ActiveSupport::Concern

  included do
    before_save :generate_slug
    class_attribute :slug_column
  end

  def to_param
    self.slug
  end
  
  def generate_slug
    # ignore...
    # self.send(self.class.slug_column)
  end

  module ClassMethods
    def sluggable_column(noun)
      self.slug_column = noun
    end
  end

end
```

Notice that using `class_attribute` to connect different class symbol. Setting `sluggable_column` in `Models`.

Also notice that `slug_column` is a class methods and return a symbol, in order to get instance's value, use `self.send(self.class.slug_column)`.

###Slug

There are two purpose using slug:

- Don't expose database information.
- URL for SEO friendly

The concepts to generate slug are:

1. add a slug column to store slug value
2. substitute key word like `name` into slug, avoid some keywords for HTTP like `@&``'` etc.
3. check slug exsited or not, otherwise add a number for avoiding the same name.

###Using Gems

Gem is a very convenient way to use the same methods between two different projects.

1. build a gem spec file.
2. copy exsited module codes into gem specify path.
3. build the gem
4. publish the gem
5. add gem to Gemfile, then `bundle install`.

###Time Zone

Rails offer time zone methods to do this.

- in model backed form

```ruby
f.time_zone_select :time_zone, ActiveSupport::TimeZone.us_zones, default: Time.zone.name
```

- in View template

changing time zone by (if time zone is 'Arizona')

```ruby
datetime = post.created_at.in_time_zone("Arizona")
```

###Conclusion

Using gem is a way for Rails how to DRY our codes. Rails flavored Ajax is also antoher way to do that. Exciting to know these. 

Finally, the last week is coming to the end. The final project `Postit` seems become into reality app we daily use. And rails pieces are making together and show me how to build a basic function app.

After these two course, I think now I have confidence to read through other Rails introducion books I used to not understand.About Course 3, there is no reason I do NOT keep going since I decide to make a career change. Glad that I survived through the first two courses, and I think I'm ready for the course 3 challenge.