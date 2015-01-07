---
layout: post
title: "Reflection to Tealeaf week 1"
date: 2015-01-01 16:03:33 +0800
comments: true
categories: [tealeaf, ruby]
---

{% img right http://i.minus.com/ibsevSRoOPCEmU.png 320 %}

Happy new year!!

Before I talk about where I struggled with, I would like to say something before I join the course.
I am using C++ for few years, and coding is part of my job. Before join the course, I've also done the [Learn Ruby The Hard Way][] to lesson 45. `Learn Ruby The Hard Way` is also a good pratice, especially for `muscle memory`. It focus on `just typing ruby` when learning ruby knowledge. 

[Learn Ruby The Hard Way]: http://learnrubythehardway.org/book/
 
After that, I start `Prep Course` @ tealeaf, reading the book [`Introduce to Programing`][]. `Introduction to Programing` is also an amazing book, it use a simple way to explain more detail in ruby, and I really did several times "Ah ha!" when reading it. Then I thought I was ready for lesson 1, but maybe I'm wrong :p

[`Introduce to Programing`]: http://www.gotealeaf.com/books/ruby

About my struggles, as a C++ programmer, there are mainly two "What?" for ruby:
  
  - variable scope: in C++, every local variable only survive in its scope, but in ruby, this is not always true. Even now, I'm still a little confused with that. Glad that I find a way to deal with this: `Never use outside variable in insdie scope, make them in different name`.
  
  - pointer & reference: when passing a variable to functions(in ruby called: methods), if varaible has prefix like `*` or `&`, it obvious means this variable may be modified in this methods. Ruby doesn't take this way, input variables in method could be modified just by what action do inside the method. Fotunately, ruby usaually append suffix `!` behind method's name to indicate this method modify input variable.
  
Even these, I do love ruby.

  - There are a lot of built-in convenient methods for String, Array, Hash...etc. And more are waiting to discover
  - More fluent phrase for flow control, if, unless, while, until...etc. Readable code is becoming more and more important.
  - type less key :p

And special Thanks for TA's:
Without their help, I couldn't finished these assignments on time. Because of unfamiliar with ruby methods, wrong code style, improper naming...etc. They always give me timely advice.

Last, share my experience for developing game `Black Jack`:

  - write psuedo code : 10 min
  - coding : 2 hours
  - debugging : 2 hours more
  - feedback from TA, then refactor: 2 hours
  
By the way, the most difficulty in Lesson 1 is completing "Tic-Tac-Toe" AI, it tooks me 10 hours more.
If you are totally new to program, don't be disppointed at spending a lot of time, because me too!!
If you are take these assignment as piece of cake, well, let's move to Lesson 2. See you.