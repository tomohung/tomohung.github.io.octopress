---
layout: post
title: "Eloquent Ruby -3 [Take Advantage of Ruby’s Smart Collections]"
date: 2015-04-24 16:33:18 +0800
comments: true
categories: [Eloquent Ruby, book, ruby]
---

#Chapter 3 - Take Advantage of Ruby’s Smart Collections

直接結論，Ruby的Array跟Hash提供很多方便的函數可用，可以參考

[Array](http://ruby-doc.org/core-2.2.0/Array.html)
[Hash](http://ruby-doc.org/core-2.2.0/Hash.html)

以下才是正文開始

------

- 如果是要初始化一個給值的陣列，如

```
love_words = ["Nancy", "I", "love", "you"]
```

也可以這樣產生
```
love_words = %w{ Nancy I love you }
```

－ Ruby沒有指標，但是也有個類似指標的用法

```
def echo_all( *args )
  args.each { |arg| puts arg }
end
```

*暗示arg其實是個陣列值，可以遞進來做陣列處理。

- 對於hash，可以這樣用

```
run( {speed: :fast, tools: :train} )
```

也可以不要大括號，這樣寫

```
run( speed: :fast, tools: :train )
```

甚至

```
run speed: :fast, tools: :train
```

不過在參數只傳遞一個hash時，這樣寫蠻清爽的，如果是像form_for中要傳html_options時還這樣寫，簡直是個閱讀災難，我被誤導了好久。


－ 雖然array和hash都有提供each這個method，不過hash在使用上有些不一樣的地方

只給定一個參數的話
```
hash_value = { one: 1, two: 2, three: 3 }

hash_value.each do |value|
  #something for value
end
```

value其實是個矩陣，如[:one, 1], [:two, 2], [:three, 3]。

也可以給2個參數

```
hash_value.each do |key, value|
  #something for value
end
```
key和value就分別對應到hash值上，這就不用多說了。

- 另外有個`inject` method可以幫助加總所有元素

```
def average_word_length
  total = words.inject(0.0){ |result, word| word.size + result}
  total / word_count
end
```

－ 不要再each裡面操作delete的動作，如果需要的話可以用`array.delete_if` method。

－ 如果只是要檢查某個元素有沒有已經存在陣列中，可以考慮改用`Set`這個class，而不是用Array。
http://ruby-doc.org/stdlib-2.2.2/libdoc/set/rdoc/Set.html

它在檢查效率上比較好。