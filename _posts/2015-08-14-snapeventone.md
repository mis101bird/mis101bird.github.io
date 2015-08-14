---
layout: post
title: "[SnapEvent]我的第一個Android app-規劃"
description: "規劃我的第一個App-SnapEvent，並附此系列大綱、說明"
modified: 2015-08-14
tags: [android, snapevent]
image:
  feature: snapevent1.png
  credit: hsuan-ju in Petty Harbour, Newfoundland
---

自學Android以來將近一個月，終於來到自己開發一個App的時刻了！這一系列將記錄我從頭到尾如何開發出上架的App。

## 點子-SnapEvent

台北最棒的就是**四通八達的捷運**，和**豐富的多元活動**！但我的行程變動性滿高的，除非是大型活動，否則我不會刻意去追蹤每個小活動，這樣會常lose掉一些其實能參加的好講座、好活動。舉一個例子，我今天從外地回到台北捷運士林站，時間17:00，當天剛好捷運善導寺站附近有一個Workshop在18:00~21:00，但因為我不知道這個資訊，就錯過了參加的機會...我希望有一個App能顯示**當天**所有的講座、活動並標示在地圖上，讓我能**一時興起**去聽個演講、參加個討論會等...

## 設計圖

![Snapevent Image 01]({{ site.url }}/images/snapevent1/01.jpg)

會用到的大概有Service組件、Broadcast組件、ViewPager、ListView、圖片與資料的緩存優化等...還沒開始做所以細節不知道(揍)

## 前置作業-UI製作

這是我最愛的部分了！！

1) 元件icon-我直接在這<a href="https://www.google.com/design/icons/index.html#ic_assignment_turned_in">下載</a>，如果想要客製化能參考<a href="http://romannurik.github.io/AndroidAssetStudio/" >Android Asset Studio</a>的ICON GENERATORS類別。

2)背景底圖、非ICON圖片

首先圖片可用photoshop、illustrator為工具協助繪製。關於背景圖，我用Google的<a href="https://www.google.com/design/spec/resources/layout-templates.html">layout templates</a>為底圖大小做設計，產出如下。

![Snapevent Image 02]({{ site.url }}/images/snapevent1/02.PNG)

感恩神人在Github釋出的開原軟體<a href="https://github.com/redwarp/9-Patch-Resizer/releases">9-Patch-Resizer</a>，能讓我們製作出適合不同屏幕size的圖片放入xxhdpi、xhdpi、hdpi、mdpi等...資料夾。

3)Google Map的<a href="https://mapicons.mapsmarker.com/">客製化mark</a>

>  找配色時，好用的<a href="http://www.wingsv.org/2013/09/google-chrome-htmlrgb.html">取色軟體</a>也很重要，看到順眼的配色能直接把它的HTML碼拿下來套在自己的App上。

##系列進度

	<ol>
    {% for post in site.tags[snapevent] %}{% if post.title != null %}
      <li><a href="{{ site.url }}{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a>: {{ post.description }}</li>
    {% endif %}{% endfor %}
	</ol>
		
##專案REPO
<div markdown="0"><a href="https://github.com/mis101bird/SnapEvent" class="btn">SnapEvent</a></div>
