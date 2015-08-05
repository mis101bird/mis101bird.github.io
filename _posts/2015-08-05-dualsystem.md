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

實驗室分配一個全新的電腦給我，因為硬碟容量超大，就想說來試試雙系統，就跟圖書館要了linux光碟來灌，還切了300多GB的主磁碟空間給它，沒想到那個linux Ubuntu的版本竟然是11.04！舊到連chrome都不支援了！ 所以我很隨便的直接在Win7裡把灌Linux的300多GB直接格式化了...關機後，開機就一片黑，只有**grub rescue>**在螢幕上方，我才很遲鈍的察覺大事不妙了...以下記錄我如何從崩潰的grub rescue>到重新灌完雙系統(Win7+Ubuntu 15)的過程。

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

#### 2. 燒Win7和Ubuntu 15.04的iso檔入CD

我在燒完Ubuntu 15.04才發現，現在<a href="http://distrowatch.com/dwres.php?resource=popularity">最火紅的Linux distribution是Mint</a>，Ubuntu越來越弱掉了...聽說Mint介面和Window相似，看來只能等下次再玩了！

Win7有內建的光碟映像燒錄程式能將光碟映像檔案 (這類檔案通常具有 .iso 或 .img 的副檔名) 燒錄至可燒錄的 CD 或 DVD。

步驟:

1. 將可燒錄的 CD、DVD 或藍光光碟插入光碟燒錄機。

2. 

![Smithsonian Image]({{ site.url }}/images/recover/recover-1.png)
{: .image-center}

ps: <a href="http://www.ubuntu.com/desktop/get-ubuntu/download">下載 Ubuntu</a> 安裝光碟的 iso 檔頁面是英文的，但是抓回來的安裝檔案一律是多國語言，所以，安裝中文版本的Ubuntu並不需要特地跑去中文版的網頁下載，等之後真正安裝時，語言設定再調即可。

3.

![Smithsonian Image]({{ site.url }}/images/recover/recover-2.png)
{: .image-center}

如果沒有找到內建的光碟映像燒錄程式，google一下也會發現網路上有提供很多免費的燒錄軟體。

