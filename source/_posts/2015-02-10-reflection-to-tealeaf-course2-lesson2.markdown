---
layout: post
title: "Reflection to Tealeaf Course2 Lesson2"
date: 2015-02-10 08:39:30 +0000
comments: true
categories: [rails, tealeaf]
---

[Last week](http://www.tomohung.com/blog/2015/02/02/reflection-to-tealeaf-course-2-lesson-1/), I know Models and Database side of Rails.

This week Tealeaf reveals the `Controller` and `View` part of MVC, I would like to summarize this lesson into two parts: `Don't Repeat Yourself` and `Conventions over Configuration`.

<!--more-->

###Don't Repeat Yourself

- before_action

  Like Sinatra, before_action works at Controller, help us to remove repeatedly codes, like
  
  ```
    before_action: :set_post, only: [:show, :edit, :update]
  ```

  ```
    def set_post
      @post = Post.find(param[:id])
    end
  ```

  then, method `set_post` works at three actions : show, edit, update by once.

- form partial

  Rails offer `form partial` to help us eliminate repeatedly `HTML codes`, by Rails convention, if there is a file called `/app/views/posts/_post.erb`, I can just write code:


  ```
    <%= render @post %>
  ```

  if @post is a class Post's instance, then Rails will automatically match to the file `_post.erb`, then render this file.

  There is another amazing way to show collection of instances. If I have a collection instance `@posts` by class Post, then I have write
  
  ```
    <%= render @posts %>
  ```
  
  Rails will also automatically match the file `_post.erb` to iterate to render `@posts`. What a clean way to write such codes!

- Helpers for view: fix url and datetime

  We also can put some logic in Views into `/helpers`, like `application_helper.rb`. Put a lot of logic in Views is a dirty way when reviewing, and Rails have helpers to make us a clean way to do this. `Form Helper` is another good implement to help us. I'll metion later.

- validations

  Rails offer a simple to validate data in Model, like this

  ```ruby post.rb
  validates :title, presence: true, length: {minimum: 5}
  ```

###Conventions over Configuration

- CRUD
  For most popular actions on web, there are 4 actions: Create, Retrieve, Update, Delete.
  Rails offer a RESTful setting to make implemention these actions much simple.

  For example, in `routes.rb`:

  ```
    resources :posts
  ```

  help us to build 7 actions corespond with VERBs and URIs.

  ```
  get '/posts/', to: "posts#index", as: "posts"
  get '/posts/:id', to: "posts#show", as: "post"
  get '/posts/new', to: "posts#new", as: "new_post"
  post '/posts/', to: "posts#create",
  get '/posts/:id', to: "posts#edit", as: "edit_post"
  patch '/posts/:id', to: "posts#update"
  delete '/posts/:id', to: "posts#destroy"
  ```

  I also can use key word `except:` or `only:` to assign which routes will be created:
  
  ```
    resources :posts, except: [:destory]
    resources :comments, only: [:create]
  ```

- nested routes
  Rails also offer a way to create nested path like `/posts/:id/comments`
  
  ```
    resources :posts, except: [:destory] do
      resources :comments, only: [:create]
    end

  ```
  
  And this will create route for `comments#create` by nested route
  
  ```
    post '/posts/:post_id/comments', to: "comments#create", as: "post_comments"
  ```

- model backed form
  
  Using `Model backed form` can associated with instance, then Rails do a lot of task behind the form.

  ```
  <%= form_for [@post, @comment] do |f| %>  
    <%= render 'shared/error_messages', instance: @comment %>
    <%= f.label :body, 'Leave a Comment:'%>
    <%= f.text_area :body , rows: 5, class: 'span4'%>
    <br>
    <%= f.submit class: 'btn btn-defualt'%>
  <% end %>
  ```
  
  `[@post, @comment]` is a Rails convention for creating matching routes for `posts/coments`. And this form help us to create HTML element depends on what current action is. There are different actions in **edit** and **new** actions. `label` will update information if it's at edit action, and form action will generate a hidden attribute to store info that current actions VERB. This are all Rails conventions.

- category_ids for checkboxes
  
  Rails offer a helper to create checkboxes, like this

  ```
  <%= form_for @post do |f| %>
    <%= f.collection_check_boxes :category_ids, Category.all, :id, :name do |cb| %>
      <%= cb.label(class: 'checkbox inline') {cb.check_box(class: 'checkbox') + cb.text}%>
    <% end %>
  <% end %>
  ```
  
  And there is a virtual attribute for `category_ids`, it's generated because we set posts and categories's associations in Post Models:

  ```
  class Post < ActiveRecord::Base
    belongs_to :creator, foreign_key: :user_id, class_name: "User"
    has_many :comments
    has_many :post_categories
    has_many :categories, through: :post_categories
  end
  ```
  
  and `category_ids` store multiple values to match `collection_check_boxes`. Rails another magic show.

- auto association by mass-assignment
  
  We can write

  ```
    @comment = @post.comments.new(param.require(:comment).permit!)
  ```
  Rails will auto assign post_id to @comment.

###Others

- Strong parameters
  
  After Rails 4, Rails lock params by default, we need to get params by this way
  
  ```
  params.require(:post).permit(:title, :url, :description, category_ids:[])

  ```
  
  If we have to get a collection values, remember to add `[]`

- render vs redirect_to

  `render` is for render HTML, and `redirect_to` is for make a new request. Although we see the page is changed, but it's totally different behind these two ways.

### Conclusion

  I can't imagine how can I figure out such mass Rails knowledge by myself in two weeks. Here is my learning processes: `watch video -> 'I knew it!' -> do assignment -> 'What? How to do that?' -> watch solution -> 'Ah! I got it!' -> complete assignment`. And these things happen again and again.

  Watching option videos and reviewing disscusions also help me to dig out what I missed. 

  I should do the official guide again, and I think this time will be a totally different feeling compared with two weeks ago.