---
layout: post
title: "Eloquent Ruby -1 [Write Code That Looks Like Ruby]"
date: 2015-04-24 14:15:45 +0800
comments: true
categories: [Eloquent Ruby, book, ruby]
---

#Eloquent Ruby

`Eloquent Ruby` by Russ Olsen 是一本在Tealeaf推薦的書，但它是所謂的『第二本Ruby的書』，而不是第一本，因為這本書裡面已經假設你對Ruby有基礎的觀念了。如果真的完全是初學者，Tealeaf推薦[Learn to Program by Chris Pine](https://pine.fm/LearnToProgram/)，或者是[Learn Ruby The Hard Way](http://learnrubythehardway.org/book/)，我覺得也不錯，還可以一邊練習打字。

目前我把Part I：Basics看完了，共九章，覺得自己有學到些東西，所以就把它給紀錄下來，並參雜一些個人的想法。

#Charpter 1 - Write Code That Looks Like Ruby

其實這章指的就是Code Style。更多的Ruby Code Style可以參考[這裡](https://github.com/bbatsov/ruby-style-guide)。

- Indent code with two spaces，以前在寫C++時就一直覺得縮排其實2個空白就夠了（雖然我還是用預設4個），轉成Ruby後倒是蠻習慣的，尤其是不用再寫分號跟括號。

- 什麼時候寫註解，告訴人家要怎麼用這個method，最好還帶範例。其它的像是最好用良好的命名去定義名稱，省去閱讀時的猜測，documenting itself.

- 如果是寫const value，最好用Uppercase with underline，像UPPERCASE_WITH_UNDERLINE。

- 因為括號是可以省略的，所以⋯⋯我常被搞混=___=，書用建議def method時後面帶參數時要用括號，但如果是一些接近口語的句子時可以考慮拿掉，如

```
puts 'hello world'。
```

- 如果code block只有一行時最好縮成一排，反過來說如果是多行的話就用block

```
apples.each { |apple| apple.to_eat } # prefer if only single code statement

apples.each do |apple|
  apple.to_eat
end
```

- 如果method name結尾帶一個問號?，通常表示這個方法回傳true or false，如`array.nil?`；又如果是驚嘆號!，通常表示可能有點危險（所謂的危險通常是把丟進來的參數被改變mutate了，如`array.map!`。但是不管是問號還是驚嘆號，都只是Ruby習慣的命名而已，不是強制性。

- 有慣例就會有例外，像是

```
 pi = Float('3.14159')
```

Float開頭是大寫而不是小寫。為什麼我也不知道@_@