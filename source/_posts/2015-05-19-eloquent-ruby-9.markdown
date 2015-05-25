---
layout: post
title: "Eloquent Ruby -9 [Write Specs!]"
date: 2015-05-19 09:11:44 +0800
comments: true
categories: [Eloquent Ruby, ruby, book]
---

#Chapter 9 - Write Specs!

##Don't test it, Spec it!

雖然內建MiniTest，但實際上寫測試的思維可以像是在定義規格，這也是`RSpec`的出發點。

在描述上更接近口語的方式：

```
doc.words.include?('blog') == true

#==> RSpec
doc.words should include('blog')
```

##Easy Stubs

理想上最好測試一次只測一個類別，但實際上很困難，所以可以利用stub來隔開同時間操作不同的類別。
概念上就是如果需要呼叫外部其它的類別時，賦值給其呼叫的函數

```
stub_outputer = stub :available? => true, :render => nil
```

如此當stub_outputer.availabe? 回傳true, stub_outputer.render回傳nil。

##Easy Mocks

不同於Stubs，我們希望在測試的函數中，有呼叫到另一個函數，在這裡不用考慮這個函數是否運作正常（這個動作應該在該類別中測試），所以我們期望在這個函數中有呼叫即可

```ruby
it 'should know how to output' do
  mock_outputer = mock('Printer') # <== mock objects
  mock_outputer.should_receive(:available?).and_return(true)  # <== expectation call
  @doc.output(mock_outputer).should == 'Done' # <== :avaliable? should be called in output method.
end
```
