---
layout: post
title: "[Linux+Win7]從grub復活到雙系統"
description: "崩潰的復活經驗。"
modified: 2015-08-05
tags: [Ubuntu, linux, win7, 雙系統, 環境建置]
image:
  feature: newyork.jpg
  credit: hsuan-ju in New York
---

實驗室分配一個全新的電腦給我，因為硬碟容量超大，就想說來試試雙系統，就跟圖書館要了linux光碟來灌，還切了300多GB的主磁碟空間給它，沒想到那個linux Ubuntu的版本竟然是11.04！舊到連chrome都不支援了！ 所以我很隨便的直接在Win7裡把灌Linux的300多GB直接格式化了...關機後，開機就一片黑，只有**grub rescue>**在螢幕上方，我才很遲鈍的察覺大事不妙了...以下記錄我如何從崩潰的grub rescue>到重新灌完雙系統(Win7+Ubuntu 14)的過程。

#### 1. grub的探索

首先使用指令**ls**，他列出我4個已經切割的磁碟區域，之後我follow三步驟測試:

{% highlight ruby %}
ls                               # List the known drives (hdX) and partitions (hdX,Y)
ls (hdX,Y)/                      # List the contents of the partition's root
ls (hdX,Y)/boot/grub             # Normal location of the Grub 2 modules.
ls (hdX,Y)/usr/lib/grub/i386-pc  # Alternate location of the Grub 2 modules.
{% endhighlight %}

發現結果全部都是"error: unknown filesystem"，查了資料才知道這代表4個磁碟的OS都不是Linux系統，因為Linux系統是用資料夾的方式構成，這結果也非常合理，因為我4個磁碟區分別是window C槽、D槽、預留空間和舊Ubuntu已經被清除掉的300多GB磁碟空間。

Grub, or the Grand Unified Bootloader, is the most common boot loader for Linux.
而Window的boot loader(引導加載程序)是 Master Boot Record (MBR)，Grub無法啟動Window OS壓~

如果您是Linux系統還健在的請<a href="http://ubuntuforums.org/showthread.php?t=1599293">走這</a>，遇到和我一樣情況的請隨我往下。

#### 2. 燒Win7的iso檔入CD

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
嵌入的<a href="https://help.disqus.com/customer/portal/articles/472097-universal-embed-code">詳細教學</a>如下，先嵌入他們的code至自己的網站，再幫個人網站<a href="https://disqus.com/admin/create/">註冊comment board</a>，得到的shortname拿去更新_config.yml的設定就完成了!!
