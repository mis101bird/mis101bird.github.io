---
layout: post
title: "[讀書心得]The Effective Engineer"
description: "本篇是我在soft&share讀書會所讀的一本書，The Effective Engineer的讀書心得。"
modified: "2016-11-20"
tags: [讀書心得]
image:
  feature: effective.jpg
  credit: hsuan-ju in Beitou, Taipei, Taiwan
---

### 推薦

<figure>
	<img src="/images/effective/cover.png" alt="book cover">
	<figcaption></figcaption>
</figure>

本書是我人生中第一本認真閱讀的"生涯成長類"書籍，以前我都將這種類型的書當成閒書在看，因為這種書往往都只是講一個概念，沒有非常具體的案例過程，
就算有也不一定是符合軟體工程師的。而這本書的不同點在於，作者 Edmond Lau 本身就曾經是Google工程師，也在Quora等...startup待過，經歷過許多風風雨雨，
並拿過往的經驗做了整理與分析後，寫成了這一本書。因此這本書的案例非常具體，除了自身經驗外，還補充了Facebook, Twitter等公司的辛酸史，並從案例裡面引出Effective engineer所需要的特質。
如果想要更詳盡的書籍介紹及購買辦法，請移步至<a href="https://softnshare.wordpress.com/portfolio/packageeffectiveengineer/">The Effective Engineer</a>。

PS: 一個人讀只有一種想法，五個人就會有五種想法，不同想法間的所激盪的火花常常會超出想像！<a href="https://softnshare.wordpress.com/">Soft&Share</a>有為這本書舉辦線上讀書會，不得不推，這是<a href="https://softnshare.wordpress.com/2016/10/18/softnshareboolclubnote/">我對於讀書會的心得</a>。

### 本書大綱

作者將本書分成三塊，教導我們如何成為有效率的軟體工程師。

1. 第一塊，要建立正確的心態，改變先從自己做起

* Focus on high leverage
* Optimize for learning
* Prioritize regularly

2. 第二塊，拼命執行，執行，再執行。不論是程式、人、團隊、專案、公司，都是要經過不斷迭代來改善自己、靠近目標的。

* Invest in iteration speed
* Measure what you want to improve
* Validate your idea early and often
* Improve your project estimate skill

3. 第三塊，建立長期的價值。高影響力的工程師，不能只是自己厲害就好，還要能帶給團隊正面的力量，幫助團隊成功！
如果一個工程師，能解決專案遇到的困難，又能凝聚團隊向心力，讓團隊向上成長，就是真神人了！

* Balance quality with Pragmatism
* Minimize operational burden
* Invest in your team growth

本篇心得不會詳述書本的內容，只會點出我印象深刻的幾個部分。主要會集中在第一塊跟第二塊。

### 心得

#### 第一塊，建立正確的心態
整本書用這個公式貫穿：

* Leverage = Impact Produced / Time Invested

我們要透過改善Leverage，來成為有效率的工程師。

由這個公式來看，提升leverage有兩個方法：

 1. 縮短花費的時間或擴大產出
 2. 選擇 high leverage 的事情，但什麼才是 high leverage 的事情?

首先第一點，就是改善自己的技術和解決問題的能力，來擴大產出；或是自身的增加專注力，像市面上有很多書籍，蕃茄鐘方法等...都是改善專注力的好辦法，
但作者認為提高leverage的效果是巨大且立即見效的，這算是一種做事的智慧，他舉了很多例子強調這一點，從個人，到團隊，到公司。

* 再來是學習的重要性，作者強調要時時刻刻關注在成長 (Growth)! 學習屬於重要但不緊急的事情。

* 最後是養成Prioritize的習慣

工作優先度: (1)重要又緊急的事(直接增加到產品價值) (2)重要但不緊急的事(Ex: 學習)
在(1)中，先選擇用最少“努力”達到“最大”價值的事。

如果想克服拖延症，可以用if-then方法。(Ex: "如果"這杯水喝完，我"就"學習新東西...)

#### 第二塊，拼命執行，執行，再執行
有了好的心態後，就要加強迭代，藉由驗證不斷修正自己的方向，達成目標。
快速疊代能提供有效的學習與能力上的提升。因此，我們應該不吝惜地投資在提升疊代的速度。
疊代的速度越快，越能即時地瞭解什麼決策是有效的，什麼是無用的。

要增加迭代速度，就必須節省做”不用動腦的工作”的時間，保持的原則是:

* 「只要需要重複執行兩次以上的工作，對值得為第三次之後的執行開發一個工具。」

能幫助節省時間的工具，不僅對個人有益，如果應用到整個團隊上，更能大幅的提升整體的效能。
還有像是改善自己的workflow、debugging flow，也可以幫助自己在以後程式出問題時更容易針對問題做修正。
舉例，假設一支程式的flow是A -> B -> C，你已經大概猜到bug是發生在B上面，你希望run code的時候，可以直接跑到B去重現bug，這樣就不用花時間在經過A的上面了。
此時軟體組件的耦合度低就很重要，才容易拉出來做單元測試，快速針對問題做測試。

* 熟悉你的開發環境

改善你的開發環境吧! 能自動化就盡量自動化，有更快速、更方便的工具，就盡量去學，減少手動操作的時間。

* 除了技術，也要注意人和

很多時候，我們花費最多的時間反而不是coding，而是其他有關於人的bottlenecks

再來就是需要好指標，指標不只能監控程式狀態，還會趨動整個團隊的開發方向。好的監控指標需要下面三的特點。

1. maximize impact，
	看你的目標是什麼，選對於目標有最大影響的指標，核心指標大於其他指標，當新功能使核心指標改善，卻使其他指標惡化時，就需要團隊去做取捨。
2. actionable，因果關係明確的指標(能確實反映團隊努力的指標)，像是網站的總瀏覽量/總註冊數等...就不適合，因為他不能確實反映產品新功能對指標的影響(前後立足點不同等原因)，好的指標像每週活躍用戶年齡層比率等...，就能明確了解到努力是否真的有達到目標。
3. responsive，快速反饋且干擾少，好的指標能快速得到反饋(feedback)，增加迭代速度，但也要能排除或弱化其他影響因素(noises)，例如per-minute response time metric的變化和雜音就太大了

定好指標後，記憶一些關鍵的數值也很重要，他們可以幫助你快速評估目前的狀況。
假如你要建立一個系統，要是你知道一些數值，像是平均從Memory讀取1MB的資料，大概要花250us
、平均從硬碟讀取1MB的資料，大概要花30ms、平均從網路讀取1MB的資料，大概要花10ms，
你可以就快速粗估整個系統所會花費的平均執行時間，然後比較已經建立好的系統的執行時間，也就可以大略的知道問題是出在哪邊。

最後，適當的懷疑數據並懂得排除不好的資料。
我們習慣用自己喜歡的方式去詮釋資料，這樣容易造成誤解而不自知。
我們要常常問自己下面這兩個問題：

1. Is there some way to measure the progress of what I am doing?
2. If a task I'm working on doesn't move a core metric, is it worth doing? Or is there a missing key metric?

有好的指標，就能更全面的瞭解目前的狀況，進而安排事情的輕重緩急。

#### 第三塊，建立長期的價值

第一點就是，要求完美常常是優秀工程師的致命傷，要懂得為現實情況去做妥協。

Ex. Code review VS 專案時程，
有時時程太趕，可以選擇先功能做出來，能執行並上線後，再重構和處理技術債的部分。

第二，找解決方案時，要選擇最穩定的做法，可靠穩定的解決技術，而不是最新最熱門的方法；因為可以降底維護新技術的成本，避免過多客製化的動作(這些其實都是能避免的)。

考慮以下情境:

1. 引入新語言:team member熟悉嗎?容易學嗎？新進員工學習曲線？
2. 換資料庫: 其他team的擴展效果也良好?維護和擴展成本?有比大部分的標準作法好?
3. 遇到問題，與其開發一條客製化的解決方法，增加整個系統的複雜度..不如重新規劃整個系統架構，簡化他！（不要像Printerest初期那樣越用越複雜越難維護），要評估兩者的利益
4. 你的data真的多到需要Cluster？別忘記Cluster也比單機更難維護和debug!

* 做簡單的事，用公認的解決方案比較穩健，不易出錯，且支援的套件也比較多。新技術支援少、懂得人少、套件不足，startup初期還是不要搬石頭砸自己的腳。

最後是專注在團隊的成長。

一個好的文化並不是一天造成的，我們不只是要自己成為effective engineer；更要建立一個effective engineering culture。

1. 幫助身邊的人成功
2. 僱人是high-leverage，投資在招募好的人才
3. 投資onbroading(員工訓練)和mentoring，形塑好的做事態度與觀念
4. 建立分享code的機制
5. 執行專案時隨時追蹤留下紀錄，專案結束後將經驗文件化，此為公司的眾人智慧

