---
layout: post
title: "Eloquent Ruby -2"
date: 2015-04-24 15:37:46 +0800
comments: true
categories: 
---

#Chapter 2 - Choose the Right Control Structure

說是Control Structure，我比較習慣說Control Flow，簡單就是說幾乎所有程式語言都有的流程控制語法，像是if, while, case等。

- Ruby比較特別的是有unless, until反向詞語，一開始用不大習慣，但後來寫著寫著，心裡慢慢習慣照著口語的語法說，現在反而比較喜歡這樣寫了，總之照Ruby的理念就是這些反向詞語的目的就是為了要減少在閱讀程式碼時，心裡還需要做轉換的浪費。

```
if not @read_only
```

看到not心裡還要先把@read_only的值反向

```
unless @read_only
```
讀起來比較順口

- 另一個蠻特別的點是，條件式可以放在最後面，也是為了配合口語語法

```
@title = new_title unless @read_only
```

- Ruby也有for迴圈，不過直接被捨棄不用（那當初幹嘛創造=___=），都用`each`。而且`for`也是用each實踐的，所以直接用each就好了。註：for語法不包含block，所以內中的變數scope是和for同一個，和each只存在於block中不一樣，each和for還是有些不同。

- case, 要注意的是在case中比較是用`===`，另外條件式也可以用regex來比較

這邊惡補一下幾個我不熟的operator

`<=>` 
Combined comparison operator. Returns 0 if first operand equals second, 1 if first operand is greater than the second and -1 if first operand is less than the second.

`(a <=> b) returns -1.`

`===` 
Used to test equality within a when clause of a case statement. 

`(1...10) === 5 returns true.`

`.eql?` 
True if the receiver and argument have both the same type and equal values. 

`1 == 1.0 returns true, but 1.eql?(1.0) is false.`

`equal?`
True if the receiver and argument have the same object id.  

`if aObj is duplicate of bObj then aObj == bObj is true, a.equal?bObj is false but a.equal?aObj is true.`


- 只有false和nil被測試時才會回傳false，`0`不是！還特別highlight以前寫C的人（哈哈），因為在C裡面`0`值被測試也是算false

- 延續上，因為false和nil被測試都是回傳false，所以要特別注意條件式中會不會false和nil混在一起

```
while next_object = get_next_object
  # Do something with the object
end
```
上面的迴圈是假設next_object最後會變成nil，而nil測試後回傳false。但萬一今天next_object本身的值就是false呢。改成下面的碼會比較好

```
until (next_object = get_next_object).nil?
  # Do something with the object
end
```

明確指定出next_object是nil才會跳離迴圈

- Ternery operator

這個以前在C++我就蠻愛用的，可以很漂亮的縮短一些簡單的if-else

```
result = (face == :beauty) ? "handsome" : "ugly"
```

- 另一個是常用的變數初始化技巧

```
@first_name ||= ''
```

@first_name如果還沒初始化，會被賦值''，如果已經有值，那就回傳本身。不過不要拿來對boolean type的變數這樣做，因為它也是倚賴 || operator機制。
