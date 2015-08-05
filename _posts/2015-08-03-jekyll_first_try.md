---
layout: post
title: "[jekyll]我的第一個Github個人網站"
description: "Jekyll 是目前static site generators中最有名、最多人使用的，在本篇將記錄我第一次使用jekyll建立github個人網站的經驗。"
modified: 2015-08-03
tags: [jekyll, web]
image:
  feature: 20150730_105942.jpg
  credit: hsuan-ju in C.I.O.F.F.
---

從2014申請了GitHub，直到現在2015才下定決心好好經營(之前都當放垃圾專案的地方XD)，希望我能持續堅持下去!!讓我的Github的Contributions每周都有commit!

### 我為什麼使用Jekyll
最初覺得LinkedIn很空，就想先從建立自己的個人網站開始擴充履歷，但一想到要租server就覺得很煩(完全免費的都好爛)。之後在網路偶然看到"快又完全免費"的架站方式:<a href="http://blog.winwu.today/2013/06/githubio.html">建立github.io專頁</a>才發現原來能用Github架站!真所謂眾裡尋他千百度，驀然回首，Github卻在燈火闌珊處。

但整個網站要從無到有需要花很多時間投入，當時我在致力學習APP，不想花太多時間在網頁建置，就開始尋找好用的automatic generator!
找到了<a href="https://www.staticgen.com/">StaticGen</a>，發現了**排名第一的Jekyll**，Github本身官方的支援讓這個開源生產器的star足足是第二名的2倍以上! fork數量更是第二名的5倍多!太強了! 很潮所以用了!! (喂)

fork數量很多代表有許多人拿此framework再客制化，開源樣版的提供相對也會多，這裡有好心人做的開源theme整理! 選一個自己看順眼的再加工比重頭開始速度快多了:<a href="https://github.com/mattvh/jekyllthemes">jekyll themes</a>

### Jekyll是什麼

Jekyll 是使用 Ruby 開發的「靜態網站產生器」（static site generator)，和一般直接以 HTML 編寫靜態網站不同的地方是，Jekyll 產生的網站可以有佈景、模組等動態的好處，當你需要修改網站整體的佈局（layout），只需要設計、切換為新的佈景，重新產生的網站就會套用新的外觀，所以平常撰寫Blog文章時只用新增md檔，不需動到coding部份。

### 我流Jekyll環境建置

如同上面提到，Jekyll只有在改變layout style時才需要動到code，其他時間寫Blog時只要新增/編輯md文字檔即可。
寫過程式的人都知道搭建環境"超級煩"，我個人又愛在實驗室、家裡、圖書館跑來跑去，筆電又好重懶得帶，有什麼解決的好方法呢?

#### 1. 使用<a href="https://koding.com/">Koding</a>作為開發環境 

Koding是一個網站，他專門在提供開發部份雲端服務。在Koding網站裡，你可以擁有自己的VM（Linux終端機），並且使用他來開發程式或開發網站。目前Koding支持的語言開發為Python、**Ruby**、Perl、HTML/CSS、PHP、Node.js......等只要能夠在Linux上執行的程式語言都可以使用。當然最重要的是他的服務完全免費！有它不只能無時無刻coding，也省掉建置Ruby環境的麻煩了!!

#### 2. 搭建Ruby之上的Jekyll環境

Jekyll官方網站的<a href="http://jekyllrb.com/docs/quickstart/">Quick-start</a>有很清楚的說明。因為Koding環境基本上都建置好了，故只要在terminal打以下指令就完成建置了。

{% highlight ruby %}
sudo gem install bundler
sudo gem install jekyll
sudo bundle install
{% endhighlight %}

#### 3. 選一個喜歡的theme並fork到自己的Github repo: <a href="https://github.com/mattvh/jekyllthemes">jekyll themes</a>

#### 4. 改repo名稱(如果為建置個人網站請改為: 你的帳號.github.io)

#### 5. clone剛剛fork的專案至Koding

{% highlight ruby %}
git clone repoURL
{% endhighlight %}

#### 6. 在Koding跑server

Koding的環境為VM，不能用localhost或127.0.0.1 run server，必須以0.0.0.0為host。

{% highlight ruby %}
cd repo
bundle exec jekyll serve --host 0.0.0.0
{% endhighlight %}

再用VM setting的URL:**port**開啟網站觀看運行結果

![Smithsonian Image]({{ site.url }}/images/jekyll_1_pic1.JPG)
{: .image-center}

*之後就能在Koding設計自己的layout style，詳細設計方式各位下載的theme的github頁面大都有詳細撰寫。*

#### 7. 寫文章直接在自己的github repo的_post資料夾新增md檔，按照README.md的方式撰寫即可。

方便又簡單吧!!下次有機會我再自己設計layout style!! 
---
關於讀者留言comment部分，大推<a href="https://disqus.com/">Disqus</a>的嵌入式留言!
嵌入的<a href="https://help.disqus.com/customer/portal/articles/472097-universal-embed-code">詳細教學</a>如下，先嵌入他們的code至自己的網站，再幫個人網站<a href="https://disqus.com/admin/create/">註冊comment</a>，得到的shortname修改_config.yml就完成了!!
