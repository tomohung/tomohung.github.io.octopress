---
layout: post
title: "Reflection of tealeaf week 2"
date: 2015-01-07 00:15:13 +0000
comments: true
categories: [tealeaf, ruby]
---
{% img right http://i.minus.com/ibnRw2O1pxsQam.png 320 %}

Although I have some option video not done yet, I want to share some of my reflection.

When I came to lesson 2, first I found that discussion is less than lesson 1 obviously. I think this imply the lesson OOP is harder than before. Thank god I finished three assignments in a week, how I can do this is because:

1. As a C++ programmer, OOP concept is also the same in Ruby, like inheritance, polymorphism, composition...etc. Since I have some experience to OOP, familiar with Ruby's style is not a hard work for me.

2. [`Object Oriented Program book`][] is fantastic!! I spent one night to read it, then next day I can just start to implement my idea without too much struggles. It clearly explain the relationship between `is-a` and `has-a` corresponding to inheritance and mixin.

[`Object Oriented Program book`]: http://www.gotealeaf.com/books/oo_ruby

And I do fall in love with Ruby, `convention` is over configuration becomes nature. In lesson 1, it's weird for me to pass a variable into method, but modified or not depends on what actions in the method. Now I start to think it's convenient to decide modified the variable in the method.
`attr_accessor` is another kind of example, default getter and setter helps a lot to reduce typing time.

Using polymorphism, I don't need to care about how using virtual function, just inheritance class then use it.
Especially for method `super`, the best way to reduce redundant code. 
One suggestion, If subclass's initialize method doesn't use super, then you might think the superclass and subclass relationship is `has-a`, not `is-a`.

I do love my [Tic-Tac-Toe][] and [Black Jack][] implemented by OO. In Tic-Tac-Toe, I use polymorphism to implement single player and double players mode. In Black Jack, I add feature that player can play with others NPC together, not just with dealer. This make the game likely in the real world. Without OO concept, these features are hard to implement.

[Tic-Tac-Toe]: https://github.com/tomohung/tealeaf/blob/master/Lesson2/oo_tic_tac_toe.rb
[Black Jack]: https://github.com/tomohung/tealeaf/blob/master/Lesson2/oo_blackjack.rb

Next lesson is absolutely my challenge, because I'm totally new for web development.
But I do look forward to it.