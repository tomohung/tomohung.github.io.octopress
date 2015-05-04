---
layout: post
title: "Eloquent Ruby -7"
date: 2015-05-04 06:19:26 +0800
comments: true
categories: 
---

#Chapter 7 - Treat Everything Like an Object—Because Everything Is

##Classes, Instances, Methods

- Class就像是容器，可以裝很多Method。Class也像是工廠，製造出很多副本Instances

- 如果沒有指定superclass時，`Object`為預設繼承的類別。`Object`也是繼承了`BasicObject`

##Objects

- 反正什麼東西在Ruby裡面都是Object，可以測試這些

```
-1.class
true.class
nil.class
/regex/.class
```

- 如果puts出來，預設會用`to_s`轉換

##eval

```
cmd = 'gets'
puts eval(cmd)
```

`eval`可以像irb執行字串所代表的程式，這也是由Object繼承而來的函數

##public, private, protected

三種屬性來控制method的曝露性

```
class Method
  def public_method
    #預設為public，所有public method都可以呼叫
  end

  private # 在此之後方法都是private，只有在該instance內可以呼叫
  def private_method
  end
end
```

也可以這樣寫，不過我還沒看過實際有人這樣寫

```
class Method
  def private_method
  end

  private :prviate_method #在這指定private_method屬性是private
```

- private內的方法繼承到subclass後，**同樣可以呼叫**，這跟在C++中不一樣

- protected有夠難理解，原文是這樣的

> Any instance of a class can call a protected method on any other instance of the class

我是參考高見龍[這篇文章](http://blog.eddie.com.tw/2011/07/26/public-protected-and-private-method-in-ruby/)才比較理解為什麼

可以在irb這樣測試

```
class Base
  private 
  def private_show
    puts self.class
  end

  protected
  def protected_show
    puts self.class
  end
end
```

然後

```
class SubBase < Base
  def use_private
    private_show # 如果寫成self.private_show，會產生錯誤
  end

  def use_protected
    self.protected_show # 可以指定在self這個instance上
  end
```

- 原生Ruby source code中，20萬行的程式碼，大約有1000個private methods, protected methods只有50幾個。意思就是用到的機會也不多啦。

- 另外Ruby也有個設計，如果你實在想突破限制使用class內的方法，可以用`send`

```
sub_base.send(:private_show)
```

不過一般的作法大概都是另外寫一個public method把private method包起來給外部呼叫。

- public, private, protected並不是什麼特別的Ruby Magic，其實他們都是methods，呼叫後面帶參數

相同的還有`require`載入檔案

```
require 'rails_spec'
```

或者是`attr_accessor`

```
attr_accessor :name
```

然後我們就可以有`name`的getter和setter方法
