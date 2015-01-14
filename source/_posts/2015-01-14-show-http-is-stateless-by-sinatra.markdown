---
layout: post
title: "show HTTP is stateless by Sinatra"
date: 2015-01-14 21:25:15 +0800
comments: true
categories: [sinatra, web, ruby]
---

In tealeaf week 3, introduce to HTTP.

The most important thing is that, HTTP is stateless.

What's the influnce by this property? Showing this by Sinatra.

<!--more-->

first, follow [Sinatra intro][] to run basic function:
[Sinatra intro]: http://www.sinatrarb.com/intro.html

then try this code:

``` ruby main.rb
require 'sinatra'

before do
  @name = 'Guest'
end

get '/' do
  erb :home
end

post '/set_name' do
  @name = 'tealeaf'
  redirect '/'
end

```

and this will show a button, click the button will invoke a post request back to `set_name` in main.rb:

``` erb home.erb
<h4>hi~ <%= @name %> </h4>

<form action="/set_name" method="post">
  <input class="btn" type="submit" name="name">
</form>
```

OK, then what will `@name` show in home.erb after click button??

It shows `Guest`, not `tealeaf`. Why?

{% img http://i.minus.com/i9oeLWDYI4DBb.png %}

The key point is: when 2 redirect to 3, it's a new request, instance variable like @name will be deleted after redirect, then create a new one.

That's why @name still shows `Guest`.

How about this way:

``` ruby main.rb
require 'sinatra'

before do
  @name = 'Guest'
end

get '/' do
  erb :home
end

post '/set_name' do
  @name = 'tealeaf'
  erb :home
end

```

Now using erb to embeded `home.erb` into the same request('/set_name'). Then you'll see `@name` shows `tealeaf`!!

This experience shows me how HTTP is stateless. 

And how web developers make it like stateness is a long way to go.