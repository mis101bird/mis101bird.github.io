---
layout: post
title: "[Git]什麼是Github flow"
description: "本篇解說如何在 Github 和別人協作開發"
modified: "2016-02-05"
tags: [git, github]
image:
  feature: panda.png
  credit: hsuan-ju in Taitung, Taiwan
---

### 前言
與以前的集中式(SVN)不同，開發者之間的協作方式因為 Git 的分散式特性而變得更為靈活多樣。其中，我認為 Github flow 很適合初學者，它實用又不繁複，對普通的小團隊非常夠用。此外，我也會在後面簡單介紹一下著名的 git flow，希望大家先熟悉 <a href="http://mis101bird.js.org/advancegit/">git branch 的操作</a>再繼續看下去。

<figure class="half">
	<img src="/images/git2/01.png" alt="tag">
	<figcaption></figcaption>
	<img src="/images/git2/02.png" alt="tag">
	<figcaption></figcaption>
</figure>

### Github flow

<figure>
	<img src="/images/git2/11.png" alt="log flow">
	<figcaption></figcaption>
</figure>

整個工作流程如下：

1. 將遠端repo clone到自己的本地端，並創造一條新分支，在新分支上開發。
2. 當新功能開發完成後，不要將新分支合併到master，直接向遠端repo發出 pull request，請求遠端接受合併。
3. 遠端repo的管理人和貢獻者經過code review和一來一往的討論。
4. 由遠端repo的管理人合併新分支至master。

#### 如何在Github開源社群做貢獻？
以下解說，當我們在 Github 閒逛，看到有興趣的 repo 時，要如何貢獻一己之力。

首先，我們按右上角的 **fork**，將別人的 Github 專案，完全複製一份到自己的 Github。

<figure>
	<img src="/images/git2/03.png" alt="log flow">
	<figcaption></figcaption>
</figure>

之後我們可以看到同名的專案出現在自己的 Github，此後我們所有的變更都是push到這個 repo。

<figure>
	<img src="/images/git2/04.png" alt="log flow">
	<figcaption></figcaption>
</figure>

Pull專案至本地端，並開始開發新功能。方式是產生一條屬於自己的新branch，以這一條branch做新功能的開發，千萬不要改動專案上的master分支，log flow 如下圖。

<figure>
	<img src="/images/git2/05.png" alt="log flow">
	<figcaption></figcaption>
</figure>

接下來，push變更到自己的Github。

<figure>
	<img src="/images/git2/06.png" alt="log flow">
	<figcaption></figcaption>
</figure>

向原來的專案作者發出 **pull request**(圖上兩個皆可點選)，請求他合併自己增加的新功能。注意，發出pull request後是無法取消的，所以要測試充足後再發出。

<figure>
	<img src="/images/git2/07.png" alt="log flow">
	<figcaption></figcaption>
</figure>

留言給原專案作者，說明一下變更的詳細內容。

<figure>
	<img src="/images/git2/08.png" alt="log flow">
	<figcaption></figcaption>
</figure>

往下拉可以看到自己所變更的內容（if you press "compare & pull request"），做最後的檢查。

<figure>
	<img src="/images/git2/09.png" alt="log flow">
	<figcaption></figcaption>
</figure>

我們可以看到，剛剛的發出的pull request已經在上面。接下來只要等原專案作者回覆了。

<figure>
	<img src="/images/git2/10.png" alt="log flow">
	<figcaption></figcaption>
</figure>

#### 遠端專案管理者
遠端repo的管理者需要隨時注意的地方為 issues 區域和 pull request 區域。issues區域需要管理者到"設定"去開放給外來者，如下圖。

<figure>
	<img src="/images/git2/12.png" alt="log flow">
	<figcaption></figcaption>
</figure>

之後不論任何能都能在issues這裡回報問題，並使用簡單的open/close追蹤，issue還能被多個tag標記，讓搜尋issue更方便。搜尋技巧可以見<a href="http://blog.miniasp.com/post/2014/04/08/GitHub-Issue-Search-Tips.aspx">這篇</a>。

<figure>
	<img src="/images/git2/13.png" alt="log flow">
	<figcaption></figcaption>
</figure>

專案管理者和貢獻者可以在pull request區域的對話過程中，用#引用issue和commit。有這個功能，讓討論變得非常方便！跨專案的issues引用也是ok的，只是要自行google了！

<figure>
	<img src="/images/git2/14.png" alt="log flow">
	<figcaption></figcaption>
</figure>

#### 結語
Gitflow簡單又好用，但如果要公司形式的多人合作，可以參考傳說中的Git flow，網路上有很多資料可以參考。

