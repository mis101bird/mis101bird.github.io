---
layout: post
title: "[影片推薦]瀏覽器中Javascript的運作機制"
description: "介紹瀏覽器渲染步驟中Javascript的部分，包含Event loop的說明"
modified: 2018-05-01
tags: [browser, video, event loop]
image:
  feature: sydney1.jpg
  credit: hsuan-ju in Sydney, Australia
---

### 瀏覽器是如何做渲染的?

![Browserwork Image 01]({{ site.url }}/images/browserwork/01.png)

當使用者點擊一個按鈕觸發了 eventListener callback，瀏覽器會照上圖的步驟依序執行，先跑完 Javascript 再解析 CSS，CSS parser 產出的 CSSOM 會和 HTML DOM Tree 結合產生 Render Tree 後，交給 Layout 畫 element 佈局、Paint 畫像是圖片、顏色、文字等內容物、Composite 做不同圖層的疊加，最後輸出 pixel 到 Browser 頁面上。

上述過程中，有很多有趣的細節值得學習，下面針對 Javascript 的部分提供好康給大家參考!

![Browserwork Image 02]({{ site.url }}/images/browserwork/02.png)

### Jake Archibald: In The Loop - JSConf.Asia 2018

這個演講對上面的流程做了一個很棒的梳理，必看啊!

<iframe width="560" height="315" src="//www.youtube.com/embed/cCOL7MC4Pl0" frameborder="0"></iframe>

影片裡面提到 JS running code 和渲染 GUI render thread 不會產生 race condition，是因為兩者之間有 event loop 幫忙協調。Event loop 由瀏覽器的 main thread 負責執行，其他耗時操作 (ex. setTimeout, network, monitor) 會被分配到別的 thread 去執行，再回報給 main thread 去 render 畫面或執行 Javascript callback，如下圖。

![Browserwork Image 03]({{ site.url }}/images/browserwork/03.png)

Event loop 要搭配 task queue 才完整！task queue 可以比喻成 event loop 的 To Do List，event loop 會在空閒時取出 queue 中的任務 (task) 執行。

JS engine 和 GUI render thread 是互斥無法同時運行的，如果在 task 裡定義執行 while 無限迴圈，會 block event loop 導致瀏覽器畫面無法更新而死當，參考下面程式碼。

{% highlight javascript%}
setTimeout(function(){
    while (true) // run here forever
}, 0);
{% endhighlight %}

下圖白色的方框走到的地方代表 event loop 正在執行的任務，左邊是 Task queue，右邊是 GUI render thread，requestAnimationFrame 會在畫 Frame 之前執行 (Safari 的 Raf 是在畫 Frame 之後)，瀏覽器會保證在畫 Frame 前執行完全部 "已經發出" 的 Animation callbacks。

![Browserwork Image 04]({{ site.url }}/images/browserwork/04.png)

影片裡面有提到為何做動畫時推薦使用 requestAnimationFrame，而不是 setTimeout。

我們知道 setTimeout(callback, 0) 會將 callback 放到 task queue 等待執行，但我們無法強制瀏覽器在下個 Frame 被畫出來之前就執行這個 callback。

瀏覽器是根據當下資源利用的狀況來決定何時執行 task queue 的 task，它可以在兩個 Frame 之間的空檔執行任意多個 tasks，也可以一個 task 都不執行，這是我們用程式無法控制的，故像動畫這種講求畫面流暢度的 feature，為了保證動畫的 script 能在畫下一個 Frame 之前跑到，並避免因重複執行而浪費資源的風險，requestAnimationFrame 比 setTimeout 更適合用在做 UI 動畫上面。

![Browserwork Image 05]({{ site.url }}/images/browserwork/05.png)

講者還建議把 animation task 批量放在 requestAnimationFrame callback 執行，如果有需求要強制更新 Layout 可以使用 getComputedStyle 直接強迫 style recalculate，有些人會利用這個特性產生動畫，如下圖，但這種不正常的渲染方式會耗費較多資源，不建議常用。

![Browserwork Image 06]({{ site.url }}/images/browserwork/06.png)

講者最後有介紹到 microTask，Promise 的 callback 都會放在 microTask 等待執行，microTask有兩個和 Task 比較不同的特性：

1. microTask 會等 Task queue 空了才執行
2. 當執行 microTasks 時，會 block event loop 直到 microTask queue 的 task 都執行完，如果 microTask queue 的 task 太多會造成畫面停滯

整個 JS 執行的順序如下: Task --> microTask --> requestAnimationFrame，影片裡給了很棒的題目測試大家對 task / microTask 執行順序的理解，如果想深入探討可以參考這篇<a href="https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/">Tasks, microtasks, queues and schedules</a>。

---

在聽影片的過程，我也找了一些文章來解答自己的一些疑問，在下面分享。

### 瀏覽器從宏觀到細部的一次全面梳理
<a href="http://www.dailichun.com/2018/01/21/js_singlethread_eventloop.html">這篇文章</a>詳細說明了瀏覽器哪裡是 multi-process 哪裡是 single-process、哪些是 multi-thread 哪些是 single-thread，在看這一篇之前我一直懷疑 event loop 是包含在 JS engine 裡的，但如果包含那不就不是我以前學到的 single thread 了嗎 (哭)，好險在這裡找到了解答。

簡單來說，瀏覽器是 multi-process，一個瀏覽器只有一個 Browser Process，負責管理 Tabs、協調其他 process 和將 Renderer process 存至 memory 內的 Bitmap 繪製到畫面上 (pixel)；一個 Tab 一個 Renderer Process，負責頁面渲染和 script 的執行等，內部為 multi thread。Renderer Process 內主要有 GUI render thread / JS engine thread / 事件觸發 thread:

* GUI render thread 負責解析 HTML/CSS 和執行 Layout/Paint/Composite 等繪製過程，該 thread 也執行 reflow 和 repaint 操作
* JS engine thread 負責執行 JS script
* 事件觸發 thread 是 event loop 所在的 thread，負責協調 GUI render thread 和 JS engine thread
* GUI render thread 和 JS engine thread 互斥，一方執行就會 block 另一方

當用戶輸入網址，Browser process 會下載頁面資料，將它交付給 Renderer process 做渲染，之後再將 Renderer process 吐出的結果拿來做繪製。

### 從 Event Loop 規範探討 Javascript 異步和瀏覽器更新渲染時機
<a href="https://github.com/aooy/blog/issues/5">這篇討論</a>將 event loop 梳理的很仔細，留言的部分也有很多人補充。裡面有說到 event loop 是在 HTML Standard 定義的，不包含在 Javascript engine 的實作裡面。撰寫 Javascript 時所用到的 Ajax 網路資源請求、修改 DOM、EventListener attach 等，都是使用瀏覽器的 Web API，Web API 會將這些任務丟到其他相對應的 thread 執行，threads 執行完會將 callback 丟到 event loop 的 task queue 等待。

### What forces layout / reflow
<a href="https://gist.github.com/paulirish/5d52fb081b3570c81e3a">這篇 Gist </a> 說明在 Javascript 哪些設值/取值的過程會造成畫面 reflow! 

![Browserwork Image 07]({{ site.url }}/images/browserwork/07.png)

Reflow 是造成 Layout style 重新計算的意思，它同時也會將新的 Javascript 變更都計算進去，用下面同一段 code 舉例，當執行 getComputedStyle 時，在 getComputedStyle 之前的程式碼，像是 translateX(1000px) 也會被計算進去並呈現在畫面上；但在 getComputedStyle 之後的程式碼則不會，有人會用這個特性製造動畫效果，但不推薦。

![Browserwork Image 06]({{ site.url }}/images/browserwork/06.png)