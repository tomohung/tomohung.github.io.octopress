---
layout: post
title: "Getting started with Phoenix - 4. Phoenix File/Folder"
date: 2016-08-17 21:29:15 +0800
comments: true
categories: [elixir, phoenix, getting_started_with_phoenix]
---

在上一篇文章順利的看到首頁了，現在先等等，看一下剛剛我們利用`mix phoenix.new`所建立出來的資料夾結構

|File/Folder   | Purpose  |
|---|---|
| config/  | 就是設定檔，包含config.exs和各個環境變數要用的設定檔，如dev.exs就是預設開發用的設定檔  |
| deps/  | 利用mix安裝的套件都會放在這  |
| lib/  | routes檔案也是放在這裡。如果修改config/或是lib/下的檔案，server都必需重新啟動，貼心的是Phoenix會提示你  |
| test/  | 好測試，不寫嗎  |
| web/  | 類似Rails中的app/資料夾，內容都是放在這裡  |
| web/channels  | Phoenix的招牌WebSocket實作，不過目前我們還用不到它  |
| web/controllers  | 所有流程的中心點，MVC中的C  |
| web/models  | model是單純的資料結構，可以和Ecto合併使用|
| web/static  | 存放assets  |
| web/templates  | html內容存放，這邊使用的是html.eex格式，也就是embed elixir format，可以像erb一樣把elixir變數代進去使用  |
| web/views  | 在Phoenix的定義下，view是module，而template只是module下的function。我自己是把它理解成在view module下可以放顯示頁面邏輯的方法，類似Rails中的Helper |
| brunch-config.js  | Phoenix預設使用Brunch做為前端管理套件  |
| mix.exs  | 就像是Rails中的Gemfile  |
| mix.lock  | 就像是Rails中的Gemfile.lock  |
| package.json  | npm管理套件設定檔  |

