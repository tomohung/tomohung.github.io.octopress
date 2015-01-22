---
layout: post
title: "Reflection to tealeaf week 3"
date: 2015-01-13 22:23:15 +0800
comments: true
categories: [tealeaf, ruby, sinatra, http, bootstrap, html, css]
---

{%img right http://i.minus.com/ilSTmPNHVpVTm.png 320 %}

It's realy a tough week for me, because I'm totally new to web development.
There are lots of material need to be done:

- One new concept book: HTTP https://www.gotealeaf.com/books/http
- Two new languages: HTML & CSS http://www.codecademy.com/tracks/htmlcss
- Two new frameworks: [Sinatra][] & [bootstrap][]

[Sinatra]: http://www.sinatrarb.com/intro.html
[bootstrap]: http://getbootstrap.com/

I think the most important thing in this week is "HTTP is stateless".

And using Sinatra show me this property explicit.

Every request in main.rb is independant with each other, using instance variables to store data only survive its scope.

I also write a brief post to memo this property[(HTTP is Stateless)][].
[(HTTP is Stateless)]: http://www.tomohung.com/blog/2015/01/14/show-http-is-stateless-by-sinatra/


Using chache is another way to feel free in thinking programming in the old way.

Though I still use class to complete this assignment, I feel comfortable with this, because I don't need to make a wheel again.

The first struggle in this week is when I saw something like `<form action..../>`, I have no idea what's this?! because HTML & CSS course in codecademy never metion this. After google that, I found there are plenty of other syntax I didn't know, [w3shool][] help me to know more about HTML.

[w3shool]: http://www.w3schools.com/html/html5_intro.asp


Another struggle is Sinatra, when I first time read the intro, I totally had no idea what those nouns mean. `get '/' {}` seems not a nature ruby way; what `get` `post` are ??

Following with video and implement functions and pages, then I read Sinatra intro again, this time I feel much clear what its said.

Another horrible thing is on viewing video introduce to modern web development. There are so many tools, concepts, languages need to know. Wow...

To be a web development, I know I have to keep going. 
Moving to Lesson 4.

Finally, here is my blog talking about Reflections for tealeaf lessons.
http://www.tomohung.com/blog/categories/tealeaf/
