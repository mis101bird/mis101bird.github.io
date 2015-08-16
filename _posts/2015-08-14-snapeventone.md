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

1) 元件icon

我直接在這<a href="https://www.google.com/design/icons/index.html#ic_assignment_turned_in">下載</a>，如果想要客製化能參考<a href="http://romannurik.github.io/AndroidAssetStudio/" >Android Asset Studio</a>的ICON GENERATORS類別。

不同顏色但同圖案的icon能拿來提升使用者體驗。Android中的selector標籤可以根據狀態的不同為同一個View控件更換不同的圖片，舉個例子，我希望觸碰按鈕時，按鈕上的Icon能變色。

首先在drawable資料夾內建立button_selector.xml檔案，內含選擇器selector如下。

{% highlight html %}
<selector xmlns:android="http://schemas.android.com/apk/res/android">  
  <item android:state_pressed="true"  #按下按鈕
      android:drawable="@drawable/pic1"/>        #圖片1
  <item android:state_pressed="false" #放開按鈕
      android:drawable="@drawable/pic2" />       #圖片2
</selector>
{% endhighlight %}

再到原來畫面布局內的<Button>標籤引用button_selector(把selector當成像一種圖片)就完成了。

{% highlight html %}
<Button android:id="@+id/button1"
        android:layout_height="50dip" android:layout_width="150dip"
        android:text="Click To Check Style" android:background="@drawable/button_selector"></Button>
{% endhighlight %}

2)背景底圖、非ICON圖片

首先圖片可用photoshop、illustrator為工具協助繪製。關於背景圖，我用Google的<a href="https://www.google.com/design/spec/resources/layout-templates.html">layout templates</a>為底圖大小做設計，產出如下。

![Snapevent Image 02]({{ site.url }}/images/snapevent1/02.PNG)

特別推薦各位將背景圖(無論是螢幕或元件的背景圖)做成9-patch圖片，使背景圖片不失真，可随着文字内容的多寡而伸縮。

大家能使用ANDROID ASSET STUDIO的<a href="https://romannurik.github.io/AndroidAssetStudio/nine-patches.html">Simple Nine-patch Generator</a>來製作9-patch圖片。點入網頁，匯入已經設計好的圖片後，會看到類似以下的畫面，使用者能任意調整上下左右的線來規範圖片哪裡可伸縮，哪裡不可伸縮。調整好後下載會得到不同解析度資料夾，內有檔名為XXX.9.jpg/XXX.9.png的圖檔，將它們放入專案res內就大功告成了！如果改變主意，想再調整伸縮區域，直接在Android studio內點擊圖檔就能編輯了，編輯方法可參考<a href="http://www.cnblogs.com/slider/archive/2011/12/07/2279302.html">這裡</a>。

![Snapevent Image 03]({{ site.url }}/images/snapevent1/03.PNG)

3)Google Map的<a href="https://mapicons.mapsmarker.com/">客製化mark</a>

>  找配色時，好用的<a href="http://www.wingsv.org/2013/09/google-chrome-htmlrgb.html">取色軟體</a>也很重要，看到順眼的配色能直接把它的HTML色碼拿下來套在自己的App上。

## 建立專案與使用開源專案必注意：build.gradle

有Gradle自動建置工具對Android開發真的方便很多，好好了解對工作上提升不少。在下載專案前先查看app資料夾內的**build.gradle**，也能避免掉很多奇怪的錯誤。

1. **android:minSdkVersion**是宣稱此專案至少要在哪一個SDK 版本(API Level值)的Android系統上才能跑，這個設定值會影響Google Play是否將此程式顯示在商店中，如果使用者裝置的Android版本比程式中這一項設定值指定的版本低，此程式就不會出現在Google Play商店中。實際在執行程式時, 系統也會檢查此項設定值, 若是系統版本比程式的minSdkVersion設定值低， 也會禁止執行這個程式。不過要注意的是，這個設定值是開發者自己**宣稱**，至於這個程式是不是真的可以在該版本的Android裝置上正確執行, 就是開發者要負責的事情。所以我不只會看這個值，還會查看dependencies。
2. **android:targetSdkVersion**為目標版本，也就是測試環境下，模擬機的Android版本，這重要度不高，因為我都用自己手機測試...
3. **compileSdkVersion**為本專案用於編譯compile的SDK版本，在下載專案時別忘看一下，確認此SDK版本在Android studio的SDK Manager有沒有下載過。
4. **buildToolsVersion**為用於Gradle項目建置的Tool版本，基本上越新越好，因為build-tools有支援舊版本，所以buildToolsVersion > compileSdkVersion是OK的。在下載專案時別忘看一下，確認此版本在Android studio的SDK Manager的build-tools版本有沒有下載過。
5. **dependencies**內有使用的外部套件名，由gradle從maven庫自動網上搜尋並載入，超方便！
ex：compile 'com.android.support:appcompat-v7:22.2.1'，v7代表此包支援API 7以上，22代表版本號，詳細<a href="http://developer.android.com/intl/zh-tw/tools/support-library/index.html">入內</a>。

##系列進度

<div markdown="0">
<ol>
    {% for post in site.tags["snapevent"] reversed %}{% if post.title != null %}
      <li class="entry-title"><a href="{{ site.url }}{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a>： {{ post.description }}</li>
    {% endif %}{% endfor %}
</ol>
</div>

##專案REPO
<div markdown="0"><a href="https://github.com/mis101bird/SnapEvent" class="btn">SnapEvent</a></div>
