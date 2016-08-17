---
layout: post
title: "Getting started with Phoenix - 2. What is Phoenix"
date: 2016-08-16 21:33:57 +0800
comments: true
categories: [elixir, phoenix, getting_started_with_phoenix]
---

## What is Phoenix?

Phoenix是一套基於Elixir語言的網頁應用開發框架，主體為MVC架構，很多的概念其實都有從Rails借來的影子，所以如果你本身有Rails的基礎，要入門Phoenix其實不需要太多的時間。

Phoenix要強調的是，Rails所提供的高效開發，在Phoenix上你也能同樣體驗到，並且它能提供更好的表現。其它像是Channel（其實就是Rails 5所主打的ActionCable）

這篇剛好最近Phoenix的作者Chris McCord所發表[Phoenix Channel和Rails ActionCable的比較(link)](https://dockyard.com/blog/2016/08/09/phoenix-channels-vs-rails-action-cable)

另外這個同樣是McCord[比較好幾個Framework Throughput的表現(link)](https://gist.github.com/omnibs/e5e72b31e6bd25caf39a)

而在Rails的開發哲學中，最重要的兩項

- Don't Repeat Yourself
這點在以functional programming為基礎上的Elixir，我認為可以更容易的將方法抽象出來成為module，另外Elixir所提供的Concurrency，大幅降低要寫多執行程式的困難度

- Convention Over Configuration
攝取大量Rails養份的Phoenix同樣遵循MVC架構，Restful Route設計，一些方便的generator，但又不像Rails那麼的Magic。譬如在Rails中的Controller Action，預設直接render對應到該action名稱的html檔案，但是在Phoenix中你必需明確的寫出要執行render的動作。

另外在Rails中常常單數複數在不同的情況下，Rails會幫你自動轉換，譬如說你有一張Table是people，就能假設有一個model是person。但有時候單複數轉換其實會讓人覺得困擾，所以在Phoenix中你只需要多打幾個字，明確的指定名稱，就能避免這些問題。

在我個人少許Phoenix開發的經驗中，甚至某些情況下我會覺得Phoenix開發速度甚至是比Rails快，因為compile會提醒你語法錯誤，未使用的變數名稱，typo等，這些在Rails上都必需等到實際執行才發注意到的問題。
不過也必需承認以Ruby的生態圈來說，應該還是會興盛好一陣子，所以以學習資源或是工作機會來說，Rails是毫無疑問的大勝

不過我仍然相信在學習Phoenix的過程中會得到一些在Rails上看不到的樂趣，讓我們繼續走下去:-)
