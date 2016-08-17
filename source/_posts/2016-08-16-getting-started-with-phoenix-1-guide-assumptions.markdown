---
layout: post
title: "Getting started with Phoenix - 1. Guide Assumptions"
date: 2016-08-16 21:16:55 +0800
comments: true
categories: [elixir, phoenix, getting_started_with_phoenix]
---

這份說明是參考Rails官網的Getting Started with Rails，以相同的架構和實作內容，來介紹Phoenix這個框架。如果你本身己經有了Rails的基礎，那麼你應該能跟著這個說明一起實作，體驗到Phoenix/Elixir與Rails/Ruby一些不同或相同的地方，希望你能在過程中得到些樂趣。

你需要先在電腦安裝以下元件:

- Elixir 1.3 或者以上（本文寫作時最新版本）
- Erlang
- Phoenix 1.2 或者以上（本文寫作時最新版本）
- node.js
- PostgreSQL（官方預設資料庫）, 但後面的範例我們還是使用Rails開發者比較熟悉的MySQL

安裝方法可以參考[Phoenix官網](http://www.phoenixframework.org/docs/installation)

Phoenix是基於Elixir的網頁應用開發框架，所以對於Elixir有基本的認識會比較容易上手，我個人的經驗是官網的說明看完即可，OTP部份可以先暫時跳過（雖然這應該是Elixir/Erlang的菁華）。

[Elixir官網介紹](http://elixir-lang.org/getting-started/introduction.html)

這一系列的文章除了架構上引用Rails官網的Getting started with Rails之外，實作的內容同時也參考了

- [Phoenix Official website](http://www.phoenixframework.org/)
- [Programming Phoenix](https://pragprog.com/book/phoenix/programming-phoenix)

如果真的要進行Phoenix的開發，Programming Phoenix這本書應該可以縮短很多學習時間，我個人非常推薦。
