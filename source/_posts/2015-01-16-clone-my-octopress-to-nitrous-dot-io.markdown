---
layout: post
title: "Clone My Octopress to Nitrous.io"
date: 2015-01-16 21:10:08 +0800
comments: true
categories: [octorpress, nitrous, github]
---

{%img http://i.minus.com/iiGNP2Lnx1fRk.png %}

#Octopress & github
這個部落格是用[Octopress][]在[github][]上建立起來的。但是不像一般的部落格系統，可以只要用瀏覽器登入，就可以開始寫文章，既然知道[nitrous.io][]可以雲端工作，就把腦筋動到它的上面去。

[Octopress]: octopress.rog
[github]: github.com
[nitrous.io]: nitrous.io

#Clone Octopress
作法是參考[這篇文章](http://blog.zerosharp.com/clone-your-octopress-to-blog-from-two-places/)。

但是其中的程式碼有點錯誤，因為github上存放網頁的位置己經改成github.io了。


登入nitrous.io，開啟終端機畫面：
註：不會用可以參考[這個連結](http://www.tomohung.com/blog/2015/01/11/nitroukai-fa-huan-jing/)

輸入
```
$ git clone -b source git@github.com:username/username.github.io.git octopress
```
 
然後clone master branch到_deploy資料夾

```
$ cd octopress
$ git clone git@github.com:username/username.github.io.git _deploy 
```

說明一下，branch **source** 就是存放原始資料，branch **master** 放的是rake後產生的資料，再上傳至github讓它來處理。

接著在nitrous安裝需要的gem
```
$ gem install bundler
$ rbenv rehash    # If you use rbenv, rehash to be able to run the bundle command
$ bundle install
$ rake setup_github_pages
```
輸入相關資料，這邊跟octopress上的安裝說明都是一樣的。
然後所有資料就會從github上完整的clone一份到nitrous上。

# Backup & Sync

每次寫完文章，最重要的是要記得備份。

`rake deploy`就不用擔心，因為它就是把branch master上傳到github上。
```
$ rake generate
$ rake deploy             # update the remote master branch
```

但是branch source就要自己處理：
```
$ git add .
$ git commit -m "Some comment here." 
$ git push origin source  # update the remote source branch 
```
這樣子可以確保github上版本是最新的。

然後如果要在本機做完到nitrous做，或者是nitrous做完回本機，記得pull branch source 和 master。

```
$ cd octopress
$ git pull origin source  # update the local source branch
$ cd ./_deploy
$ git pull origin master  # update the local master branch
```

這樣子就算是在外面用別台電腦，也能夠blog一下了，不錯吧！