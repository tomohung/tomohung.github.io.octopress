---
layout: post
title: "Reflection to Tealeaf Course2 Lesson2"
date: 2015-02-10 08:39:30 +0000
comments: true
categories: [rails, tealeaf]
published: false
---

[Last week](http://www.tomohung.com/blog/2015/02/02/reflection-to-tealeaf-course-2-lesson-1/), I know Models and Database side of MVC.

This lesson Tealeaf reveals the `Controller` and `View` part of MVC, I would like to summarize this lessons into two parts: `Don't Repeat Yourself` and `Conventions over Configuration`.

###Don't Repeat Yourself

- before_action

- form partial

- Helpers for view: fix url and datetime

###Conventions over Configuration

- CRUD
  Create, Retrieve, Update, Delete.

- routes for 7 actions: index, show, new, create, edit, update, destroy
- manual way to create routes; get '/posts', to 'posts#index', as: 'posts'

- nested routes

- model backed form

- category_ids for checkboxes

###Others

- Strong parameters

- render vs redirect_to

- validations