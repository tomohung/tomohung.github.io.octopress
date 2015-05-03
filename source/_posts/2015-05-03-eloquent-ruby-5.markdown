---
layout: post
title: "Eloquent Ruby -5"
date: 2015-05-03 20:38:53 +0800
comments: true
categories: [ruby, book]
---

#Chapter 5 - Find the Right String with Regular Expressions

##Match One Character

- `.`可以match任何單一字元(except new line)

- 如果想match `.`本身，可以用`\`，如`3\.14`

- 再譬如`.r\.`，可以match `Mr.` or `Dr.`

##Sets, Range, Alternatives

###Sets

- 例如`[0123456789]`可以match任何一個數字字元

- `[pPaA][Mm]`可以match `Am` or `pm`.

###Range

- `[0-9a-f]`可以match任何一個16進位的字元

- `\d`可以match任何一個數字字元，`\d\d`可以match 連續2個數字字元

- `\w`可以match任何一個字元，包含數字、底線等

- `\s`可以match任何一個空白字元，包含space, tab, new line.

###Alternatives

- `|`是替換符號

- `A\.M\.|AM|P\.M\.|PM` 可以match `A.M.` or `AM`, or `P.M.` or `PM`

- 處理時間格式可以用 `\d\d:\d\d (AM|PM)`

##Asterist `*`

- *星號可以表示在星號前的字元有任意個，包含零個。

- `AB*`，可以match `AB`, `A`, or `ABBBB`

- 所以可以用 `[0–9]*`，match任何長度的數字

## Regular Expression in Ruby

- Format regex by slash: `/\d\d:\d\d (AM|PM)/`

- use operator `=~`

- 所以在irb中跑這段

```
/\d\d:\d\d (AM|PM)/ =~ '10:24 PM'
```

回傳`0`，這表示字串有符合，從index = 0的位置開始。

- 如果沒有符合，回傳`nil`

- 可以變成case insensitive，加上i，如：`/AM/i =~ 'am'`

- 也可以當參數傳入gsub來用

```
time_string.gsub!( /\d\d:\d\d (AM|PM)/, '**:** **' )
```

##Beginning and Ending

- 如果想要match字串的開頭用指定的文字，可以加入`\A`，如`\AIn the Beginning`，可以match字串前面為`In the Beginning`

- 反過來如果想要match字串結尾用指定的文字，可以在尾巴加入`\z`

- 如果想要match字串中`任何一行的開頭`有滿足，可以在條件字串前面加上`^`

- 如果想要match字串中`任何一行的結尾`有滿足，可以在條件字串尾巴加上`$`

- 如果想要進階同時match開頭跟結尾

```
/^Once upon a time.*happily ever after\.$/
```
這樣會失敗，因為`.`配合`*`雖然可以取代中間的任意數量的字元，但`.`不包含new line

```
/^Once upon a time.*happily ever after\.$/m
```

在結尾加上`m`可以關閉`.`這個特性。

- `?`的作用和`*`類似
