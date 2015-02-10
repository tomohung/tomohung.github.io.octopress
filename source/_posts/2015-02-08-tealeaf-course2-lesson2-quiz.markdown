---
layout: post
title: "Tealeaf Course2 Lesson2 Quiz"
date: 2015-02-08 14:13:48 +0800
comments: true
categories: [rails, tealeaf]
---


> ####1. Name all the 7 (or 8) routes exposed by the resources keyword in the routes.rb file. Also name the 4 named routes, and how the request is routed to the controller/action.

  For example by table `posts`:

  |Prefix   |Verb   |URI   |Controller#Action   |
  |---------|----------|-----------|-----------|
  |posts   |GET   |/posts   |posts#index   |
  |new_post   |GET   |/posts/new   |post#new   |
  |   |POST   |/posts   |post#create   |
  |post   |GET   |/posts/:id   |post#show   |
  |edit_post   |GET   |/posts/:id   |post#edit   |
  |   |PUT/PATCH   |/posts/:id   |post#update   |
  |   |DELETE   |/posts/:id   |post#destroy   |

<!--more-->

> ####2. What is REST and how does it relate to the resources routes?

  REST is abbreviation for `Representational State Transfer`. It use a VERB and a URI to operate some action for web application. And it's corespond to 4 HTTP VERB: GET, POST, PUT, DELETE with 7 actions: index, show, new, create, edit, update, destroy.

> ####3. What's the major difference between model backed and non-model backed form helpers?

  `Model backed form` from helpers like `form_for`, it can automatically association with objects in table, and it can dynamic generate coresponding element for different actions(ex. edit and new).

  `Non-model backed form` from helpers like `form_tag`, it operate elements without relationship of tables in database.

> ####4. How does form_for know how to build the <form> element?

  form_for based on given object, like this:
```
  <%= form_for @posts do |f| %>
    <%= f.label :title %>
    <%= f.text_field :title %>
    <%= f.submit %>
  <% end %>
```
  There's a hidden attribute in form to record that what's action for this request. Rails regconize this hidden attribute to build <form> element. It will automatically create submit button by what action is called.

> ####5. What's the general pattern we use in the actions that handle submission of model-backed forms (ie, the create and update actions)?

For creating a post: `POST /posts`
```
def create
  @post = Post.new(params.require(:post).permit!)
  if @post.save
    redirect_to posts_path
  else
    render :new
  end
end  
```
  
  For updating a post: `PUT /posts/:id`
```
def update
  @post = Post.new(params.require(:post).permit!)
  if @post.save
    redirect_to post_path(@post)
  else
    render :edit
  end
end  
```

> ####6. How exactly do Rails validations get triggered? Where are the errors saved? How do we show the validation messages on the user interface?

  Rails validations get triggered in Model(ActiveRecord::Base), and errors are saved in Model.
  We can use Model's method to show error messages like this:
```
  instance.errors.full_messages
```
  which instance is Model's instance.

> ####7. What are Rails helpers?

  Rails helpers is a way to implement DRY principle in `View`, it abstract redundant **logic codes** in Views into helpers.
This make code in View more cleaner without too much logic in it.

> ####8. What are Rails partials?

  Rails partials is a way to implement DRY principle in `View`, it abstract redundant **HTML codes** in Views into partial forms.

> ####9. When do we use partials vs helpers?

  Partials is to eliminate repeatedly HTML codes in Views, and helpers is to eliminate repeatedly logic codes in Views.

> ####10. When do we use non-model backed forms?

  Some input or showing requirements do not need to be associated with tables in database.