---
layout: post
title: "Getting started with Phoenix - 3. Create a new Phoenix project"
date: 2016-08-17 20:59:48 +0800
comments: true
categories: [elixir, phoenix, getting_started_with_phoenix]
---

## Create a new Phoenix project

### Install Phoenix

可以參考[Phoenix官網安裝方法](http://www.phoenixframework.org/docs/installation)
另外我們要使用MySQL，所以也要記得安裝，網路上教學文章很多，這裡就跳過

### Create the Blog application

打開終端機執行以下程式

```
$ mix phoenix.new blog --datebase mysql
```

`--database mysql`是為了將預設資料庫PostgreSQL指定成MySQL。
而blog是我們專案的名稱

接下來會提示是否安裝相依套件
Fetch and Install dependencies? [Yn]

選擇Y之後，程式應該會自動執行這兩行

```
$ mix deps.get
$ npm install
```

如果npm install沒安裝的話，前端套件會沒有，你看到的就是一個醜醜的網站，因為Phoenix已經預設幫你用了Bootstrap的css檔案，和一點點客製化的內容。

想要有完整的Bootstrap功能，包含Javascript，可以參考我另一篇文章[Phoenix with Bootstrap]()

接下來進入專案並且執行server

```
$ cd blog
$ mix phoenix.server
```

Server運行起來後，打開瀏覽器輸入`localhost:4000`，你應該可以看到Phoenix的觀迎首頁

{% img https://files.readme.io/dgQq2PC8SiyitRnHtf7A_welcome-to-phoenix.png %}

另外雖然還沒用到，但可以先建立資料庫

```
$ mix ecto.create
```
如果出現錯誤的話，很有可能是帳號密碼錯了，可以到`config/dev.exs`修改

```
# Configure your database
config :blog, Blog.Repo,
  adapter: Ecto.Adapters.MySQL,
  username: "root",
  password: "",
  database: "blog_dev",
  hostname: "localhost",
  pool_size: 10
```

