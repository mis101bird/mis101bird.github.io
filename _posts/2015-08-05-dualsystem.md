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

實驗室分配一個全新的電腦給我，因為磁碟容量超大，就想說來試試雙系統，向圖書館要了linux光碟來灌，還切了300多GB的主磁碟空間給它，灌完後才發現那個linux Ubuntu的版本是11.04！舊到連chrome都不支援了！ 所以我就很隨便的直接在Win7裡把300多GB的磁碟區刪除...結果重新開機出現一片漆黑，只有**grub rescue>**在螢幕上方，我才很遲鈍的察覺大事不妙...以下記錄我如何從崩潰的grub rescue>到重新灌完雙系統(Win7+Ubuntu 15)的過程。
如果各位看官們還沒安裝雙系統，強烈建議別像我這樣隨便切磁碟機...太不慎重了..<a href="http://iuejeng.blogspot.tw/2012/06/win7ubuntu1204.html">教學這裡走</a>。

#### 1. grub的探索

首先使用指令**ls**，他列出我4個已經切割的磁碟區域，之後我follow下面步驟依序測試:

{% highlight ruby %}
ls                               # List the known drives (hdX) and partitions (hdX,Y)
ls (hdX,Y)/                      # List the contents of the partition's root
ls (hdX,Y)/boot/grub             # Normal location of the Grub 2 modules.
ls (hdX,Y)/usr/lib/grub/i386-pc  # Alternate location of the Grub 2 modules.
{% endhighlight %}

發現結果全部都是"error: unknown filesystem"，查了資料才知道這代表4個磁碟的OS都不是Linux系統，因為Linux系統是用資料夾的方式構成，這結果也非常合理，因為我4個磁碟區分別是window C槽、D槽、預留空間和舊Ubuntu已經被清除掉的300多GB磁碟空間。

Grub, or the Grand Unified Bootloader, is the most common boot loader for Linux.
而Window的boot loader(引導加載開機程序)是 Master Boot Record (MBR)，Grub無法啟動Window OS壓~

如果您是Linux系統還健在的請<a href="http://ubuntuforums.org/showthread.php?t=1599293">走這</a>，遇到和我一樣情況的請隨我往下。

#### 2. 燒Win7和Ubuntu 15.04的iso檔入CD

我在燒完Ubuntu 15.04才發現，現在<a href="http://distrowatch.com/dwres.php?resource=popularity">最火紅的Linux distribution是Mint</a>，Ubuntu越來越弱掉了...聽說Mint介面和Window相似，看來只能等下次再玩了！

Win7有內建的光碟映像燒錄程式能將光碟映像檔案 (這類檔案通常具有 .iso 或 .img 的副檔名) 燒錄至可燒錄的 CD 或 DVD。

步驟:

1. 將可燒錄的 CD、DVD 或藍光光碟插入光碟燒錄機。

2. 右鍵開啟光碟映像燒錄程式

![Smithsonian Image]({{ site.url }}/images/recover/recover-1.png)

ps: <a href="http://www.ubuntu.com/desktop/get-ubuntu/download">下載 Ubuntu</a> 安裝光碟的 iso 檔頁面是英文的，但是抓回來的安裝檔案一律是多國語言，所以，安裝中文版本的Ubuntu並不需要特地跑去中文版的網頁下載，等之後真正安裝時，語言設定再調即可。

3. 選擇CD所在之磁碟機並燒錄

![Smithsonian Image]({{ site.url }}/images/recover/recover-2.png)

如果沒有找到內建的光碟映像燒錄程式，google一下也會發現網路上有提供很多免費的燒錄軟體。

#### 3. 用Win7光碟開機

聽友人說先復原Window OS再安裝Linux比較不會出錯，可能是考量到Window我們比較熟悉的關係，有什麼萬一也比較能處理。

1. 插入Win7光碟後關機。
 
2. 開機，以ASUS電腦桌電為例，當螢幕進入"ASUS"畫面時，**長按F8**直到呼叫 Boot Device Menu(ASUS筆電為ESC)。
 
![Smithsonian Image]({{ site.url }}/images/recover/recover-4.jpg)

選擇第一項P1用光碟開機！

ASUS用F8做選取開機功能，跟**windows的F8**進安全模式相同，所以若要選擇安全模式或修復電腦時，可以在螢幕出現Window圖案時按F8，就會出現下圖畫面。

![Smithsonian Image]({{ site.url }}/images/recover/recover.JPG)

兩者之間有明顯時間差，所以只要在正確時間點按F8就不會叫錯選單。我們可藉由此按鍵修復電腦或近入安全模式，當然前提是Window健在。Win8進入安全模式需要多加設定，想知道細節請<a href="http://terryhung.pixnet.net/blog/post/30269288-%E5%AE%89%E8%A3%9D-win8-%E5%BE%8C%EF%BC%8C%E6%83%B3%E8%A6%81%E9%80%B2%E5%85%A5%E5%AE%89%E5%85%A8%E6%A8%A1%E5%BC%8F%E6%88%96%E4%BB%A5%E5%85%89%E7%A2%9F%E3%80%81%E9%9A%A8">入內</a>。

補充: Delete鍵開啟BIOS畫面

> BIOS是一個寫在主機板IC上面的程式，在每一次將電腦電源打開時，它就會開始「初始化」我們的電腦，整個硬體都準備就緒之後，才會開始執行OS。按Delete的時機一樣是當螢幕進入"ASUS"畫面的時候。BIOS能設定電腦設備的開機順序、查看主機板、中央處理器CPU的溫度、CPU風扇或是系統風扇的轉速…等等。

![Smithsonian Image]({{ site.url }}/images/recover/recover-10.jpg)

3. 恢復Win7後，開啟電腦管理

![Smithsonian Image]({{ site.url }}/images/recover/recover-5.png)

4. 查看磁碟管理，分割空間給Linux OS

我直接把之前亂割的310.50 GB 給新的Ubuntu了，滿浪費的...

![Smithsonian Image]({{ site.url }}/images/recover/recover-6.png)

5. 關機

#### 4. 用Ubuntu光碟開機並安裝

1. 如上面的1~2小項，把光碟換成Ubuntu

2. 照著步驟Next，注意安裝類型要選"其他(Other)"，第一項清除磁碟超可怕！！詳細請<a href="http://blog.xuite.net/yh96301/blog/242333268-%E5%AE%89%E8%A3%9DUbuntu+14.04">參考這裡</a>。

#### 5. Ubuntu網路問題

1. 安裝後重開機，就能看到下圖令人感動的螢幕。
 
![Smithsonian Image]({{ site.url }}/images/recover/recover-9.jpg)

2. 選擇Linux後，會發現有線網路無法連上，不知道為何雙系統不會自動偵測...可能是會跟Window OS衝到吧。所以我按右上方的網路圖示，對已經內建的connection 1 展開手動設定。

![Smithsonian Image]({{ site.url }}/images/recover/recover-3.jpg)
 
![Smithsonian Image]({{ site.url }}/images/recover/recover-8.jpg)

3. 對IPv4/IPv6(看妳IP用哪種)展開設定，也別忘DNS

![Smithsonian Image]({{ site.url }}/images/recover/recover-7.jpg)

4. 完成有線網路設定！

#### 6. 完成安裝Ubuntu後

剛安裝好Ubuntu時，最好都先升級現有已安裝的套件及其相依套件，兩個指令的差別討論<a href="http://www.arthurtoday.com/2014/12/ubuntu-apt-get-upgrade-vs-dist-upgrade.html">見此</a>。

{% highlight ruby %}
apt-get upgrade
apt-get dist-upgarde
{% endhighlight %}

之後就能在terminal使用apt-get指令安裝想要的軟體。這裡有<a href="http://chain.logdown.com/post/179184-linux-software-package-management">Linux軟體管理程序總攬</a>的介紹。

#### 7. 設定英文版介面，但擁有中文輸入法(個人愛好)

1. 設定英文版介面: 點選系統(System) ->管理(Administration)-> 語言支援(Language Selector)
 
2. 安裝好用的<a href="http://blog.xuite.net/yh96301/blog/287374341-Ubuntu+14.04%E5%AE%89%E8%A3%9D%E9%8D%B5%E7%9B%A4%E8%BC%B8%E5%85%A5%E6%B3%95%E7%B3%BB%E7%B5%B1gcin">中文輸入法gcin</a>。


再來就能好好痛快玩Ubuntu了！
