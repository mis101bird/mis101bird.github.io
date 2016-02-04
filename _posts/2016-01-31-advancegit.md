---
layout: post
title: "[Git]分支的管理與常用功能"
description: "本篇記錄我學習git的心得，包含checkout、reset、fetch、merge、rebase、cherry-pick、revert等指令的使用時機和方式。"
modified: "2016-01-31"
tags: [git, smartgit]
image:
  feature: pics.png
  credit: hsuan-ju in Taipei, Taiwan
---

這裏是我學git一段時間後，所做的學習筆記。建議各位對git有一定熟悉度再看這一篇。

### Git GUI tool: SmartGit
我最初使用git的時候，對branch的概念不熟悉，又固執的只用git shell做git操作，導致每次branch一複雜就會亂了手腳。之後因為一次機緣，試用Git GUI工具才發現，它不只會顯示log flow讓使用者了解目前版本進度，還能直接點選flow上面的commit版本做checkout、reset等操作，對我掌控整個git的版本控制非常有幫助，也讓我比較有“勇氣”用git輔助比較複雜的專案做開發，強烈推薦大家使用。

眾多Git GUI工具中，<a href="http://www.syntevo.com/smartgit/">SmartGit</a>一直蟬聯使用者喜好度的排行榜第一名，可惜它要錢。如果我們只是要在Github上面做貢獻，可以使用SmartGit的non-commercial版本，但如果是公司要使用，可能就要另尋工具了。除了SmartGit，網路上仍有許多免費的GUI工具可以使用，像是<a href="https://code.google.com/p/tortoisegit">TortoiseGit(Windows)</a>和<a href="http://www.sourcetreeapp.com">SourceTree(Windows/Mac)</a>。提醒一下，SourceTree的log flow要調整為 Ancestor Order(以各版本祖先的順位做排序)，才會看到我們比較熟悉的log flow。
#### SmartGit log flow:

<figure>
	<img src="/images/git1/01.png" alt="smartgit log flow">
	<figcaption></figcaption>
</figure>

點選log flow上面的commit版本並右鍵，就能對其進行checkout/revert等...操作，本flow是標準的Github flow開發方式，其他貢獻者自行開分支增加新功能或修正issue，修正好，測試完成後才合併回master主支。這種開發方式簡單易懂，又能很清楚看到哪些功能是哪個貢獻者開發的，又在哪個階段合併回主支的。詳細說明會在之後的章節提到。

### log flow中，branch flow的差別

<figure>
	<img src="/images/git1/0.png" alt="smartgit log flow">
	<figcaption></figcaption>
</figure>

#### SmartGit Diff:

<figure>
	<img src="/images/git1/02.png" alt="smartgit diff">
	<figcaption></figcaption>
</figure>

由左至右可以看到上一個commit和這一次commit間改動的情況，沿著顏色光標對照非常清楚。右上角有紀錄此commit共增減多少行程式碼，上下箭頭會引導到每個不同的更改內容。
### 基礎提醒
#### HEAD
Git 是如何知道你當前在哪個分支上工作的呢？其實答案也很簡單，它保存著一個名為 HEAD 的特別指標。在Git中，它是一個指向你正在工作中的本地分支的指標，會一直指向分支的頭，在HEAD下commit的版本紀錄才會被分支追蹤到。這裏提供<a href="http://devtian.me/2015/03/07/Git-studynotes-mechanism/">Git原理筆記</a>給大家做進一步了解。

#### git log & git reflog
* git log: 顯示開發者自身commit過的紀錄
* git reflog: 顯示**"所有"**commit過的紀錄，包含git操作時自動commit的紀錄，EX: checkout/pull/push等...紀錄

### 重要原則
Git branch相關的指令多到令人眼花撩亂，但有幾個原則搞清楚，在操作指令時就能比較得心應手。

* 任何指令操作，被改變的永遠都是**當下工作目錄**！
* 任何指令操作，被改變的永遠都是**當下所在分支**！

以下舉merge和rebase的例子給大家看，針對下圖log flow做操作。

<figure>
	<img src="/images/git1/05.png" alt="flow">
	<figcaption></figcaption>
</figure>

首先是merge! 我們先git checkout master到master分支，之後的操作就只會動到當前分支：master branch。再來用git merge topic，將topic合併到master。如下圖，我們看到master增加了**H 版本：合併topic branch至master branch**。

<figure>
	<img src="/images/git1/03.png" alt="merge">
	<figcaption></figcaption>
</figure>

下一個例子是rebase，我們先git checkout到topic分支，之後的操作就只會動到topic branch。再來用git rebase master，將**當前這個分支**的基點移到master上。原來的master分支沒變，只有topic分支的基底位置變了，如下圖。

<figure>
	<img src="/images/git1/04.png" alt="rebase">
	<figcaption></figcaption>
</figure>

由上述例子，我們可以知道執行操作時，被改變的永遠是**當下所在的分支和工作目錄**，接下來我們挑幾個git指令做說明。

### Checkout
git checkout是切換工作目錄的意思。
此指令有兩種使用方式，一種是切換工作目錄(含HEAD)至其他branch；另一種是切換工作目錄(不含HEAD)至之前的commit版本(不限定哪個branch)，其中有很多細節需要注意。

(1) git checkout **branch name**

常和git branch一起使用，使用方式如下：

1. git branch test #增加一個新branch叫test，等於git checkout --orphan test，orphan為孤兒的意思
2. git checkout test #切換工作目錄至test branch

以上兩個指令可以用一條指令：**git checkout -b test **取代，執行後，工作目錄和HEAD都會在test分支的最新紀錄上。
以下介紹幾條git branch指令：

1. git branch -d 分支名（刪除分支）/ git branch -D 分支名（強制刪除分支）
2. git branch --list {pattern}(ex:t*)  #條列所有分支(ex:條列所有t開頭的分支)
3. git branch 分支名  #增加分支

(2) git checkout **commit point**

本指令將切換工作目錄至之前的commit版本，任何git reflog內有的commit紀錄都能切換到。

但要注意，執行此指令後，因為HEAD沒有跟著此指令做切換(HEAD detached(分離))，在那一點後所做的任何commit都不會有branch追蹤。如果一時疏忽跳到其他branch，從那點後所做的所有commit都會失蹤(commit紀錄都還存在，但沒有branch追蹤到它們)。如下圖，左圖有2個紀錄，commit: A add 2和commit: A add 3從commit: A.txt延伸出來，commit: A add 2和commit: A add 3上面沒有branch標籤，此時他們沒有被任何分支追蹤。再來，我們用git checkout master切換到master主支，log flow馬上就更新成右圖，可以發現2個commit: A add 2和A add 3都不見了，因為沒有branch可以追蹤的到它們。

<figure class="half">
	<img src="/images/git1/06.png" alt="tag">
	<figcaption></figcaption>
	<img src="/images/git1/07.png" alt="tag">
	<figcaption></figcaption>
</figure>

但別緊張！ 輸入git checkout master指令後，我們可以看到git給我們的提示。它引導我們幫之前所做的紀錄建立一條新支線。

<figure>
	<img src="/images/git1/08.png" alt="tag">
	<figcaption></figcaption>
</figure>

輸入git branch **myTest**(分支名) **9063ebd**(切換分支前最新的commit id)後，我們可以看到下圖，新的分支myTest保存了之前的commit紀錄。

<figure>
	<img src="/images/git1/09.png" alt="tag">
	<figcaption></figcaption>
</figure>

### Reset
和git checkout不同，git reset不只工作目錄，連都會回到之前的commit版本。如下圖，左邊是git reset前，右邊是git reset後，我們已經看不到master branch上的A.txt紀錄了。這指令跟上一頁的感覺很像，圖上的綠箭頭是目前的工作目錄的位置。

<figure class="half">
	<img src="/images/git1/10.png" alt="tag">
	<figcaption></figcaption>
	<img src="/images/git1/11.png" alt="tag">
	<figcaption></figcaption>
</figure>

* 你可能有發現，只有master branch退後到前一個commit，其他branch都沒動。因為我們現在所在的branch為master，所以我們只能reset **master分支**。換言之，我們不能身在master，卻去reset myTest分支的commit。

下面介紹三種不同的reset方式，其中比較常用的是--mixed和--hard。

* git reset --mixed  {commit id}  #HEAD指到{commit id}，取消{commit id}之後所有commit的追蹤，但檔案變更仍保留(working tree沒改變)。當想要取消之前的幾次commit紀錄，又想保留之前所做的所有變更，就可以用這個指令。和取消上次存檔的感覺很像。
* git reset --hard {commit id}  #包含HEAD和檔案全部回復到{commit id}，和回到上次存擋狀態很像。
* git reset --soft {commit id}  #是git reset –-mixed {commit id}後，又做了一次git add 

### Fetch
我們用git remote add {遠端資料庫名} http://.........git 來設定遠端儲存庫(可設定多個)，並利用git fetch {遠端資料庫名} {遠端分支名} 在本地端建立名為 {遠端資料庫名}/{遠端分支名稱} 的遠端追蹤分支，同步遠端伺服器上的資料到本地。如果想刪除本地端的遠端追蹤分支，可以使用 **git push {遠端分支名稱} :{遠端分支名稱} **指令，或直接用 git remote rm {遠端資料庫名} 刪除儲存庫紀錄，此時遠端追蹤分支也會跟著一起被刪除。

<figure>
	<img src="/images/git1/12.png" alt="tag">
	<figcaption></figcaption>
</figure>

經由上面的指令，我們可以看到原來左圖的log flow變成右圖的log flow，多增加了同步後的origin1/master分支在A.txt版本。

<figure class="half">
	<img src="/images/git1/13.png" alt="tag">
	<figcaption></figcaption
	<img src="/images/git1/14.png" alt="tag">
	<figcaption></figcaption>
</figure>

使用這個指令，我們可以得到和遠端伺服器一樣的版本資料。本地的遠端追蹤分支有個特點，就是不能在上面做commit，要commit請在本地端相對應名稱的分支做commit，因為遠端追蹤分支如其名，專門用來追蹤遠端伺服器的版本。

### Merge
本指令會將其他分支的最新版本合併到所在分支，其中需要注意的就是fast-forward和non fast-forward的差別，這一篇<a href="https://ihower.tw/blog/archives/2620">Git教學文章</a>寫得很清楚，在此截取其中一段:

> fast-forward 在 Git 是一種 merge 術語，當 B branch (例如一個 local branch) 是從 A branch (例如一個 remote branch) 的最新版(HEAD)分支出來的，那當 A 要把 B merge 進來時，因為 B 的 parent commit 是 A 的 HEAD，所以這兩個 branch 唯一的差異就是 B 後來的 commit 而已，而不會有任何 conflict。所以實際上的動作只要把 A 的 HEAD 改成 B 的 HEAD 就好了，線圖上這兩個 branch 根本是同一條線，此謂 fast-forward。

以下實際用log flow做演示。首先是初始狀態，我們可以看到myTest分支的基點是master分支的最新版。

<figure>
	<img src="/images/git1/15.png" alt="tag">
	<figcaption></figcaption>
</figure>

再來我們在master主支合併myTest分支回master：

* 左圖使用fast-forward指令(git預設): git merge --ff myTest，我們可以發現log flow沒有小耳朵，直接連成一條直線，myTest和master的HEAD都來到最新版本。當我們在合併遠端追蹤分支到本地分支時，我們會希望同一人開發就同一條branch就好，不要因為這個單純的同步，讓分支到處分岔出去添亂。故用這個指令能讓log flow比較清楚易懂。
* 左圖使用no fast-forward指令: git merge --no-ff myTest，這個指令在開發者把“別人”的分支合併回master時常用。為了追蹤方便，我們習慣把分岔出去的小耳朵，認為是別人開發的分支，這樣會比較方便追蹤。

<figure class="half">
	<img src="/images/git1/16.png" alt="tag">
	<figcaption></figcaption>
	<img src="/images/git1/17.png" alt="tag">
	<figcaption></figcaption>
</figure>

#### 工作習慣整理:
> 1. 我們習慣把分岔出去的小耳朵，認為是別人開發的分支合併進來的結果
> 2. 我們習慣將自己開發的commit保持在同一條branch上
> 3. fast-forward無小耳朵； no fast-forward有小耳朵

### Pull
簡單說，Pull = Fetch + Merge，pull預設的merge就是fast-forward，我們可以使用git pull --no-ff指令，指定no fast-forward。如果在pull時遇到衝突，可以使用git reset --merge回復到merge前的狀態(也就是只執行到fetch)，查看衝突再merge。

* 我們可以將pull預設改用 fast-forward only： git config --local pull.ff only，好處是當我們無法pull時，馬上就知道遠端伺服器的版本被更動了！此時用git pull --ff 就能解決。

### Rebase
git rebase是重新定義基底點的意思，其說明在這篇<a href="https://blog.yorkxin.org/posts/2011/07/29/git-rebase/">Git-rebase小筆記</a>說得很清楚。他的感覺就像把樹枝拔下來重新插在其他分支，如下面左圖，執行以下指令：

1. git checkout myTest  #切換到myTest分支才能更改myTest的基點
2. git rebase master #把myTest分支的基點接到master的HEAD

就會產生右圖的log flow，更改的只有myTest分支，master分支沒有任何變更。

<figure class="half">
	<img src="/images/git1/18.png" alt="tag">
	<figcaption></figcaption>
	<img src="/images/git1/19.png" alt="tag">
	<figcaption></figcaption>
</figure>

### Cherry-Pick & Revert
兩者的相同點在於，會在做完變更後commit留下紀錄，不同點在於前者是“撿”幾個版本的變更到所在分支並重新commit，後者是取消幾個版本的變更重新commit。這篇<a href="http://ithelp.ithome.com.tw/question/10139368">git cherry-pick</a>寫得非常清楚，下面這條很重要，擷取給大家看。

#### 使用git cherry-pick＆git revert的注意事項
> 「工作目錄」必須是乾淨，工作目錄下的「索引」不能有任何準備要 commit 的檔案 (staged files) 在裡面，否則將會無法執行。

#### (1) Cherry-Pick展示：
見左圖，我們可以看到2條分支，一支是master，另一支為test。我們現在要將test分支的2個commits(被光標的2個)像撿櫻桃那樣，撿到master分支。我輸入的操作如下:

1. git checkout master
2. git cherry-pick {commit id: add test}
3. git cherry-pick {commit id: add test2}

我們可以看到log flow變成右圖，test上的2個commits被原封不動的搬到master上，成master的新版本。

<figure class="half">
	<img src="/images/git1/20.png" alt="tag">
	<figcaption></figcaption>
	<img src="/images/git1/21.png" alt="tag">
	<figcaption></figcaption>
</figure>

沒有發生衝突沒事，一發生衝突時，使用怎麼樣得cherry-pick操作就很重要。我們看到下圖SmartGit的Cherry-pick操作，有兩個選擇：cherry-pick & commit 跟 cherry-pick。

含commit的選象為cherry-pick指令預設，我們處理完衝突後，要用git cherry-pick --continue留下版本紀錄，才算完成整個cherry-pick流程。如果覺得衝突太多想取消這次cherry-pick，可以輸入git cherry-pick --abort放棄這次cherry-pick；後者的指令有加上--no-commit，其只有把所選的版本變更合併進所在分支後，就完成cherry-pick了，沒有督促我們留下任何commit紀錄。我們能使用這個指令，合併完變更版本後再做一些客製化修改，再commit。

<figure>
	<img src="/images/git1/22.png" alt="tag">
	<figcaption></figcaption>
</figure>

執行git cherry-pick {commit id} 發生衝突時，git會提醒我們做修正，如下。此時cherry-pick流程還沒有結束，為了保險，我們可以用git status查看是否還在cherry-pick process。

<figure>
	<img src="/images/git1/25.png" alt="tag">
	<figcaption></figcaption>
</figure>

衝突發生時的解決方法，可以操考<a href="https://wiki.koha-community.org/wiki/Using_Git_Cherry_Pick">這篇的Resolve conflicts</a>，cherry-pick不會告訴我們哪些檔案有conflict，但我們可以用 **git status** 查閱到，如下圖。

<figure>
	<img src="/images/git1/24.png" alt="tag">
	<figcaption></figcaption>
</figure>

解決完衝突後，別忘記git cherry-pick --continue，完成cherry-pick的程序。否則一不小心忘記而跳到別的branch，之前所做的修正可能會都不見。

#### 小知識:
> cherry-pick會把之前的版本原封不動的搬到所在分支上，所以cherry-pick過來的版本的時間和作者等...都和原本的commit一模一樣，這樣讓人方便追蹤是誰何時做的版本變更。

<figure>
	<img src="/images/git1/23.png" alt="tag">
	<figcaption></figcaption>
</figure>

#### (2) Revert展示：
首先，看到下圖的log flow。我們希望“回復“之前的commit: fix所做的變更。

<figure>
	<img src="/images/git1/26.png" alt="tag">
	<figcaption></figcaption>
</figure>

commit: fix變更了test.txt的內容，修正了衝突。我們希望將test.txt回復到修正衝突之前。

<figure>
	<img src="/images/git1/27.png" alt="tag">
	<figcaption></figcaption>
</figure>

如果沒有遇到衝突，就能看到log flow變成下圖。 git revert會恢復指定commit的變更，並下新的commit紀錄此次恢復。我們可以在指令加上--no-commit，讓revert執行後不要馬上系統自動commit，讓我們有空間做些許調整後再自己手動commit。

<figure>
	<img src="/images/git1/28.png" alt="tag">
	<figcaption></figcaption>
</figure>

revert遇到衝突時的應對方式和cherry-pick一模一樣，通常會在我們試圖恢復老舊的變更時發生，參照下圖。遇到衝突時，多用** git status **查看是好用的。

<figure>
	<img src="/images/git1/29.png" alt="tag">
	<figcaption></figcaption>
</figure>

### 結語
git的觀念雖然比較難具象化，但一懂一輩子受用無窮，可說是最棒的版本追蹤+備份工具。下篇將解說如何用git做協同合作，期待再見XD







