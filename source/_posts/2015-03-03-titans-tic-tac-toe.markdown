---
layout: post
title: "Titan's Tic Tac Toe"
date: 2015-03-03 09:11:10 +0800
comments: true
categories: [tealeaf, rails]
---

{% img http://i.minus.com/i2XJ0y9E48CHg.png %}

###Prelude

This is a blog entry for record my first personal project for Rails, and focus on what's are first time if starting a new project `From Zero To One`.

`Titan's Tic Tac Toe` is my first personal app for Rails.

Website: https://young-castle-5534.herokuapp.com/

GitHub repo: https://github.com/tomohung/tictactoe-rails

This project is activated by Tealeaf Course 2(Rapid Prototyping with Ruby on Rails), we are asked to do a personal project by ourself. In course 1(Introduction to Ruby and Web Development), we have an optional assignment to build a game `Tic Tac Toe` engine by Object-Oriented, that is, to give this game AI. I spent a lot of time to design logic, and I believe I have a good job for this. This is why I choose this subject for my personal project. And reuse my code is also a kind of `DRY` way.

<!--more-->

**Warning**

I have to admit that I make a big mistake, because I am using a class variable to control **Game Board** status. This work when only me to play with it, but after deployment it will become disaster when multiple player play together. Player share the same game status and sometimes it look weird.

I should use sessions or database to store game status, and extract methods from class GameBoard to controller. I won't fix this problem but writing this blog entry to record this.

**Do no use `class vars` and `global vars` in Rails.**

###Concepts

Before implementation, I need to split works into pieces.

1. Game enegine.
2. Game board implementation.
3. Ladder for record user's game result.
4. User's register system and Login/out.

### Update Environment

RVM and rbenv are two popular Ruby version manager. I choose RVM, but rbenv is also a good choice.
It makes me easy to switch different versions Ruby between different projects.
I use `Ruby version 2.2.0`, and `Rails 4.2.0` for this new project.

Then create a new Rails project
```
$ rails new project_name
```

Specify version in GemFile

```
gem 'rails', '4.2.0'
ruby "2.2.0"

gem 'bootstrap-sass', '~> 3.3.3'
```

###Game Engine

The game engine is already done in Course 1, so I just modified some codes to fit web process.
GitHub repo: https://github.com/tomohung/tictactoe-rails/blob/master/lib/oo_tic_tac_toe.rb

###Game Board

  I'm a newbie for HTML & CSS, so Bootstrap will be my best choice. It offer lots of template with high quality, and mobile first.

  I choose [template-cover](http://getbootstrap.com/examples/cover/) for my project, it looks clean and suit for my application.

#### Bootstrap

  1. Download Bootstrap, unzip it, then find source code in `/docs/examples/cover`.
  2. I got two files, `index.html` and `cover.css`.
  3. combine `index.html` into Rails `views/layout/application.html.erb`.

  It may looks like this:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Tic Tac Toe</title>
  <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track' => true %>
  <%= javascript_include_tag 'application', 'data-turbolinks-track' => true %>
  <%= csrf_meta_tags %>
</head>
<body>
  <div class="site-wrapper">
    <div class="site-wrapper-inner">
      <div class="cover-container">
        <%= render 'layouts/navigator' %>
        <%= render 'layouts/messages' %>
        <%= yield %>
        <%= render 'layouts/footer' %>
      </div>
    </div>
  </div>
</body>
</html>
```

  I use partial `layouts/navigator` and `layouts/footer` to clean the code.

#### CSS for cover.css

  The page looks not good, because CSS is not included. Copy `cover.css` to `/assets/stylesheets`. Rails will automatically include CSS files in this directory.

  So far is good, but something wrong when I want to use other bootstrap components. 
  After google that, this is because I haven't import bootstrap!! I'm using just cover.css for this template.

  Changing file name `assets/applications.css` to `asstes/application.scss`, and add this code
  
```
  @import "bootstrap-sprockets";
  @import "bootstrap";
```

  `bootstrap-sprockets` has to be imported otherwise **icon** can't be displayed.

  Well, bootstrap components works, but ruin all my template.

  After trial and error, I merge `cover.css` code into `application.scss`, and put behind `@import "bootstrap"`. Rails may load other css files in `assets` directory, then application.scss import bootstrap, overwrite those class attributes.

  After these setting, now my cover template works well with Bootstrap. I aslo test on my iPhone, it shows pretty good, Bootstrap is awesome!!

#### CSS for GameBoard

  The hardest part for me is to build a game board. I search from web and get some solution, but there's no better solution to draw a symbol `X`, I decide to display it by another style circle. One more thing, I add the feature when mouse `hover` on choosen square, it will render a temparary fake circle. This makes game much friendly. Here is the css file:

```css
.doughnut { 
  display: inline-block;
  border: 10px solid #f00;
  border-radius: 50px;
  height:60px;
  width:60px;
}

.host_doughnut { 
  display: inline-block;
  border: 5px solid #fff;
  border-radius: 20px;
  height:60px;
  width:60px;
}

.unpicked {
  display: inline-block;
  height:60px;
  width:60px;
  border: 2px dashed #444;
}

.unpicked:hover {
  border: 10px solid #f66;
  border-radius: 50px;
  height:60px;
  width:60px;
}
```

####Ajax

  Everytime player choose the square, the whole page will reload again. This is not good for a modern web app. Using Ajax to improve it.

  The keyword is add a ID for picked squre.

```
  <div id = 'pick_<%= number %>'>
```

  in `GameController`, we use `respond_to` to trigger js:

```ruby
  respond_to do |format|
    format.html do
      redirect_to game_path
    end
    format.js do          
      host_pick
      winner = @board.game_is_over?
      if winner
        flash[:notice] = 'OH NO!! You are DEAD!' if winner == @host
        render :js => "window.location = '/game/over'"
      end
    end
  end
```

  > Note: I use `render :js => "window.location = '/game/over'"` to render page /game/over if game is over.

  and `game.create.js` file looks like:

```ruby
  <% (1..9).each do |number| %>
    <% if @player_picked_numbers.include? number %>
      $('#pick_<%= number %>').html("<div class='doughnut'></div>");
    <% elsif @host_picked_numbers.include? number %>
      $('#pick_<%= number %>').html("<div class='host_doughnut'></div>")
    <% end %>
  <% end %>
```

  Ajax make this game vivid!!

###User's Register

Since I need to store game record, I have to add User's Register, this is not a difficult stuff if you had gone through course 2. Here is a simple ERD:

- User: username, digest_password, timestamps, has_many :game_records
- GameRecord: user_id, game_result, attack_times

User and GameRecord is a one-to-many assocaition.

Create routes, model, controller, and views follow what we learned in course 2.

###Login/out 

  Create a SessionController to manager user login/logout.
  Use session[:user_id] to store data in cookie.

  - def `new` to render login page.
  - def `create` for login.
  - def `destroy` for logout.

  > Don't forget to add feature: auto login after user register.

###Ladder

I will show three kinds of game result: Keeper, Winner, Loser.
And I need tab navigation here, still copy from Bootstrap:

```
<ul class="nav nav-tabs">
    <% params[:tab] = 'WIN' if !params[:tab] %>
    <li class="<%= 'active' if params[:tab] == 'TIE' %>"><a href="#TIE" data-toggle="tab">Keepers</a></li>
    <li class="<%= 'active' if params[:tab] == 'WIN' %>"><a href="#WIN" data-toggle="tab">Heroes</a></li>
    <li class="<%= 'active' if params[:tab] == 'LOSE' %>"><a href="#LOSE" data-toggle="tab">Loser</a></li>
</ul>

<div class="tab-content">
  <%= render 'column_title' %>  
  <div class="<%= if (params[:tab] == 'TIE' || !params[:tab]) then 'tab-pane active'  else 'tab-pane' end%>" id="TIE">
    <%= render @game_records.where(status: 'TIE') %>
  </div>
  <div class="<%= if params[:tab] == 'WIN' then 'tab-pane active'  else 'tab-pane' end%>" id="WIN">
    <%= render @game_records.where(status: 'WIN') %>
  </div>
  <div class="<%= if params[:tab] == 'LOSE' then 'tab-pane active'  else 'tab-pane' end%>" id="LOSE">
    <%= render @game_records.where(status: 'LOSE') %>
  </div>
</div>
```

Tabs do not work? This is because I haven't include javascript for Bootstrap. 

Add this line in `/assets/javascripts/application.js`

```
//= require bootstrap-sprockets
```

Well, it works now!!

Then, I found Kevin(Tealeaf Instructor) also write a blog for [integrating rails and bootstrap](http://www.gotealeaf.com/blog/integrating-rails-and-bootstrap-part-1). 

###Deploy to Heroku

Finally, functions all work and ready to deploy to [Heroku](heroku.com). And follow by Heroku instruction, push code, then...`Fail`.

Check the log and find something wrong about `gem pg`. pg is `Postgres` used by Heroku. Rails default setting is using `sqlite3`, so I need to do some work in Gemfile:

```
group :production do
  gem 'pg'
  gem 'rails_12factor'
end
```

Add this code to Gemfile for config :production using Postgres. Run `bundle install` and git push again, it should work!!


###Conclusion

In Course 2, we have lots assignments from a prepared template. If I don't start a new project, I will never knew there are lots of minor things I should know. After this project, I have chance to be more familiar with Ruby language, Rails routes, CRUD...etc.

###Compliment from others

Record these just for encourage myself to keep going on.

Albert Agram (TA):
> Hey Tomo, nicely done! I played a few rounds and am impressed. Good job :)
<br/>

Chris Lee (Tealeaf Instructor):
> This is incredibly awesome! I'll let out a secret: we have a web-based 
tic tac toe game in the works too, and I think yours may be more fun to 
play :\
<br/>

> Great job!
<br/>

> This type of app doesn't lend itself well to Rails, because it's not 
really "page oriented". Rails tends to be a better fit for apps like 
social networks, or things like that where you have CRUD actions on some 
resources.
<br/>


Brandon Conway (TA):
> This game is awesome! I really like the ladder implementation. 
<br/>


Eugene Chang:
> This is a really nice app! After seeing it, I had to constantly resist the temptation of spending more time trying to make my UI look better. Great job! 
