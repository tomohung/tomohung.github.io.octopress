---
layout: post
title: "Eloquent Ruby -4"
date: 2015-04-26 21:25:50 +0800
comments: true
categories: [book, ruby]
---

#Chapter 4 - Take Advantage of Ruby’s Smart Strings

string也和array, hash一樣有豐富的函式庫

http://ruby-doc.org/core-2.2.2/String.html

本章的結論就有Ruby提供的string非常好用，但壞處就是你要熟讀它。

-------

- double quoted string ", 比'還多了點用途

像是在字串中加入變數時#{}，我常忘了用'就沒有這個效果。

```
"Hi, #{user.name}."
```

- 遇到很煩的字串含有一堆單引號雙引號，可以用

```
%q{"Oh No", "I can't tell what's the difference between 's' and "s"."}
```

像上面的小寫q，也有對應用大寫Q的，效果就是像要加入變數時用：

```
%Q{ "Hi, I'm #{user.name}."}
```

- 可以隨意斷行

```
multi_line_string = '你抓
不到我
'

another_multi_line_string = %q{ 你抓
不到我 }

```

如果不想有new line，可以用back slash

```
multi_line_with_no_new_line = '你抓\
不到我'
```

- 超長文字可以這樣寫

```
really_long_string = <<EOF
這是開頭
...
這是結尾
EOF
```

- chop & chomp

長的很像的兩個函數，用法天差地遠

```
"a string with new line\n".chomp
```
會回傳 `a string with new line`，尾巴的new line會截掉。注意如果結尾含有多個new line，它只會截掉一個。

```
"a string".chop
```
會回傳"a strin"，回傳截掉最後一個字的結果。


- sub & gsub

替換字串內容

```
puts 'good good'.sub( 'good', 'bad' )
puts 'good good'.gsub( 'good', 'bad' )
```
得到的結果是

```
'bad good'
'bad bad'
```

- split 分割字串

```
"one two three four five".split
```

回傳一個矩陣

```
["one", "two", "three", "four", "five"]
```

或是分割參數

```
"one;two;three;four;five".split(";")
```
回傳結果同上

- index 搜尋字串位置

```
"actions speak louder than words".index("speak") # return 8
```

- each_char, each_byte, each_line

分別用迴圈回傳字元，位元數，每一行

- Ruby string is mutable

mutable雖然翻譯是可變異，但我覺得它的意義和指標有點類似

```
street_name = 'taiwan street'
road_name = street_name
street_name.upcase!
```

結果road_name = ?

注意像帶有!的函數常會有mutate的作用，所以上述結果road_name = 'TAIWAN STREET'

