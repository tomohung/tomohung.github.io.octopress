---
layout: post
title: "nitrous.io 開發環境"
date: 2015-01-11 21:26:49 +0800
comments: true
categories: [nitrous, web]
---

{%img http://i.minus.com/iyZNw6vDZnJa6.png 800 %}

- 太強了
- 太強了
- 太強了

因為[nitrous][]太強了，所以說三遍。

[nitrous]: http://nitrous.io

入門網路應用程式開發一開始最大的門檻就是安裝適合的環境，尤其是OS用Windows的，好像是次等公民一樣，一下是終端機要用跟別人不一樣的，一下又是安裝套件卡住，一開始一頭熱的想要投入，馬上就被潑盆冷水。

<!--more-->

之前我自己的桌機就是用Windows，弄了很久才裝起來可以跑，但就是常遇到怪事，所以就放棄了。現在有一台MBA，少了很多問題，至少按照網路上搜尋到的指引文章照著做，多半都沒什麼意外。

但有時候在外面想用其它電腦看一下自己做的東西時，這時候[nitrous][]配合[github][]就派上用場了。nitrous當作雲端編輯平台，github負責同步資料，至少目前我在學習的階段都沒遇到什麼問題，聽說[tealeaf][]裡也有人用nitrous完成所有課程。

[github]: http://github.com
[tealeaf]: http://gotealeaf.com

{%img http://i.minus.com/ibjA5RfvgDSmp6.png 640 %}

Ruby Girls也是用這套教學，要知道還沒開始就在安裝遇到挫折，真的沒有動力再做下去 <== 就是在說我自己

{%img http://i.minus.com/ibKMcP19URJvJ.png 640 %}

首頁寫的60秒完成不是開完笑的，註冊後有200閃電幣可以用，綽綽有餘，開一個Box只要10個閃電幣，可以選擇的有上圖幾種。

{%img http://i.minus.com/iDo0NvpKvypX6.png 640 %}

登入後可以選擇要IDE介面還是Terminal介面，如果選IDE就像上面一樣。

以編輯器來說，內建的己經蠻好用的，也可以選擇Vim模式。

上圖左邊就是雲端儲存的資料夾，有1.5Gb，寫程式來說應該是夠放，下面就是Terminal，git都己經裝好可以直接用，可以直接把自己github上放的project直接clone下來，五分鐘內就可以從無到有開工。

###Preview

比較要注意的是Preview時，根據官方Support說，Localhost ip必需設定為0.0.0.0，port的話可以參考下圖：

{%img http://i.minus.com/ioeX2cUhUAC5A.png %}

所以如果是Ruby配合shotgun gem來啟動的話，可以用下面指令：
```
$ shotgun xxx.rb -o 0.0.0.0 -p 3000
```

指定`Localhost ip = 0.0.0.0, port = 3000`

本機的開發環境當然還是要建立，不過可以等真正入門後再來考慮吧。