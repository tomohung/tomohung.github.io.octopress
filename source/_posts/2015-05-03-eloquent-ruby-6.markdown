---
layout: post
title: "Eloquent Ruby -6"
date: 2015-05-03 22:06:44 +0800
comments: true
categories: [ruby, book]
---

#Chapter 6 - Use Symbols to Stand for Something

String和Symbol很類似，都可以用來代表程式中的某個事物，但字串還多了可以被用來處理得到額外資訊，如果我們單純只是想要用來代表程式中的某個事物，用Symbol就可以了。

－ 同一個symbol永遠指著同一個物件，如

```
a = :sym 
b= a
c = :sym
```
a, b, c都是相同的物件

但如果是string

```
a = "sym"
b = "sym"
```

`a.equal? b` 會回傳false

- 所有Ruby的物件都有一個`public_methods`的函數，會回傳一個Array of symbols

---------------

簡單總結這章，Symbol因為有著unique, immutable這兩個特性，在用來當作代表性詞時就很實用，尤其是在應用hash時。