---
layout: post
title: "Phoenix with Bootstrap"
date: 2016-06-24 21:50:28 +0800
comments: true
categories: [phoenix, elixir, bootstrap]
---

最近終於真正開始用Phoenix來開發，對於一個前端近乎白痴的人來說，起手式當然要來個匯入Bootstrap才行。

原本以為Phoenix內建就有引入Bootstrap，但使用後才發現原來它只匯入了css，javascript和icon都必需另外處理。找了網路上相關的文章好像都弄的有一點複雜，但經過試誤法後其實蠻簡單的，記錄一下。

--------

建立新專案

```
$ mix phoenix.new bootstrap_example
$ mix deps.get
$ mix phoenix.server
```
打開網頁到`localhost:4000`看看結果，很好，預設的首頁出來了。

再來就是測試javascript相關的原件

修改`web/template/page/index.html.eex`

貼上Bootstrap官網的範例,順便把內容加上icon可以一起測試

```
<div>
  <!-- Nav tabs -->
  <ul class="nav nav-tabs" role="tablist">
    <li role="presentation" class="active"><a href="#home" aria-controls="home" role="tab" data-toggle="tab">Home</a></li>
    <li role="presentation"><a href="#profile" aria-controls="profile" role="tab" data-toggle="tab">Profile</a></li>
    <li role="presentation"><a href="#messages" aria-controls="messages" role="tab" data-toggle="tab">Messages</a></li>
  </ul>

  <!-- Tab panes -->
  <div class="tab-content">
    <div role="tabpanel" class="tab-pane active" id="home"><span class="glyphicon glyphicon-home"></span></div>
      <div role="tabpanel" class="tab-pane" id="profile"><span class="glyphicon glyphicon-th-list"></span></div>
      <div role="tabpanel" class="tab-pane" id="messages"><span class="glyphicon glyphicon-file"></span></div>
  </div>

</div>

```

Phoenix會自動幫我們更新內容
更新一下瀏覽器，會看到tab list，但是點了之後下方內容不會更新，因為javascript並沒有匯入。

其實Phoenix已經幫我們鋪好路了，來看看設定檔`brunch.config.js`

```
exports.config = {
  // See http://brunch.io/#documentation for docs.
  files: {
    javascripts: {
      joinTo: "js/app.js",

      //
      // To change the order of concatenation of files, explicitly mention here
      // https://github.com/brunch/brunch/tree/master/docs#concatenation
      //  order: {
      //    before: [
      //     "web/static/vendor/js/jquery-2.1.1.js",
      //     "web/static/vendor/js/bootstrap.min.js"
      //   ]
      // }
    },
........
```

把

```
      order: {
        before: [
           "web/static/vendor/js/jquery-2.1.1.js",
           "web/static/vendor/js/bootstrap.min.js"
        ]
      }
```
解除掉,記得前一行的`joinTo: "js/app.js",`要加上逗號

好，這樣只是設定好要放的路徑，因為bootstrap.min.js相依jquery，所以要下載

既然我們已經用npm來管理套件了，用npm安裝最快囉

```
$ npm install bootstrap
```

jQuery是相依套件npm會一起裝

copy jQuery library

```
$ mkdir -p web/static/vendor/js/
$ cp  node_modules/jquery/dist/jquery.min.js web/static/vendor/js/jquery-2.1.1.js
```

copy Bootstrap library

```
$ cp node_modules/bootstrap/dist/bootstrap.min.js web/static/vendor/js/bootstrap.min.js
```

順便連icon也一起處理

```
$ mkdir web/static/assets/fonts
$ cp node_modules/bootstrap/dist/fonts/* web/static/assets/fonts/
```

大功告成！！！
回去瀏覽器看結果，切換不同的tab都會顯示不同的icon！！

Happy Phoenix!!

Demo: https://github.com/tomohung/phoenix_bootstrap_example
