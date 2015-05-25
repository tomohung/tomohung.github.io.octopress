---
layout: post
title: "Eloquent Ruby -8 [Embrance Dynamic Typing]"
date: 2015-05-19 08:56:30 +0800
comments: true
categories: [Eloquent Ruby, ruby, book]
---

#Chapter 8 - Embrance Dynamic Typing

##不需要抽象類別

在C++中，通常會先定義一個抽象類別讓其它類別來繼承並實現其中的Virtual Function，但是在Ruby中這個動作是多餘的。只要類別中有同名的method即可。

例如有二個類別`Document`, `LazyDocument`，其中`Document`的method `title`是回傳宣告時的值，而`LazyDocument`是回傳宣告時讀入檔案的值。我們不需要另外定義一個`BaseDocument`，其中還定義了`title`這個virtual method，這是一個多餘的動作。

所以有人說對Ruby來說，只要這個類別會像鴨子一樣呱呱叫（譬如就是有`make_sound`這個method），就可以把它當作鴨子。缺點就是如果沒注意的話，很容易在runtime時期遇到`undefined method`的問題。

##總結

這章大部份都是舉例，總結最後

- 只實作你需要的方法
- 利用命名來documentation
- 記得要寫test