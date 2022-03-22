# Git-Tutorials 基本使用教學 📝

因為小弟覺得這東西蠻有趣的，所以就簡單寫個教學文，順便記錄一下📝，希望能幫助想學的人😄

如果教學有誤再請糾正😅

基本使用指令以及安裝可參考小弟之前拍的影片 [github基本教學 - 從無到有](https://www.youtube.com/watch?v=py3n6gF5Y00)

影片教學包含如何產生 **SSH key**

如果步驟正確且沒出錯誤，可以在路徑下找到 **.ssh資料夾**，裡面有 **id_rsa** 以及 **id_rsa.pub** 兩個檔案，

這兩個就是 SSH Key， **id_rsa是私鑰** ，不能洩露出去， **id_rsa.pub是公鑰** ，可以很放心的告訴任何人。

安裝完 Git 之後，要做的第一件事情就是去設定自己的名字和信箱

```
git config --global user.name "twtrubiks"
git config --global user.email "twtrubiks@gmail.com"
```

可以輸入以下來確認是否輸入成功

```
git config --global user.name
git config --global user.email
```

[![alt tag](https://camo.githubusercontent.com/46ec7287a9e1a720568d6b8add1445a36107ee6ea2fa284b60cb9ec2bddccca0/68747470733a2f2f692e696d6775722e636f6d2f356d705337496a2e6a7067)](https://camo.githubusercontent.com/46ec7287a9e1a720568d6b8add1445a36107ee6ea2fa284b60cb9ec2bddccca0/68747470733a2f2f692e696d6775722e636f6d2f356d705337496a2e6a7067)

Git 設定資料查看，可執行以下指令 ( 文章末會有較詳細的教學 )：

```
git config --list
```

## git init 指令

初始化 git

```
git init
```

也可以指定資料夾

```
git init <directory>
```

## git clone 指令

複製如圖位置網址 ( 不要複製我的哦~ 複製你自己的 ) [![alt tag](https://camo.githubusercontent.com/4c4107bfeee61e4768e627fbf71a49b6ba03957626536f7dd6ba87d7f04da324/68747470733a2f2f692e696d6775722e636f6d2f454a354a4e6a742e6a7067)](https://camo.githubusercontent.com/4c4107bfeee61e4768e627fbf71a49b6ba03957626536f7dd6ba87d7f04da324/68747470733a2f2f692e696d6775722e636f6d2f454a354a4e6a742e6a7067)

git clone ( 複製的網址 ) SSH / HTTPS

```
git clone git@github.com:twtrubiks/test.git
```

第一次會出現 SSH 警告，選 YES 即可。

如圖 ( 下載成功 )，在你的下載路徑下就會多出一個資料夾 [![alt tag](https://camo.githubusercontent.com/0f9fdcf2fef7a0d6ccd72eb4b4fc1aa6eaac45deb3989a2a52675b78ac9d777f/68747470733a2f2f692e696d6775722e636f6d2f69496b546c71662e6a7067)](https://camo.githubusercontent.com/0f9fdcf2fef7a0d6ccd72eb4b4fc1aa6eaac45deb3989a2a52675b78ac9d777f/68747470733a2f2f692e696d6775722e636f6d2f69496b546c71662e6a7067)

### 如何改善(加速)大型 repo git clone 速度

- [Youtube Tutorial - 如何改善(加速)大型 repo git clone 速度](https://youtu.be/YHX0qkQa1UI)

有時候我們會需要 clone 很大的 repo，執行 `git clone` 都需要很長的時間，是不是有方法可以

加速 clone 的速度呢 ❓

直接開始動手嘗試 ( 使用 [django](https://github.com/django/django) 當範例 )，

```
git clone git@github.com:django/django.git
```

( 你會發現 clone 需要一些時間 😤)

[![alt tag](https://camo.githubusercontent.com/4ff0c62e06f55aa1a610cc03ed06b46e10109555660bdd9ec61307007bf6fcda/68747470733a2f2f692e696d6775722e636f6d2f794d48364c38462e706e67)](https://camo.githubusercontent.com/4ff0c62e06f55aa1a610cc03ed06b46e10109555660bdd9ec61307007bf6fcda/68747470733a2f2f692e696d6775722e636f6d2f794d48364c38462e706e67)

接著查看 log，`git log`

[![alt tag](https://camo.githubusercontent.com/ebc48797eb0a477313e66d278f7c3ee2a0593dbd45e18468c3c876eddeb86ad0/68747470733a2f2f692e696d6775722e636f6d2f764a6b465472322e706e67)](https://camo.githubusercontent.com/ebc48797eb0a477313e66d278f7c3ee2a0593dbd45e18468c3c876eddeb86ad0/68747470733a2f2f692e696d6775722e636f6d2f764a6b465472322e706e67)

嘗試切換 branch `git checkout stable/2.2.x`

[![alt tag](https://camo.githubusercontent.com/6fc44643c98f53e52f8a7e558853a6b36333cde3ebbaffcf8b795a940695fe1d/68747470733a2f2f692e696d6775722e636f6d2f5574784a3245522e706e67)](https://camo.githubusercontent.com/6fc44643c98f53e52f8a7e558853a6b36333cde3ebbaffcf8b795a940695fe1d/68747470733a2f2f692e696d6775722e636f6d2f5574784a3245522e706e67)

開始改善(加速) clone 的時間，

可以透過 `--depth` 這個參數來完成，簡單說明一下他的功能，當我們一般執行 clone 之後，

接著執行 `git log` 你會發現有大量的 log，在某修情況下，你可能不需要那麼多的 log，

也就是說你可能只需要最近 10 筆的 history commit，甚至你只需要 1 筆 ( 也就是根本不需要

history commit )，這時候就很適合使用 `--depth`。

```
git clone git@github.com:django/django.git --depth 1
```

( 你會發現這次快很多了 )

[![alt tag](https://camo.githubusercontent.com/8faa8e5ef4ba51503f05463979fd1ecb29cfb3064db9e0d2e828bd38f75492a3/68747470733a2f2f692e696d6775722e636f6d2f79766b5a555a492e706e67)](https://camo.githubusercontent.com/8faa8e5ef4ba51503f05463979fd1ecb29cfb3064db9e0d2e828bd38f75492a3/68747470733a2f2f692e696d6775722e636f6d2f79766b5a555a492e706e67)

接著查看 log，`git log`

( 會變快的原因是因為我們只保留最新的一筆 history commit ，

如果你需要最近 10 筆，改成 --depth 10 即可 )

[![alt tag](https://camo.githubusercontent.com/d28a5c0ec0b38bb674cb91a75fc02154bc61a8521247cd56663e1c90cb14095c/68747470733a2f2f692e696d6775722e636f6d2f6174395a7a71332e706e67)](https://camo.githubusercontent.com/d28a5c0ec0b38bb674cb91a75fc02154bc61a8521247cd56663e1c90cb14095c/68747470733a2f2f692e696d6775722e636f6d2f6174395a7a71332e706e67)

但是會有一個問題，當嘗試切換 branch `git checkout stable/2.2.x`

( 你會發現你無法切換 remote branch 😱

原因是因為使用 `--depth` 相當於是 `--single-branch`，

所以當然沒有其他的 branch。 )

[![alt tag](https://camo.githubusercontent.com/319d03190925211d092588117132491454d98dd0d305ce72dd9b1efb47f672b1/68747470733a2f2f692e696d6775722e636f6d2f674461657131572e706e67)](https://camo.githubusercontent.com/319d03190925211d092588117132491454d98dd0d305ce72dd9b1efb47f672b1/68747470733a2f2f692e696d6775722e636f6d2f674461657131572e706e67)

也就是說以下兩條指令其實是相等的

```
git clone git@github.com:django/django.git --depth 1
git clone git@github.com:django/django.git --depth 1 --single-branch
```

為了解決這個問題，比較好的做好應該是這樣

```
git clone git@github.com:django/django.git --depth 1 --no-single-branch
```

( 這個和 `--single-branch` 比會稍微久一點點，因為每個 branch 的最新一個 history commit 都要 clone 下來 )

這樣的話，就可以保留 remote 的 branch 了，

[![alt tag](https://camo.githubusercontent.com/9b0a93f5462b03dde309ba8e3b8d80dbe3b26fea7e25cabb39bc8072783285fa/68747470733a2f2f692e696d6775722e636f6d2f426b4c4b565a7a2e706e67)](https://camo.githubusercontent.com/9b0a93f5462b03dde309ba8e3b8d80dbe3b26fea7e25cabb39bc8072783285fa/68747470733a2f2f692e696d6775722e636f6d2f426b4c4b565a7a2e706e67)

成功切換 remote 的 branch， `git checkout stable/2.2.x`。

[![alt tag](https://camo.githubusercontent.com/f45d18247f2b872a8761e1f907bc22a50cf9013ec7e12f8228be10c8707b895f/68747470733a2f2f692e696d6775722e636f6d2f564376635354722e706e67)](https://camo.githubusercontent.com/f45d18247f2b872a8761e1f907bc22a50cf9013ec7e12f8228be10c8707b895f/68747470733a2f2f692e696d6775722e636f6d2f564376635354722e706e67)

最後稍微整理，

如要 clone 最近一次的 history，而且也需要其他 branch，使用如下，

```
git clone git@github.com:django/django.git --depth 1 --no-single-branch
```

如果你想要指定分支, 加上 `-b`,

```
git clone git@github.com:django/django.git --depth 1 --no-single-branch -b stable/3.1.x
```

如要 clone 最近一次的 history，而且**不需要**其他 branch，使用如下，

```
git clone git@github.com:django/django.git --depth 1 --single-branch
```

or

```
git clone git@github.com:django/django.git --depth 1
```

更多詳細參數說明請參考 [git clone](https://git-scm.com/docs/git-clone)

## git status 指令

```
git status
```

可以讓我們觀看目前的 repository ( repo 容器 )。

[![alt tag](https://camo.githubusercontent.com/306e90a8d5fe0b9bc0b519ac593529a206f65ddefa097dec14f21ec450b9d7f2/68747470733a2f2f692e696d6775722e636f6d2f354774393856682e6a7067)](https://camo.githubusercontent.com/306e90a8d5fe0b9bc0b519ac593529a206f65ddefa097dec14f21ec450b9d7f2/68747470733a2f2f692e696d6775722e636f6d2f354774393856682e6a7067)

意思是目前你的工作區是乾淨的。

## 工作區與暫存區 ( Stage )

git add 意思是把要送出的文件放到暫存區 ( Stage ) ，

然後執行

git commit 就可以把暫存區 ( Stage ) 裡所有修改的內容送到目前的分支上。

一旦送出 ( git commit ) 後，如果你又沒有對工作區做任何修改，那麼工作區就是"乾淨"的。

git commit -m "xxxxx" 指令，-m 後面輸入的內容是本次修改 ( 送出 ) 的說明，

盡量輸入一眼就可以看出這次送出修改了什麼的內容 ( 方便以後回去觀看能快速了解此次 commit 修改了什麼 )。

以下 demo 為在一個資料夾內新增一個 Hello.py 檔案

然後使用 git status 觀看目前的 repository ( repo 容器 )，你會看到 Hello.py 未被追蹤，如下圖

[![alt tag](https://camo.githubusercontent.com/1627b1f4e4b36842d484757235fe0df5412cfb0371917dda8497f6d7a88613f8/68747470733a2f2f692e696d6775722e636f6d2f64766a314451682e6a7067)](https://camo.githubusercontent.com/1627b1f4e4b36842d484757235fe0df5412cfb0371917dda8497f6d7a88613f8/68747470733a2f2f692e696d6775722e636f6d2f64766a314451682e6a7067)

可以使用如下指令

```
git add Hello.py
```

額外補充，下面這個指令很有趣，大家可以玩玩看

```
git add -p
```

接著再使用

git commit -m "文字"

```
git commit -m "add Hello.py"
```

再使用 git status，你會發現工作區變乾淨了。如下圖

[![alt tag](https://camo.githubusercontent.com/92b404de0f3917128bedb80f713c128c1ea910e5288ac95b2922033942f7552f/68747470733a2f2f692e696d6775722e636f6d2f36567269654e622e6a7067)](https://camo.githubusercontent.com/92b404de0f3917128bedb80f713c128c1ea910e5288ac95b2922033942f7552f/68747470733a2f2f692e696d6775722e636f6d2f36567269654e622e6a7067)

補充，如果只有輸入

```
git commit
```

[![alt tag](https://camo.githubusercontent.com/64e6fd94cea3b5d370686cbd776aa1f23b5391855ddaa33a3db0d9f6e7b3cdba/68747470733a2f2f692e696d6775722e636f6d2f795a784b4754552e6a7067)](https://camo.githubusercontent.com/64e6fd94cea3b5d370686cbd776aa1f23b5391855ddaa33a3db0d9f6e7b3cdba/68747470733a2f2f692e696d6775722e636f6d2f795a784b4754552e6a7067)

這時會跳出編輯視窗

[![alt tag](https://camo.githubusercontent.com/ff313b6ba2ca41e97bfafc2370d097c9abf35f87604dd6323db3265a514af60e/68747470733a2f2f692e696d6775722e636f6d2f68744e5130644a2e6a7067)](https://camo.githubusercontent.com/ff313b6ba2ca41e97bfafc2370d097c9abf35f87604dd6323db3265a514af60e/68747470733a2f2f692e696d6775722e636f6d2f68744e5130644a2e6a7067)

這時可以按鍵盤的 **Ins鍵** ( 或按鍵盤上的 **英文字 i** ) 即可輸入文字

[![alt tag](https://camo.githubusercontent.com/613a3bd3c24cf486835a7c870cb41819e2f6092f063c9a5ff82b0d6dea457283/68747470733a2f2f692e696d6775722e636f6d2f4e4679313664702e6a7067)](https://camo.githubusercontent.com/613a3bd3c24cf486835a7c870cb41819e2f6092f063c9a5ff82b0d6dea457283/68747470733a2f2f692e696d6775722e636f6d2f4e4679313664702e6a7067)

輸入完先按 **Esc鍵** ，按完後底下的 INSERT 會消失，接著直接打 **:wq** ，再按 enter 就會儲存並離開了。

更多參數可參考 https://git-scm.com/docs/git-commit 說明。

**如何修改最後一次的commit呢 ?**

有時候我們 commit 完之後，才發現自己的 commit 內容手殘打錯了

這時候可以使用如下指令，他會跳出編輯視窗給你編輯你上一次的 commit 內容。

```
git commit --amend
```

又或是我們 commit 完之後，才發現自己漏了幾個檔案沒有 add 進去

這時候可以使用如下指令

```
git commit -m "init commit"
git add missing_file.py
git commit --amend
```

如上狀況為當我 git commit -m "init commit" 之後，

我發現我漏掉了 **missing_file.py** 這個檔案 ( commit 前忘記 add 進去 ) ，

這時候就可以使用 git commit --amend 來修改最後一次的 commit 。

有時候我們會為了方便，直接使用下面的指令一次加入全部的檔案

```
git add .
```

但是加完後發現其實有些檔案是不需要 add 進入的，這時候就可以使用如下指令去取消 add

```
git reset HEAD <file>
```

範例，路徑下有 A.py 以及 B.py 這兩個檔案，然後我使用 **git add .** 加入， [![alt tag](https://camo.githubusercontent.com/b6b9c1065a27cd9d2d42f58d54289c8cb359fde49a0ddca8e61b842362f5b463/68747470733a2f2f692e696d6775722e636f6d2f305337546345422e6a7067)](https://camo.githubusercontent.com/b6b9c1065a27cd9d2d42f58d54289c8cb359fde49a0ddca8e61b842362f5b463/68747470733a2f2f692e696d6775722e636f6d2f305337546345422e6a7067)

但加入完我發現其實 B.py 我還沒有要 add 進入，所以我這時候就可以使用 **git reset HEAD B.py** 去還原。

[![alt tag](https://camo.githubusercontent.com/2759678cf89f7aec5e4424e1e5cb2b28d674d18ccf87672dc3e88c25a79bd719/68747470733a2f2f692e696d6775722e636f6d2f336941794545782e6a7067)](https://camo.githubusercontent.com/2759678cf89f7aec5e4424e1e5cb2b28d674d18ccf87672dc3e88c25a79bd719/68747470733a2f2f692e696d6775722e636f6d2f336941794545782e6a7067)

## git push 指令

```
git push
```

將程式 push 到 github ( or bitbucket 之類 )上 , 如下圖

[![alt tag](https://camo.githubusercontent.com/f7ffadb68e7be359847db0f873fd65123488656600b659dce4554a5f227a9c1c/68747470733a2f2f692e696d6775722e636f6d2f643631506175362e6a7067)](https://camo.githubusercontent.com/f7ffadb68e7be359847db0f873fd65123488656600b659dce4554a5f227a9c1c/68747470733a2f2f692e696d6775722e636f6d2f643631506175362e6a7067)

## 版本控制 - 歷史記錄

```
git log
```

按 **小寫q** 可退出

[![alt tag](https://camo.githubusercontent.com/31d35eeeada0bb9f1b6e659d84abcc717b056d39642175f78e4ee16c6ce287e7/68747470733a2f2f692e696d6775722e636f6d2f6a3131616643502e6a7067)](https://camo.githubusercontent.com/31d35eeeada0bb9f1b6e659d84abcc717b056d39642175f78e4ee16c6ce287e7/68747470733a2f2f692e696d6775722e636f6d2f6a3131616643502e6a7067)

如果覺得版面太雜，可以使用下列指令

```
git log --pretty=oneline
```

按 **小寫q** 可退出

[![alt tag](https://camo.githubusercontent.com/8d026dddc3c8819d404eeb6f01e0e31ddb99979764412f2fa0016c7c8bd597d3/68747470733a2f2f692e696d6775722e636f6d2f6a7a32637755412e6a7067)](https://camo.githubusercontent.com/8d026dddc3c8819d404eeb6f01e0e31ddb99979764412f2fa0016c7c8bd597d3/68747470733a2f2f692e696d6775722e636f6d2f6a7a32637755412e6a7067)

另外底下也是一個看 log 的方式（ 很酷 😆），有 GUI 的感覺（ 來源為文章最後的連結 ）

```
git log --graph --pretty=format:"%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset" --abbrev-commit --date=relative
```

[![alt tag](https://camo.githubusercontent.com/3386171b4a783d8068f3b34501fa8f762b5a841a64466d2823fc04faaa032468/68747470733a2f2f692e696d6775722e636f6d2f584e51697375662e706e67)](https://camo.githubusercontent.com/3386171b4a783d8068f3b34501fa8f762b5a841a64466d2823fc04faaa032468/68747470733a2f2f692e696d6775722e636f6d2f584e51697375662e706e67)

Git 中，使用 HEAD 表示目前的版本，

```
git reset --hard HEAD
```

[![alt tag](https://camo.githubusercontent.com/bc7beb2dc290ebb97e570fead3df12d4e97af5f5ce665ff52765863bc75499e9/68747470733a2f2f692e696d6775722e636f6d2f706b464f38706b2e6a7067)](https://camo.githubusercontent.com/bc7beb2dc290ebb97e570fead3df12d4e97af5f5ce665ff52765863bc75499e9/68747470733a2f2f692e696d6775722e636f6d2f706b464f38706b2e6a7067)

如果現在要把目前版本退回到上一個版本，就可以使用 git reset 指令：

上一個版本就是HEAD~1，

```
git reset --hard HEAD~1
```

[![alt tag](https://camo.githubusercontent.com/38ab354ada40f7160f9d217cab9e82afa3056d5f4be84f0e30ac4808129f2cf1/68747470733a2f2f692e696d6775722e636f6d2f5a54686f6155542e6a7067)](https://camo.githubusercontent.com/38ab354ada40f7160f9d217cab9e82afa3056d5f4be84f0e30ac4808129f2cf1/68747470733a2f2f692e696d6775722e636f6d2f5a54686f6155542e6a7067)

上上一個版本就是HEAD~2，

如果要指定回到某個特定版本：

[![alt tag](https://camo.githubusercontent.com/a67890cf3c361404f73a4bf824c6e806f0c111ef9d8d28b3bd257cceeaef39e9/68747470733a2f2f692e696d6775722e636f6d2f4b72434f4337312e6a7067)](https://camo.githubusercontent.com/a67890cf3c361404f73a4bf824c6e806f0c111ef9d8d28b3bd257cceeaef39e9/68747470733a2f2f692e696d6775722e636f6d2f4b72434f4337312e6a7067)

```
git reset --hard ad41df36b7
```

`--hard` 這個參數，有三種選擇，分別為 `--mixed`( default ）`--hard` `--soft`，

`--hard` 這個參數簡單解釋就是將之前的 commit 都丟掉（ 完全 **不保留** ）。

`--soft` 這個參數簡單解釋就是將之前的 commit 都丟掉，但 **保留** 你之前工作區的狀態。

`--hard` 和 `--soft` 這兩個我覺得用文字不好說明，我建議大家自己可以動手玩玩看，就可以了解他們之間的差異。

`--soft` 很適合使用在將多個無意義的 commit 合併成一個 commit。

[![alt tag](https://camo.githubusercontent.com/fdd6ba2aadbdc0f696a359d14f476e6685772f9d3ca3d32f6b8d0403208c5e4a/68747470733a2f2f692e696d6775722e636f6d2f3652567574694b2e6a7067)](https://camo.githubusercontent.com/fdd6ba2aadbdc0f696a359d14f476e6685772f9d3ca3d32f6b8d0403208c5e4a/68747470733a2f2f692e696d6775722e636f6d2f3652567574694b2e6a7067)

版本號 ( ad41df36b7 ) 沒必要全部都寫，寫前幾位就可以了，Git 會自動去找。

當你退回到某個版本，突然隔天後悔了，想恢復到之前的新版本該怎麼做呢?

找不到新版本的 commit id 該怎麼辦呢?

這時候就可以使用一個指令

```
git reflog
```

[![alt tag](https://camo.githubusercontent.com/425d43c8e0b1c6e7e7ebe3782ff3cfe96766e22677fc278aa45846f1d2b933ea/68747470733a2f2f692e696d6775722e636f6d2f4d61526c5a5a722e6a7067)](https://camo.githubusercontent.com/425d43c8e0b1c6e7e7ebe3782ff3cfe96766e22677fc278aa45846f1d2b933ea/68747470733a2f2f692e696d6775722e636f6d2f4d61526c5a5a722e6a7067)

接著看你要回到哪個版本，再使用 git reset 即可。

```
git reset --hard 642e7af
```

有時候想消除( 覆蓋 )已經 push 出去的 commit，這時候我們可以使用

```
git push --force
```

或是更簡短的寫法

```
git push -f
```

可以強制 push。先回到某個版本，然後再強制 push。

***注意！在多人專案共同開發時，盡量不要用 --force 這種方法，因為有時候會害到別人，建議可以使用 revert 。***

因為上面這個原因，所以建議用另一種比較安全的方式

```
git push --force-with-lease
```

可以確保你沒有隨便丟掉別人的 commit。（ 如果有人比你早 commit push 上去，你就會無法 push 到 remote ）

## checkout

也請參考 [git switch](https://github.com/twtrubiks/Git-Tutorials#git-switch) 和 [git restore](https://github.com/twtrubiks/Git-Tutorials#git-restore).

`git checkout -- file` 可以丟棄工作區的修改：

```
git checkout  -- hello.py
```

命令 git checkout -- hello.py 意思就是，把 hello.py 文件在工作區的修改全部撤銷 ( 丟棄 ) ，

讓這個檔案回到最近一次 git commit 或 git add 時的狀態。

[![alt tag](https://camo.githubusercontent.com/28d85fcb798c0ee36f974d8fcfcdc5f5985d04cbca0486bb8df3f476f689ee6b/68747470733a2f2f692e696d6775722e636f6d2f5372436f346b482e6a7067)](https://camo.githubusercontent.com/28d85fcb798c0ee36f974d8fcfcdc5f5985d04cbca0486bb8df3f476f689ee6b/68747470733a2f2f692e696d6775722e636f6d2f5372436f346b482e6a7067)

當然也可以用 git reset 指令直接回到某個 commit。

```
git reset --hard xxxxxx
git reset --hard 201f40604ec3b6fa8
```

## 刪除

有兩種狀況，一種是確定要從版本庫中刪除該檔案，那就用命令 git rm 刪掉，並且 git commit：

```
rm hello.py
git rm hello.py
git commit -m "remove hello.py"
```

[![alt tag](https://camo.githubusercontent.com/f499212dd9e54041d52e3d90d2a9addb93be517b5909727f4ecd989f58540b66/68747470733a2f2f692e696d6775722e636f6d2f734c4d544458372e6a7067)](https://camo.githubusercontent.com/f499212dd9e54041d52e3d90d2a9addb93be517b5909727f4ecd989f58540b66/68747470733a2f2f692e696d6775722e636f6d2f734c4d544458372e6a7067)

另一種狀況是刪錯了，使用 git checkout 可以輕鬆還原檔案:

```
rm hello.py
git checkout -- hello.py
```

[![alt tag](https://camo.githubusercontent.com/71dbcff11d4437ad2f06519449cc6a0a33892ddf332290522f50eab265842ba8/68747470733a2f2f692e696d6775722e636f6d2f3558324e6366532e6a7067)](https://camo.githubusercontent.com/71dbcff11d4437ad2f06519449cc6a0a33892ddf332290522f50eab265842ba8/68747470733a2f2f692e696d6775722e636f6d2f3558324e6366532e6a7067)

## 新建與 合併 ( merge ) 分支 branch

在說明分支 branch 之前，先給大家一個觀念。

通常開發的時候，大家都是從 **master** 做一個分支 branch 出去，最後再 **merge** 回 master，

為什麼要這麼做呢 ? 因為要確保大家都是使用最新的 **master**

使用 git branch 指令查看目前的分支：

```
git branch
```

[![alt tag](https://camo.githubusercontent.com/658cc41a1bb6b5fa9f4ad086e96d6fec32fc5a0f691b02d9c97f48cbfdf25fba/68747470733a2f2f692e696d6775722e636f6d2f5356626c5844322e6a7067)](https://camo.githubusercontent.com/658cc41a1bb6b5fa9f4ad086e96d6fec32fc5a0f691b02d9c97f48cbfdf25fba/68747470733a2f2f692e696d6775722e636f6d2f5356626c5844322e6a7067)

首先創建一個分支，bug1 分支 ( 名稱可以隨便取 )，然後切換到 bug1 分支：

```
git branch bug1
git checkout bug1
```

git branch bug1 為創造一個名稱為 bug1 的分支，

git checkout bug1 為切換到一個名稱為 bug1 的分支底下。

[![alt tag](https://camo.githubusercontent.com/6a85ce2c0ebd83d06bac1ddd6e0b6e2d1a4efa46c152df58d43b5593dbdd162b/68747470733a2f2f692e696d6775722e636f6d2f4a744742486b342e6a7067)](https://camo.githubusercontent.com/6a85ce2c0ebd83d06bac1ddd6e0b6e2d1a4efa46c152df58d43b5593dbdd162b/68747470733a2f2f692e696d6775722e636f6d2f4a744742486b342e6a7067)

以上兩行指令，相當於下列一行指令

```
git checkout -b bug1
```

(這邊教大家一個小技巧, 以下這個指令可以快速切換上一個分支, 和 `cd -` 概念一樣❗)

```
git checkout -
```

我們在 bug1 分支上進行任何修改操作，

然後再把工作成果 ( 補充一下，修改任何內容後請記得使用 git add 指令和 git commit 指令 ) 合併到 master 分支上：

```
git checkout master
git merge bug1
```

[![alt tag](https://camo.githubusercontent.com/c77b29a92fae08dbfed86a8c5620b11324a40c010d6dc14a94e1b9e83f1cae6b/68747470733a2f2f692e696d6775722e636f6d2f704634784455452e6a7067)](https://camo.githubusercontent.com/c77b29a92fae08dbfed86a8c5620b11324a40c010d6dc14a94e1b9e83f1cae6b/68747470733a2f2f692e696d6775722e636f6d2f704634784455452e6a7067)

git checkout master 為切換到一個名稱為 master 的分支底下。

git merge bug1 指令用於合併 ( bug1分支 ) 指定分支到目前分支 ( master ) 底下。

如果非常順利， git merge 的訊息裡會出現 Fast-forward，合併速度非常快。

當然不是每次合併都能很順利的出現 Fast-forward，很多時候會出現衝突 CONFLICT 。

如果順利合併 ( merge ) 完成後，就可以刪除 (本機) bug1 分支：

```
git branch -d dev
```

[![alt tag](https://camo.githubusercontent.com/3deebfbbecf8a31b5311189f7f8fe99800a2fe343d488b958b0dc7f3695a1542/68747470733a2f2f692e696d6775722e636f6d2f4c6d4b4b5778522e6a7067)](https://camo.githubusercontent.com/3deebfbbecf8a31b5311189f7f8fe99800a2fe343d488b958b0dc7f3695a1542/68747470733a2f2f692e696d6775722e636f6d2f4c6d4b4b5778522e6a7067)

如果要丟掉一個沒有被合併過的分支，可以使用 git branch -D 分支名稱 強行刪除 (本機)。

```
git branch -D dev
```

那如果今天要刪除 remote 端的 branch 該怎麼辦呢❓

- [Youtube Tutorial - git 刪除查看遠端的分支 branch](https://youtu.be/0JQrT7nfm_c)

```
git push origin --delete {remote_branch}
```

補充，git branch 也可以修改名稱，而且 commit id 是不會改變的，使用方法也很簡單，

可參考 git-branch [文件](https://git-scm.com/docs/git-branch#git-branch--m)，使用方法如下，

```
git branch -m <name>
```

原本的 b1 branch 分支的 log 如下，

[![alt tag](https://camo.githubusercontent.com/da4ac59eacca6aa687d91d21d4725d8a6351adfc5874843778f95a0e69609a5c/68747470733a2f2f692e696d6775722e636f6d2f62314b314555792e706e67)](https://camo.githubusercontent.com/da4ac59eacca6aa687d91d21d4725d8a6351adfc5874843778f95a0e69609a5c/68747470733a2f2f692e696d6775722e636f6d2f62314b314555792e706e67)

現在將 b1 branch 修改成 b2 branch，

[![alt tag](https://camo.githubusercontent.com/1fb06b1e0f50ccee0390e2170326a39559402103f841821c17ec1952153d1d3e/68747470733a2f2f692e696d6775722e636f6d2f54777a356b526d2e706e67)](https://camo.githubusercontent.com/1fb06b1e0f50ccee0390e2170326a39559402103f841821c17ec1952153d1d3e/68747470733a2f2f692e696d6775722e636f6d2f54777a356b526d2e706e67)

如果你仔細和剛剛的 log 比較，你會發現 log 的 commit id 是不會改變的，

[![alt tag](https://camo.githubusercontent.com/113b05f3c2b0c082319ffb9e8debde8c6996c003121739beb60025b41e245f34/68747470733a2f2f692e696d6775722e636f6d2f714d6a7156335a2e706e67)](https://camo.githubusercontent.com/113b05f3c2b0c082319ffb9e8debde8c6996c003121739beb60025b41e245f34/68747470733a2f2f692e696d6775722e636f6d2f714d6a7156335a2e706e67)

## 使用特定 commit id 建立 branch

有時候我們會想測試某個 commit 的狀態, 這時候可以直接利用 commit id 去建立一個 branch,

方法如下,

```
git checkout -b new_branch <commit id>
```

這樣就會依照你指定的 commit id 去建立出一個 branch.

## 新建分支 branch 並 push

相信大家有時候在 github 上面都會看到，如下圖，很多分支

[![alt tag](https://camo.githubusercontent.com/7fd127830140e91567cf934bc3936f06192c4598e3d8aa870505fef91a6a5f16/68747470733a2f2f692e696d6775722e636f6d2f777249646c7a532e6a7067)](https://camo.githubusercontent.com/7fd127830140e91567cf934bc3936f06192c4598e3d8aa870505fef91a6a5f16/68747470733a2f2f692e696d6775722e636f6d2f777249646c7a532e6a7067)

那我們要如何建立分支呢? 首先，我們先看下面這張圖

[![alt tag](https://camo.githubusercontent.com/51f387a6902c4828098ec24fcffb3e9008720751004e62c508622a97d45724f8/68747470733a2f2f692e696d6775722e636f6d2f335530393261312e6a7067)](https://camo.githubusercontent.com/51f387a6902c4828098ec24fcffb3e9008720751004e62c508622a97d45724f8/68747470733a2f2f692e696d6775722e636f6d2f335530393261312e6a7067)

有一個 v1 的分支，並且我在分支上增加一個 g.py 並且 commit。

接下來要 **第一次** git push 的時候， 你會發現有錯誤提示

請使用以下指令才是正確的

```
git push --set-upstream origin v1
```

也可以使用

```
git push -u origin v1
```

更多詳細說明可參考 https://git-scm.com/docs/git-push#git-push--u

[![alt tag](https://camo.githubusercontent.com/f082a2d0afca7d76c798df8dc4bf359dad780a8bffe1bdec4af467cced385db3/68747470733a2f2f692e696d6775722e636f6d2f316675533256592e6a7067)](https://camo.githubusercontent.com/f082a2d0afca7d76c798df8dc4bf359dad780a8bffe1bdec4af467cced385db3/68747470733a2f2f692e696d6775722e636f6d2f316675533256592e6a7067)

接下來你可以到網頁上看 ( 這裡用 bitbucket 當作範例 ) ，你會發現有分支 v1 了

[![alt tag](https://camo.githubusercontent.com/1fae09219fa0c12277d91d9659a2378e879c45bcc54735cb4c11fc07146fb8b3/68747470733a2f2f692e696d6775722e636f6d2f6c4f747a736b382e6a7067)](https://camo.githubusercontent.com/1fae09219fa0c12277d91d9659a2378e879c45bcc54735cb4c11fc07146fb8b3/68747470733a2f2f692e696d6775722e636f6d2f6c4f747a736b382e6a7067)

如果是第一次使用 git clone ，你會發現你只有 master 分支 ，

這時候我們先查看遠端還有什麼分支，

```
git branch -r
git branch --remote
```

`--remote` 或 `-r` 都可以.

假設遠端有一個名稱為 develop 的分支，

我們只要 checkout 到該分支底下就可以了

```
git checkout develop
```

## git switch

[Youtube Tutorial - git switch 和 git restore 教學](https://youtu.be/JL_bSOGDR-k)

請先確認目前的 git 版本, 更新方法可參考 [git 更新](https://github.com/twtrubiks/Git-Tutorials#git-更新).

在 git 2.23 版本開始, 增加了 `git switch` 和 `git restore`, 這兩個指令主要是

要更清楚的劃分功能, 主要是來代替 `git checkout`.

你其實可以想成 `git checkout` = `git switch` + `git restore`.

官方文件可參考 [git-switch](https://git-scm.com/docs/git-switch)

```
git switch [<options>] (-c|-C) <new-branch> [<start-point>]
```

切換到一個已經存在的 branch (如果該 branch 不存在則指令無效)

```
git switch <new-branch>
```

建立 new-branch 並且切換到 new-branch 分支

```
git switch -c <new-branch>
-c` `--create
-C` `--force-create
```

依照 commit_id (或前 N 的 commit 點) 建立 new-branch 並且切換到 new-branch 分支

```
git switch -c <new-branch> <commit_id>
git switch -c <new-branch> HEAD~2
```

(這邊教大家一個小技巧, 以下這個指令可以快速切換上一個分支, 和 `cd -` 概念一樣😄)

```
git switch -
```

## git restore

[Youtube Tutorial - git switch 和 git restore 教學](https://youtu.be/JL_bSOGDR-k)

請先確認目前的 git 版本, 更新方法可參考 [git 更新](https://github.com/twtrubiks/Git-Tutorials#git-更新).

在 git 2.23 版本開始, 增加了 `git switch` 和 `git restore`, 這兩個指令主要是

要更清楚的劃分功能, 主要是來代替 `git checkout`.

你其實可以想成 `git checkout` = `git switch` + `git restore`.

官方文件可參考 [git-restore](https://git-scm.com/docs/git-restore)

以下兩個指令是相同的.

```
git checkout <file>
git restore <file>
```

還原目前資料夾全部的檔案

```
git restore .
```

還原目前資料夾底下結尾是 `*.py` 的全部檔案

```
git restore '*.py'
```

如果你的 `git` 版本比較新, 你應該會發現這個指令你以前好像沒看過😄

[![alt tag](https://camo.githubusercontent.com/e371f4083d8a0f5cf6639d6aeee3e833bfe22fceb24e3f6b1f09be175a0290eb/68747470733a2f2f692e696d6775722e636f6d2f4948716656726e2e706e67)](https://camo.githubusercontent.com/e371f4083d8a0f5cf6639d6aeee3e833bfe22fceb24e3f6b1f09be175a0290eb/68747470733a2f2f692e696d6775722e636f6d2f4948716656726e2e706e67)

```
git restore --staged <file>
```

## git pull

通常在開始工作或要 push 之前，會先從遠端抓取分支，

```
git pull
```

如果有衝突，要先解衝突。

這邊補充一下 `-C` 這個參數的意思, 它的意思代表指定 folder 路徑,

有時候我們可能不想先 `cd` 進去資料夾, 再進行 pull, 這時候,

就很適合使用它😄

```
git [-C <path>] pull
```

舉例,

```
cd git_folder
git pull
```

可以直接簡化為

```
git -C git_folder pull
```

## git fetch

可以先簡單想成 **git pull = git fetch + git merge**

我們先來看下面這張圖， **git fetch + git merge**

[![alt tag](https://camo.githubusercontent.com/0b791374c6f2602ac9cd15cd13bb796f11eefbc0050545fabf51c405a128c089/68747470733a2f2f692e696d6775722e636f6d2f434f75574279772e706e67)](https://camo.githubusercontent.com/0b791374c6f2602ac9cd15cd13bb796f11eefbc0050545fabf51c405a128c089/68747470733a2f2f692e696d6775722e636f6d2f434f75574279772e706e67)

再看這張圖 **git pull**

[![alt tag](https://camo.githubusercontent.com/193537a9cc09f17e6ecb5b8a3b6fad0ba981e4b9197db23dfd64c217d5b12766/68747470733a2f2f692e696d6775722e636f6d2f384647754137352e706e67)](https://camo.githubusercontent.com/193537a9cc09f17e6ecb5b8a3b6fad0ba981e4b9197db23dfd64c217d5b12766/68747470733a2f2f692e696d6775722e636f6d2f384647754137352e706e67)

這樣是不是清楚多了!!!

多補充一個參數 `--prune`,

- [Youtube Tutorial - git fetch 指令 prune 參數說明](https://youtu.be/ZMpMv1P1Q1Q)

這個主要的功能是刪除 remote 無效的 branch,

有時候明明已經把遠端的 branch 刪除, 但是你執行 `git branch --remote`,

卻會發現你還看的到那些 branch 的分支 (但明明網頁上的分支已經被移除了😓)

常常會發生在 pull 端(非工作端)的機器 (如果不懂這句話的意思建議看影片說明😄)

這時候就可以同步一下本機和遠端的分支, 使用以下的指令

```
git fetch --prune
```

## git rebase

什麼是 rebase 呢 ? git rebase 就是避免多餘 ( 沒有意義 ) 的 merge !!! 先看看下面兩張圖

補充 :

ck = checkout

br = branch

st = status

cm = commit

可以自行設定。

圖一

[![alt tag](https://camo.githubusercontent.com/8f0fbec99a5ff9decee038959baf96519677b41060c1a5a53da20c4b956a0acf/68747470733a2f2f692e696d6775722e636f6d2f6d57593066324a2e706e67)](https://camo.githubusercontent.com/8f0fbec99a5ff9decee038959baf96519677b41060c1a5a53da20c4b956a0acf/68747470733a2f2f692e696d6775722e636f6d2f6d57593066324a2e706e67)

圖二

[![alt tag](https://camo.githubusercontent.com/86982959d7e9f53df6ce1702c8e02f81a86321062ca373dbbee1c8416ea61f31/68747470733a2f2f692e696d6775722e636f6d2f51565a633550352e706e67)](https://camo.githubusercontent.com/86982959d7e9f53df6ce1702c8e02f81a86321062ca373dbbee1c8416ea61f31/68747470733a2f2f692e696d6775722e636f6d2f51565a633550352e706e67)

圖一 和 圖二 你喜歡看哪種圖 ? 答案很明顯，是 圖一 !!

**rebase** 的目的主要就是盡量讓圖都像 圖一

用講的大家一定霧煞煞，所以我直接實戰給大家看。

先示範 **沒有使用 rebase** 的範例

目前分支

[![alt tag](https://camo.githubusercontent.com/62a778afa731e52f0bf02303f2bb929b2600754f78ab7a6ba4da3e9a42e7f027/68747470733a2f2f692e696d6775722e636f6d2f45306168666e442e706e67)](https://camo.githubusercontent.com/62a778afa731e52f0bf02303f2bb929b2600754f78ab7a6ba4da3e9a42e7f027/68747470733a2f2f692e696d6775722e636f6d2f45306168666e442e706e67)

[![alt tag](https://camo.githubusercontent.com/4dbc38a866c29aaef39547bd30059d481ef24ab217257f3662253b62f666d0f2/68747470733a2f2f692e696d6775722e636f6d2f4c6234644230562e706e67)](https://camo.githubusercontent.com/4dbc38a866c29aaef39547bd30059d481ef24ab217257f3662253b62f666d0f2/68747470733a2f2f692e696d6775722e636f6d2f4c6234644230562e706e67)

以上說明 : 先建立 v1 branch，接著 add 後再 commit。

假設現在又有人 push 了，以下模擬 pull ，自己加上一個 commit

[![alt tag](https://camo.githubusercontent.com/102b3eb362cfb1669373cdcd2b62863cc34485553733434a6a9e3338c4a95bef/68747470733a2f2f692e696d6775722e636f6d2f68464b5834794a2e706e67)](https://camo.githubusercontent.com/102b3eb362cfb1669373cdcd2b62863cc34485553733434a6a9e3338c4a95bef/68747470733a2f2f692e696d6775722e636f6d2f68464b5834794a2e706e67)

以上說明 : 自己在 master 分支上加 t2.txt ， 並且commit ( 模擬 pull )

接下來，切換到 master 分支下和 v1 branch 分支 合併，並且 push

[![alt tag](https://camo.githubusercontent.com/a43ad4fed03fc5536d4eb41fc699bfecca56e8b783fb4a54327996e434222163/68747470733a2f2f692e696d6775722e636f6d2f307343483251312e706e67)](https://camo.githubusercontent.com/a43ad4fed03fc5536d4eb41fc699bfecca56e8b783fb4a54327996e434222163/68747470733a2f2f692e696d6775722e636f6d2f307343483251312e706e67)

你會發現，顯示出來的圖並不漂亮，如下圖

[![alt tag](https://camo.githubusercontent.com/336a7a4357856e98fdb8b66169154459c4121d191bf65ae617b13b316d47c259/68747470733a2f2f692e696d6775722e636f6d2f7a6249506479622e706e67)](https://camo.githubusercontent.com/336a7a4357856e98fdb8b66169154459c4121d191bf65ae617b13b316d47c259/68747470733a2f2f692e696d6775722e636f6d2f7a6249506479622e706e67)

示範 **使用 rebase** 的範例

前面的部份基本上一樣

[![alt tag](https://camo.githubusercontent.com/62a778afa731e52f0bf02303f2bb929b2600754f78ab7a6ba4da3e9a42e7f027/68747470733a2f2f692e696d6775722e636f6d2f45306168666e442e706e67)](https://camo.githubusercontent.com/62a778afa731e52f0bf02303f2bb929b2600754f78ab7a6ba4da3e9a42e7f027/68747470733a2f2f692e696d6775722e636f6d2f45306168666e442e706e67)

[![alt tag](https://camo.githubusercontent.com/4dbc38a866c29aaef39547bd30059d481ef24ab217257f3662253b62f666d0f2/68747470733a2f2f692e696d6775722e636f6d2f4c6234644230562e706e67)](https://camo.githubusercontent.com/4dbc38a866c29aaef39547bd30059d481ef24ab217257f3662253b62f666d0f2/68747470733a2f2f692e696d6775722e636f6d2f4c6234644230562e706e67)

以上說明 : 先建立 v1 branch，接著 add 後再 commit。

假設現在又有人 push 了，以下模擬 pull ，自己加上一個 commit

[![alt tag](https://camo.githubusercontent.com/102b3eb362cfb1669373cdcd2b62863cc34485553733434a6a9e3338c4a95bef/68747470733a2f2f692e696d6775722e636f6d2f68464b5834794a2e706e67)](https://camo.githubusercontent.com/102b3eb362cfb1669373cdcd2b62863cc34485553733434a6a9e3338c4a95bef/68747470733a2f2f692e696d6775722e636f6d2f68464b5834794a2e706e67)

以上說明 : 自己在 master 分支上加 t2.txt ， 並且 commit ( 模擬 pull )

***差異的部份***

[![alt tag](https://camo.githubusercontent.com/1da027c947348dd412692c14d76896a8f5d98927d51141d0e8eade222304ac39/68747470733a2f2f692e696d6775722e636f6d2f34355a5847694b2e706e67)](https://camo.githubusercontent.com/1da027c947348dd412692c14d76896a8f5d98927d51141d0e8eade222304ac39/68747470733a2f2f692e696d6775722e636f6d2f34355a5847694b2e706e67)

以上說明 : 先切換到 v1 分支，然後使用以下指令

```
git rebase master
```

[![alt tag](https://camo.githubusercontent.com/09ab6e62b55cec09e58a0641ec591febc9946942513b1962a8f456c42afe97d0/68747470733a2f2f692e696d6775722e636f6d2f4c7064394b6a722e706e67)](https://camo.githubusercontent.com/09ab6e62b55cec09e58a0641ec591febc9946942513b1962a8f456c42afe97d0/68747470733a2f2f692e696d6775722e636f6d2f4c7064394b6a722e706e67)

以上說明 : 再切回 master 分支，並且使用 merge 合併 v1 分支，最後在 push

你看~ 是不是變的整齊又漂亮多了呢?

[![alt tag](https://camo.githubusercontent.com/77e022974ef8697c5e3b4b39cef21411264c429719a923483f6e0caa834646f6/68747470733a2f2f692e696d6775722e636f6d2f316a42493770772e706e67)](https://camo.githubusercontent.com/77e022974ef8697c5e3b4b39cef21411264c429719a923483f6e0caa834646f6/68747470733a2f2f692e696d6775722e636f6d2f316a42493770772e706e67)

git rebase 就是將 master 的最新 commit 接回來，再補上自己分支的 commit。

以上就是 git rebase 的介紹。

## git rebase interactive

小弟我當初年輕，一直以為 `git rebase` 就只是讓 commit log 看起來比較乾淨而已，結果無意間發現，

`git rebase` 的 interactive 超強，所以，這邊就來介紹 `git rebase` 的強大功能 😏

以下是 git rebase interactive 可以使用的指令，這些說明是我從 git 中複製出來的，等等會顯示給大家看，

```
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
```

如果大家想要更進一步的了解，請參考 [INTERACTIVE MODE](https://git-scm.com/docs/git-rebase#_interactive_mode)，

pick 沒什麼好講的，就使用這個 commit 而已😄

### reword

[Youtube Tutorial - git rebase interactive - reword - PART 1](https://youtu.be/JhY0rR2wQq0)

```
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
```

以下為官方的說明

```
If you just want to edit the commit message for a commit, replace the command "pick" with the command "reword".
```

說明已經很清楚了，就是可以編輯 commit message。

( 不能修改 commit 內容，也就是 files 內容 )

假設，現在我們有一個 git log 是這樣，

[![alt tag](https://camo.githubusercontent.com/ecdd343666cf9685cc03a78006176fdf47f8cb2b503ea4556e0fd086a07588a2/68747470733a2f2f692e696d6775722e636f6d2f3662576e4a6e4b2e706e67)](https://camo.githubusercontent.com/ecdd343666cf9685cc03a78006176fdf47f8cb2b503ea4556e0fd086a07588a2/68747470733a2f2f692e696d6775722e636f6d2f3662576e4a6e4b2e706e67)

commit id 2659f65 有 Typo，正確的 commit message 應該是 add c.py 才對，

所以現在要修正他，我們的目標 commit id 為 2659f65，指令為

```
git rebase -i <after-this-commit>
```

after-this-commit 這個是什麼意思❓

簡單說，就是要選當下的 commit id 的上一個，

以這個例子來說，我們的目標 commit id 為 2659f65，但指令我們必須下

```
git rebase -i f0a761d
```

[![alt tag](https://camo.githubusercontent.com/b5836eab987edb9f64e3c85a9ce5ae62c385ff8120a5112b816d7d47dfceaf0e/68747470733a2f2f692e696d6775722e636f6d2f6431356e476a782e706e67)](https://camo.githubusercontent.com/b5836eab987edb9f64e3c85a9ce5ae62c385ff8120a5112b816d7d47dfceaf0e/68747470733a2f2f692e696d6775722e636f6d2f6431356e476a782e706e67)

這樣應該就很清楚了，總之，記得要選擇目標 commit id 的上一個就對了。

當你按下 ENTER 之後，你應該會看到下圖

[![alt tag](https://camo.githubusercontent.com/11c2c5c3644536d7fd7de94a1f21b08e03e7b1a3fcb72d3dd2960dbd562b9961/68747470733a2f2f692e696d6775722e636f6d2f344953476357312e706e67)](https://camo.githubusercontent.com/11c2c5c3644536d7fd7de94a1f21b08e03e7b1a3fcb72d3dd2960dbd562b9961/68747470733a2f2f692e696d6775722e636f6d2f344953476357312e706e67)

A 的部份就是我們要修改的目標，B 的部分就是說明 ( 前面貼給大家看的東西 )，

接著，按 i 進入編輯模式，然後將目標改成 r 或是 reword 都可以，接著輸入 `:wq`

[![alt tag](https://camo.githubusercontent.com/56887bc371be190dca623db41c36943f29fde44742271e115485ff9a1a7bb0c7/68747470733a2f2f692e696d6775722e636f6d2f7a5065487544612e706e67)](https://camo.githubusercontent.com/56887bc371be190dca623db41c36943f29fde44742271e115485ff9a1a7bb0c7/68747470733a2f2f692e696d6775722e636f6d2f7a5065487544612e706e67)

接著我們再按下 ENTER，會再跳出一次畫面，這時候，你就將 commit 訊息修改成

正確的，將 add c.py Typo 修改為 add c.py

[![alt tag](https://camo.githubusercontent.com/edb7f6fbde3193daab01a594e53cad66b3e8ebc06f950f7c07dc164abf296df1/68747470733a2f2f692e696d6775722e636f6d2f627259624e71792e706e67)](https://camo.githubusercontent.com/edb7f6fbde3193daab01a594e53cad66b3e8ebc06f950f7c07dc164abf296df1/68747470733a2f2f692e696d6775722e636f6d2f627259624e71792e706e67)

輸入 `:wq` 之後，再 ENTER ( 完成 )

[![alt tag](https://camo.githubusercontent.com/46c4e58a64eafb2f5a81b4ed3c628062ff81e1164f29c89a4ef8e698ffdb3d1d/68747470733a2f2f692e696d6775722e636f6d2f6b69744b71726d2e706e67)](https://camo.githubusercontent.com/46c4e58a64eafb2f5a81b4ed3c628062ff81e1164f29c89a4ef8e698ffdb3d1d/68747470733a2f2f692e696d6775722e636f6d2f6b69744b71726d2e706e67)

我們再用 log 確認一下( 如下圖 )，的確修改成功了，成功將訊息修改為 add c.py，

[![alt tag](https://camo.githubusercontent.com/f1a7786c0216108d37a342e720dd362453d47df5f085aaef3b96ae99d7386e67/68747470733a2f2f692e696d6775722e636f6d2f72576f6a4749752e706e67)](https://camo.githubusercontent.com/f1a7786c0216108d37a342e720dd362453d47df5f085aaef3b96ae99d7386e67/68747470733a2f2f692e696d6775722e636f6d2f72576f6a4749752e706e67)

這邊有個地方要和大家提一下，就是 commit id 會改變，我把改變的地方框出來給各位看，

修改前

[![alt tag](https://camo.githubusercontent.com/c7b0321b39d53dca3daa73661c5678474ba148c963cb847ea30f0378da3f16ff/68747470733a2f2f692e696d6775722e636f6d2f366936577633352e706e67)](https://camo.githubusercontent.com/c7b0321b39d53dca3daa73661c5678474ba148c963cb847ea30f0378da3f16ff/68747470733a2f2f692e696d6775722e636f6d2f366936577633352e706e67)

修改後

[![alt tag](https://camo.githubusercontent.com/80fdd7a8736bbfa138e275927abc51698413d803f0b124d6541db3851042f895/68747470733a2f2f692e696d6775722e636f6d2f6d766a393655322e706e67)](https://camo.githubusercontent.com/80fdd7a8736bbfa138e275927abc51698413d803f0b124d6541db3851042f895/68747470733a2f2f692e696d6775722e636f6d2f6d766a393655322e706e67)

簡單來說，就是目前 commit id 之後的 commit id 都會改變 ( 有點繞口 😅 )

這邊補充一下，只要你用了 rebase，就會看到類似下面的圖，

[![alt tag](https://camo.githubusercontent.com/2fecd626d0b36dd27c06141c8b0e8ab0b640fd25f0a993c78eb3240faab5addf/68747470733a2f2f692e696d6775722e636f6d2f696944663434712e706e67)](https://camo.githubusercontent.com/2fecd626d0b36dd27c06141c8b0e8ab0b640fd25f0a993c78eb3240faab5addf/68747470733a2f2f692e696d6775722e636f6d2f696944663434712e706e67)

origin/master 就是指遠端 ( romote ) 的 repo，它是和你說你現在的 repo 已經和 origin/master

不一樣了，所以，這時候你如果要 push，請使用 `git push --force-with-lease`。

這邊可能有人會問，如果我希望修改第一個 commit 該怎麼辦 ❓

這時候可以使用，

```
git rebase -i --root
```

### edit

[Youtube Tutorial - git rebase interactive - edit - PART 2](https://youtu.be/TCKjQppHxxQ)

```
# Commands:
# p, pick = use commit
# e, edit = use commit, but stop for amending
```

以下為官方的說明

```
By replacing the command "pick" with the command "edit", you can tell git rebase to stop after applying that commit, so that you can edit the files and/or the commit message, amend the commit, and continue rebasing.
```

簡單說，reword 只可以修改 commit message，而 edit 不只可以修改 commit message ，還可以修改 files 內容。

先來看看下面這張圖

[![alt tag](https://camo.githubusercontent.com/d66906a08ac7ebd4e1f1bdbd8b6eeb44ac4738151c6db115734206e17b045f91/68747470733a2f2f692e696d6775722e636f6d2f396a304a6e4b772e706e67)](https://camo.githubusercontent.com/d66906a08ac7ebd4e1f1bdbd8b6eeb44ac4738151c6db115734206e17b045f91/68747470733a2f2f692e696d6775722e636f6d2f396a304a6e4b772e706e67)

這圖很明顯 add a.py -> add b.py -> add c.py -> add d.py ，現在我想在 add c.py 和 add d.py 中再加一個東西，

也就是變成 add a.py -> add b.py -> add c.py -> add c1.py -> add d.py 這樣。

增加一個 add c1.py 的情境時就可以使用 edit 了，( 以下我就不說那麼詳細了，我直接講重點 )，

先執行以下指令 ( 我們的目標是 a7ed6ff ，所以選他的上一個 commit id，也就是 f0a761d )

```
git rebase -i f0a761d
```

這次我們將 pick 修改成 e 或是 edit ( 如下圖 )

[![alt tag](https://camo.githubusercontent.com/274fa13427dc377a62ca211e53804becfe8017a4b41797f1ac6df17c6c287f89/68747470733a2f2f692e696d6775722e636f6d2f624b724c496c332e706e67)](https://camo.githubusercontent.com/274fa13427dc377a62ca211e53804becfe8017a4b41797f1ac6df17c6c287f89/68747470733a2f2f692e696d6775722e636f6d2f624b724c496c332e706e67)

當你按下 ENTER 之後，你會看到下圖，

[![alt tag](https://camo.githubusercontent.com/92d02f66136ea6d7c58ae35e781c4719a707466e0d6adfd7a101ffef1cbec3ce/68747470733a2f2f692e696d6775722e636f6d2f77686b437a6f6b2e706e67)](https://camo.githubusercontent.com/92d02f66136ea6d7c58ae35e781c4719a707466e0d6adfd7a101ffef1cbec3ce/68747470733a2f2f692e696d6775722e636f6d2f77686b437a6f6b2e706e67)

A 的部份是可以修改 commit message，

B 的部份則是和你說當你修改 ( 滿足 ) 完畢，可以執行 `git rebase --continue`，

A 的部份我們不做了，但我們現在來加工吧 ( 增加 c1.py )，

首先，我們建立一個 c1.py 檔案，然後 `git add c1.py`，接著 commit 他 ( 如下圖 )

[![alt tag](https://camo.githubusercontent.com/e8a067d142eecd23ee4e9fee8eeb7c353e6454704942bf14bbe7e0e9f8cacf9b/68747470733a2f2f692e696d6775722e636f6d2f667259425566542e706e67)](https://camo.githubusercontent.com/e8a067d142eecd23ee4e9fee8eeb7c353e6454704942bf14bbe7e0e9f8cacf9b/68747470733a2f2f692e696d6775722e636f6d2f667259425566542e706e67)

剛剛有說過了，當你滿足時，可執行 `git rebase --continue`，收工

[![alt tag](https://camo.githubusercontent.com/5e6d5be28b6d2cfa8ce5be5a6f8fe852a02794488c4ac2661868a99c25549c2f/68747470733a2f2f692e696d6775722e636f6d2f736a6e456e30482e706e67)](https://camo.githubusercontent.com/5e6d5be28b6d2cfa8ce5be5a6f8fe852a02794488c4ac2661868a99c25549c2f/68747470733a2f2f692e696d6775722e636f6d2f736a6e456e30482e706e67)

再用 log 確認一下，太神了 😆 成功加上去了

[![alt tag](https://camo.githubusercontent.com/b1b3b2aa89b70cd2118738512e860d14c6d2ea5be2358bfc7ba1166091d7350b/68747470733a2f2f692e696d6775722e636f6d2f69724543774c482e706e67)](https://camo.githubusercontent.com/b1b3b2aa89b70cd2118738512e860d14c6d2ea5be2358bfc7ba1166091d7350b/68747470733a2f2f692e696d6775722e636f6d2f69724543774c482e706e67)

### squash

[Youtube Tutorial - git rebase interactive - squash fixup - PART 3](https://youtu.be/bfrZrbEHis0)

```
# Commands:
# p, pick = use commit
# s, squash = use commit, but meld into previous commit
```

以下為官方的說明

```
 The suggested commit message for the folded commit is the concatenation of the commit messages of the first commit and of those with the "squash" command,
```

簡單說，你如果想要將多個 commit 合併成一個，使用 squash 就對了，( 以下我就不說那麼詳細了，我直接講重點 )，

這次的目標是要將 commit id fc45824 以及 commit id a7ed6ff 合併起來 ( 如下圖 )

[![alt tag](https://camo.githubusercontent.com/42d44470cffe33db57817b306d4da68f69a526d5ed102848ca457c8c95343b1e/68747470733a2f2f692e696d6775722e636f6d2f763858774f544e2e706e67)](https://camo.githubusercontent.com/42d44470cffe33db57817b306d4da68f69a526d5ed102848ca457c8c95343b1e/68747470733a2f2f692e696d6775722e636f6d2f763858774f544e2e706e67)

先執行以下指令

```
git rebase -i f0a761d
```

接著你會看到下圖，我們將 fc45824 這個 cmmit 的 pick 修改成 s 或 squash

( 他會合併他的前一個，也就是 a7ed6ff )

[![alt tag](https://camo.githubusercontent.com/931c2eccdc2c01bc829e2b7813c5b96e7964e1bbc92931215f3976366b6b051c/68747470733a2f2f692e696d6775722e636f6d2f7267576b7656702e706e67)](https://camo.githubusercontent.com/931c2eccdc2c01bc829e2b7813c5b96e7964e1bbc92931215f3976366b6b051c/68747470733a2f2f692e696d6775722e636f6d2f7267576b7656702e706e67)

( 如果你要合併多個 commit，就多個都改成 s 或 squash, 注意, 有順序性❗❗ )

將著按下 ENTER，會看到下圖

[![alt tag](https://camo.githubusercontent.com/bd1d25150c422422c54b18553f7cc3205ae28640252836af496f6e08fb825dd7/68747470733a2f2f692e696d6775722e636f6d2f704236796c6c412e706e67)](https://camo.githubusercontent.com/bd1d25150c422422c54b18553f7cc3205ae28640252836af496f6e08fb825dd7/68747470733a2f2f692e696d6775722e636f6d2f704236796c6c412e706e67)

這時候他已經合併了這兩個 commit，我們就可以輸入新的 commit message，

這邊我們輸入 add c.py and c1.py

[![alt tag](https://camo.githubusercontent.com/ec1642e31a692cf252fada28a15b46174ec83c6dc79e90db756a6995a5b7ec81/68747470733a2f2f692e696d6775722e636f6d2f6d3945364b55702e706e67)](https://camo.githubusercontent.com/ec1642e31a692cf252fada28a15b46174ec83c6dc79e90db756a6995a5b7ec81/68747470733a2f2f692e696d6775722e636f6d2f6d3945364b55702e706e67)

再按 ENTER ( 成功 )

[![alt tag](https://camo.githubusercontent.com/b3d200aec3ed0e2cc4ddb0cb7b0b272b0731c8290b8a7215bf704baf4fd831cc/68747470733a2f2f692e696d6775722e636f6d2f58304f374935482e706e67)](https://camo.githubusercontent.com/b3d200aec3ed0e2cc4ddb0cb7b0b272b0731c8290b8a7215bf704baf4fd831cc/68747470733a2f2f692e696d6775722e636f6d2f58304f374935482e706e67)

可以再用 log 確認一下，我們成功將兩個 commit 合併了

[![alt tag](https://camo.githubusercontent.com/e91deed38a7a7f43dab71072568d2cf1608bf003424957d52891e81a5eb5e5ba/68747470733a2f2f692e696d6775722e636f6d2f7235334b4965762e706e67)](https://camo.githubusercontent.com/e91deed38a7a7f43dab71072568d2cf1608bf003424957d52891e81a5eb5e5ba/68747470733a2f2f692e696d6775722e636f6d2f7235334b4965762e706e67)

c.py 以及 c1.py 都存在，代表我們成功了😆

[![alt tag](https://camo.githubusercontent.com/a9d5c244a9257082e989aa02d5a10b76cbd7013151ad3f856205381339439116/68747470733a2f2f692e696d6775722e636f6d2f57686b4c4447612e706e67)](https://camo.githubusercontent.com/a9d5c244a9257082e989aa02d5a10b76cbd7013151ad3f856205381339439116/68747470733a2f2f692e696d6775722e636f6d2f57686b4c4447612e706e67)

### fixup

[Youtube Tutorial - git rebase interactive - squash fixup - PART 3](https://youtu.be/bfrZrbEHis0)

```
# Commands:
# p, pick = use commit
# f, fixup = like "squash", but discard this commit's log message
```

以下為官方的說明

```
omits the commit messages of commits with the "fixup" command.
```

其實這個和 squash 很像，通常如果我們要忽略一個 commit message 但保留 commit 的內容，我們就會使用 fixup，

目標，這邊我們想要移除 fc45824 的個 commit ( 但保留 commit 的內容 )

[![alt tag](https://camo.githubusercontent.com/240c1b34474db5b309a273ac2a8a55a1b02172ea9d0eaf9de055c194e7c5094a/68747470733a2f2f692e696d6775722e636f6d2f414672643055412e706e67)](https://camo.githubusercontent.com/240c1b34474db5b309a273ac2a8a55a1b02172ea9d0eaf9de055c194e7c5094a/68747470733a2f2f692e696d6775722e636f6d2f414672643055412e706e67)

先執行以下指令

```
git rebase -i f0a761d
```

將 fc45824 的 pick 修改成 f 或 fixup ( 如下圖 )

( 他會移除 fc45824 這個 commit message ，但保留 commit 的內容 )

[![alt tag](https://camo.githubusercontent.com/cb1a490f90db2c4c4629abfdbc2f1ca22aad7f492c294f745b074482ec6734d7/68747470733a2f2f692e696d6775722e636f6d2f6144483179316e2e706e67)](https://camo.githubusercontent.com/cb1a490f90db2c4c4629abfdbc2f1ca22aad7f492c294f745b074482ec6734d7/68747470733a2f2f692e696d6775722e636f6d2f6144483179316e2e706e67)

接著 ENTER，成功 rebase

[![alt tag](https://camo.githubusercontent.com/74eda3d02b3a3f4b8cd7020d6d9a8a50b070ad4185732d0b4c2d1746f2c82a71/68747470733a2f2f692e696d6775722e636f6d2f424d73326838722e706e67)](https://camo.githubusercontent.com/74eda3d02b3a3f4b8cd7020d6d9a8a50b070ad4185732d0b4c2d1746f2c82a71/68747470733a2f2f692e696d6775722e636f6d2f424d73326838722e706e67)

可以再用 log 確認一下，我們忽略了 add c1.py 這個 commit

[![alt tag](https://camo.githubusercontent.com/1ef0438c18f77de9b9da181907f1e113a13b7599dfe5b726560e08c25564709a/68747470733a2f2f692e696d6775722e636f6d2f6267594a6136542e706e67)](https://camo.githubusercontent.com/1ef0438c18f77de9b9da181907f1e113a13b7599dfe5b726560e08c25564709a/68747470733a2f2f692e696d6775722e636f6d2f6267594a6136542e706e67)

但是 c.py 以及 c1.py 都存在 ( 只忽略 commit message )，

[![alt tag](https://camo.githubusercontent.com/a7c5d2194b1a60e4c539586439b7634bc98b2658029321fe220b7f2cce2c1e2e/68747470733a2f2f692e696d6775722e636f6d2f745972423346392e706e67)](https://camo.githubusercontent.com/a7c5d2194b1a60e4c539586439b7634bc98b2658029321fe220b7f2cce2c1e2e/68747470733a2f2f692e696d6775722e636f6d2f745972423346392e706e67)

看到這裡，大家其實可以想一想 squash 和 fixup 真的非常類似，

只不過 squash 可以修改 commit message。

簡單一點，單純想要忽略某一個 commit message 時，使用 fixup，

想要合併 commit 並修改 commit message 時，使用 squash。

### exec

[Youtube Tutorial - git rebase interactive - exec drop - PART 4](https://youtu.be/u8imRiiSyzk)

```
# Commands:
# p, pick = use commit
# x, exec = run command (the rest of the line) using shell
```

以下為官方的說明

```
You may want to check that your history editing did not break anything by running a test, or at least recompiling at intermediate points in history by using the "exec" command (shortcut "x")
```

這個功能我比較少用，但還是說一下，簡單說，就是他可以用來 check 你的

rebase 改動是不是影響到整體 ( 用 exec command 確認 )。

聽不太懂 ❓ 沒關係，假如我今天做了一大堆的 rabase 更動，但我想確認我這樣做了之後，

對整體是不是有影響，也就是可以在更動時，順便跑你的 test 去確認整體是正常 work。

還是聽不懂 ❓ 也沒關係，我用一個範例給大家看

[![alt tag](https://camo.githubusercontent.com/d9f390dc4f66729ec6993fccea1b1f6e61b0587fe67bbd7c36991a4ebfce5f4c/68747470733a2f2f692e696d6775722e636f6d2f69753162454f772e706e67)](https://camo.githubusercontent.com/d9f390dc4f66729ec6993fccea1b1f6e61b0587fe67bbd7c36991a4ebfce5f4c/68747470733a2f2f692e696d6775722e636f6d2f69753162454f772e706e67)

如上圖，假如我想要在我更動中做一些 test 去確保我的更動不會影響整體，

( 雖然這邊都是 pick，也就是沒改動，但方便說明，大家請自行想像有改動😅 )

[![alt tag](https://camo.githubusercontent.com/94ce6addf4d2db7a0365a6d8f4c752fa81c3ba3750b2f29cc914e9d0b0c3f725/68747470733a2f2f692e696d6775722e636f6d2f32633979636d532e706e67)](https://camo.githubusercontent.com/94ce6addf4d2db7a0365a6d8f4c752fa81c3ba3750b2f29cc914e9d0b0c3f725/68747470733a2f2f692e696d6775722e636f6d2f32633979636d532e706e67)

A 的部份 echo "test sucess" 這個自然不用有問題，

但是 B 的部分就會出問題，因為根本沒有 error 這個指令，

當如果執行到 shell 有錯誤時，他會停下來，讓你修正，

如下圖，我們停在了 add c.py 這個 commit 上，因為接下來得 test error 了

[![alt tag](https://camo.githubusercontent.com/9366d928d679944a124013eb63bcacc7f2044cd8a36cdb0163c0bed4bbfd81e3/68747470733a2f2f692e696d6775722e636f6d2f795642336e61432e706e67)](https://camo.githubusercontent.com/9366d928d679944a124013eb63bcacc7f2044cd8a36cdb0163c0bed4bbfd81e3/68747470733a2f2f692e696d6775722e636f6d2f795642336e61432e706e67)

這時候我們可以修正問題，修正完了之後，再執行 `git rebase --continue`。

[![alt tag](https://camo.githubusercontent.com/6134234841d1f76fa74a1188c518e8734d95bf373f3918ad448f26969638eacb/68747470733a2f2f692e696d6775722e636f6d2f594244306439562e706e67)](https://camo.githubusercontent.com/6134234841d1f76fa74a1188c518e8734d95bf373f3918ad448f26969638eacb/68747470733a2f2f692e696d6775722e636f6d2f594244306439562e706e67)

這個功能我想應該是讓你去邊修改邊跑你自己的 test，確保改動都正常。

### drop

[Youtube Tutorial - git rebase interactive - exec drop - PART 4](https://youtu.be/u8imRiiSyzk)

```
# Commands:
# p, pick = use commit
# d, drop = remove commit
```

以下為官方的說明

```
To drop a commit, replace the command "pick" with "drop", or just delete the matching line.
```

這個就簡單多了，移除這個 commit ( 包含 commit 內容 )，

假設我們的 log 如下，

[![alt tag](https://camo.githubusercontent.com/e1683c78adc62f35161c4d19530efa6a572164cc508fcc79bd725074182c9701/68747470733a2f2f692e696d6775722e636f6d2f7a7a35617256702e706e67)](https://camo.githubusercontent.com/e1683c78adc62f35161c4d19530efa6a572164cc508fcc79bd725074182c9701/68747470733a2f2f692e696d6775722e636f6d2f7a7a35617256702e706e67)

這次的目標是移除 f0a761d 和 980bd9a 和 1539219 這些 commit，

先執行以下指令

```
git rebase -i 8f13aaa
```

將 pick 修改成 d 或 drop ( 如下圖 )

[![alt tag](https://camo.githubusercontent.com/16d1e801f33d041b6646591d4388efdf20befd778d28a537ca330c7429367e1e/68747470733a2f2f692e696d6775722e636f6d2f476f63314c48312e706e67)](https://camo.githubusercontent.com/16d1e801f33d041b6646591d4388efdf20befd778d28a537ca330c7429367e1e/68747470733a2f2f692e696d6775722e636f6d2f476f63314c48312e706e67)

按 ENTER 之後，再用 log 確認一下，

[![alt tag](https://camo.githubusercontent.com/c8c8d56d4000fb40cd330603dbc9dc789eef944895ddccbe38af29026f343adb/68747470733a2f2f692e696d6775722e636f6d2f75377a325933552e706e67)](https://camo.githubusercontent.com/c8c8d56d4000fb40cd330603dbc9dc789eef944895ddccbe38af29026f343adb/68747470733a2f2f692e696d6775722e636f6d2f75377a325933552e706e67)

從上圖可以發現，我們已經成功的移除 f0a761d 和 980bd9a 和 1539219 這些 commit，

並且也看到 commit 內容也都被移除了，只剩下 a.py 而已。

## git pull 補充

既然介紹完了 `git fetch` 以及 `git rebase` 之後，接下來我要再補充一些 `git pull` 額外的 options 參數

```
git pull [<options>] [<repository> [<refspec>…]]
```

更多詳細指令可參考 https://git-scm.com/docs/git-pull#_options。

這裡簡單整理一下，

```
git pull = git fetch + git merge
git pull --rebase = git fetch + git rebase
```

在 [git-rebase](https://github.com/twtrubiks/Git-Tutorials#git-rebase) 中已經讓大家了解到使用 git-rebase 可以讓 code review 的人

看起來比較舒服，所以就使用 `git pull --rebase` 吧 ( 前提是你要知道你在幹嘛 😄 )。

這邊我模擬 `git pull` 以及 `git pull --rebase` 的差異，順便加上衝突的情況，因為步驟蠻多的，

所以如果你想了解更多他的概念，請參考以下手把手教學，

[Youtube Tutorial - git pull vs git pull --rebase](https://youtu.be/8h0K-2OaeSk)

使用 `git pull` 後的結果，code review 的人一定翻桌 ( 如下圖 )😤

這邊我有順便模擬衝突的時候，你會發現如果使用 `git pull` 會多一個 commit (也就是下方的 "fix conflict")。

[![alt tag](https://camo.githubusercontent.com/3422ca9e9d2cee66efcab72b0804cfebdcdc1b1b99e71f1bd0b0e1a937d446dd/68747470733a2f2f692e696d6775722e636f6d2f434e674b5233792e706e67)](https://camo.githubusercontent.com/3422ca9e9d2cee66efcab72b0804cfebdcdc1b1b99e71f1bd0b0e1a937d446dd/68747470733a2f2f692e696d6775722e636f6d2f434e674b5233792e706e67)

使用 `git pull --rebase` 後的結果，code review 的人表示溫馨 ( 如下圖 )😇

這邊我有順便模擬衝突的時候，你會發現如果使用 `git pull --rebase` 並不會像剛剛一樣多了一個 commit，

原因是因為當我們使用 `git pull --rebase` 造成衝突時，修好衝突的內容之後，git add xxxx，接著我們會

直接執行 `git rebase --continue`。

[![alt tag](https://camo.githubusercontent.com/826339348ce90bb596225346b63984d413cdf7dde19b453563de182736f5c4ae/68747470733a2f2f692e696d6775722e636f6d2f524b4d6f3975652e706e67)](https://camo.githubusercontent.com/826339348ce90bb596225346b63984d413cdf7dde19b453563de182736f5c4ae/68747470733a2f2f692e696d6775722e636f6d2f524b4d6f3975652e706e67)

假設今天你執行了 `git pull --rebase` 之後，發現很難受 😨，想要取消，

直接執行 `git rebase --abort` 即可回到之前的狀態。

額外補充小技巧,

- [Youtube Tutorial - git autostash 參數說明](https://youtu.be/kg2PyZr7l5k)

說明 `--autostash`,

一般來說, 如果我們工作到一半, 突然想要直接 `git pull --rebase`, 又不想 commit,

流程大約會像下面這樣

```
git stash # 將目前的改動存進去 stash 中
git pull --rebase
git stash pop # 將之前的改動從 stash 中 pop 出來
# 如果有衝突再去解決衝突
```

但如果每次都要執行這麼多指令其實會有點煩😓

但可以透過一個參數來解決, 也就是

```
git pull --rebase --autostash
```

以上這段指令基本上就是幫你執行了剛剛上面那一串的東西,

如果有衝突, 就再修正衝突即可😄

## git-cherry-pick

看影片會更清楚，手把手帶大家動手做 [Youtube Tutorial - git-cherry-pick](https://youtu.be/x3UtKUvlDdI)

git-cherry-pick 這個指令大家可能會比較陌生😕

沒關係，我們先來看 [官方](https://git-scm.com/docs/git-cherry-pick) 的說明

```
git-cherry-pick - Apply the changes introduced by some existing commits
```

看完官方說明還是❓❓❓

沒關係，我來假設一個情境 ( 理解完它你就了解了 git-cherry-pick 的用途了 )，

假設現在 master 分支的 log 如下圖

[![alt tag](https://camo.githubusercontent.com/5193acf4e766f166f3b45b5c1f6295a7cc51d571d53b78fe55abb9449ddd96b1/68747470733a2f2f692e696d6775722e636f6d2f634d636e3679452e706e67)](https://camo.githubusercontent.com/5193acf4e766f166f3b45b5c1f6295a7cc51d571d53b78fe55abb9449ddd96b1/68747470733a2f2f692e696d6775722e636f6d2f634d636e3679452e706e67)

然後有一個 v1 的分支 log 如下圖

[![alt tag](https://camo.githubusercontent.com/2e5728d720d782add858de57bf529993c3dbd86d5609719e525f59874e8ecdd7/68747470733a2f2f692e696d6775722e636f6d2f4f5a374a4c6b652e706e67)](https://camo.githubusercontent.com/2e5728d720d782add858de57bf529993c3dbd86d5609719e525f59874e8ecdd7/68747470733a2f2f692e696d6775722e636f6d2f4f5a374a4c6b652e706e67)

現在我希望 merge v1 分支中的 14dee93 - add d.py 這個 commit

( 因為 14dee93 這個 commit 實在太棒了或是因為某些原因只需要這個 commit )

遇到上述這種情況，就很適合使用 git-cherry-pick，也就是說我想要其他分支中的某幾個 commit 而已，

不需要全部，換句話說，就是撿其他分支中的 commit 過來使用。

了解了適合的使用情境，接下來我們就來實戰😏

首先，我想要 v1 分支中的 14dee93 - add d.py 這個 commit，

所以我先切到 master 分支，接著執行

```
git cherry-pick 14dee93
```

如果你想要一次撿很多的分支過來也是可以，直接使用空白隔開即可

```
git cherry-pick 14dee93 xxxxxx xxxxxx xxxxxx xxxxx
```

如果沒有衝突，就會看到如下圖

[![alt tag](https://camo.githubusercontent.com/8656b594483553a9705340e7072aecd4344cf0d0d125ae12d5b5cbcabd6ea4c6/68747470733a2f2f692e696d6775722e636f6d2f59495458784d6b2e706e67)](https://camo.githubusercontent.com/8656b594483553a9705340e7072aecd4344cf0d0d125ae12d5b5cbcabd6ea4c6/68747470733a2f2f692e696d6775722e636f6d2f59495458784d6b2e706e67)

再觀看一下 master 的 log

[![alt tag](https://camo.githubusercontent.com/e6e9983df639dc7fd286a56e029a079a7cfec4589951b147728f2dde4f21c21e/68747470733a2f2f692e696d6775722e636f6d2f69474549445a4c2e706e67)](https://camo.githubusercontent.com/e6e9983df639dc7fd286a56e029a079a7cfec4589951b147728f2dde4f21c21e/68747470733a2f2f692e696d6775722e636f6d2f69474549445a4c2e706e67)

你會發現我們成功把 v1 分支中的 14dee93 - add d.py 這個 commit 拿過來

使用了，但現在它的 commit id 卻是 ab70429，這個是正常的，因為它需要

重新新計算😄

其實，你會發現 git-cherry-pick 沒有想像中的困難😆

在 cherry-pick 時，難免會遇到衝突，這邊我就再多做一個衝突的範例，

假設 master 的 log 如下

[![alt tag](https://camo.githubusercontent.com/199ce423503bafadfa7abf6c0d7ae45bafb32bb63b3a5bfa1f2123c42784204b/68747470733a2f2f692e696d6775722e636f6d2f707474625135552e706e67)](https://camo.githubusercontent.com/199ce423503bafadfa7abf6c0d7ae45bafb32bb63b3a5bfa1f2123c42784204b/68747470733a2f2f692e696d6775722e636f6d2f707474625135552e706e67)

v1 分支中的 log 如下，我想要它的 3a2f29a - add c.py and print world 這個 commit

[![alt tag](https://camo.githubusercontent.com/0bf6f3031337dabe43a060c4530e743f1b6ded8dd33b9cfc6f7c0d07c87ae2f2/68747470733a2f2f692e696d6775722e636f6d2f524669624853362e706e67)](https://camo.githubusercontent.com/0bf6f3031337dabe43a060c4530e743f1b6ded8dd33b9cfc6f7c0d07c87ae2f2/68747470733a2f2f692e696d6775722e636f6d2f524669624853362e706e67)

v2 分支中的 log 如下，我想要它的 553587b - add f.py這個 commit

[![alt tag](https://camo.githubusercontent.com/0c6bc4f8ec0b217dd00cdf94ef5a72bd195814187606c2faa5dc4f73c0f6a971/68747470733a2f2f692e696d6775722e636f6d2f49364c324677712e706e67)](https://camo.githubusercontent.com/0c6bc4f8ec0b217dd00cdf94ef5a72bd195814187606c2faa5dc4f73c0f6a971/68747470733a2f2f692e696d6775722e636f6d2f49364c324677712e706e67)

接下來我們就切回 master，然後 cherry-pick 這兩個 commit，

這時候你會發現，它衝突了😨

[![alt tag](https://camo.githubusercontent.com/8854ece617515a8fab6119599b7837634a596bf3efb95e9c1404c64611998fac/68747470733a2f2f692e696d6775722e636f6d2f664174514554302e706e67)](https://camo.githubusercontent.com/8854ece617515a8fab6119599b7837634a596bf3efb95e9c1404c64611998fac/68747470733a2f2f692e696d6775722e636f6d2f664174514554302e706e67)

使用 `git status` 看一下狀態，其實 A 的部分都教你如何解衝突了

[![alt tag](https://camo.githubusercontent.com/4c7ac85462a250e0af6ac614fb67d29b76c150353c79a368a28df9d7ebfd6892/68747470733a2f2f692e696d6775722e636f6d2f4a385a70506e672e706e67)](https://camo.githubusercontent.com/4c7ac85462a250e0af6ac614fb67d29b76c150353c79a368a28df9d7ebfd6892/68747470733a2f2f692e696d6775722e636f6d2f4a385a70506e672e706e67)

首先，我們先將 c.py 修正後，執行 `git add c.py`，接著再按照 A 的部份

執行 `git cherry-pick --continue`，就時候會跳出一個編輯視窗，

[![alt tag](https://camo.githubusercontent.com/737bafb0c8b6c817f75f742a0a210265a1c6d18648f469165bd634b4661fee31/68747470733a2f2f692e696d6775722e636f6d2f6769796c56414c2e706e67)](https://camo.githubusercontent.com/737bafb0c8b6c817f75f742a0a210265a1c6d18648f469165bd634b4661fee31/68747470733a2f2f692e696d6775722e636f6d2f6769796c56414c2e706e67)

輸入完 commit message 之後，再輸入 `wq`，就會看到下圖

[![alt tag](https://camo.githubusercontent.com/2ce3be9d0efbc8876e30c3e4345b11faac088aaf297456d7239bd3981e9bbfd3/68747470733a2f2f692e696d6775722e636f6d2f724138774d624f2e706e67)](https://camo.githubusercontent.com/2ce3be9d0efbc8876e30c3e4345b11faac088aaf297456d7239bd3981e9bbfd3/68747470733a2f2f692e696d6775722e636f6d2f724138774d624f2e706e67)

最後，再觀看 log，

[![alt tag](https://camo.githubusercontent.com/06e37ef8ad81d2fa5d42c3a497166e6e2520777c7553a141ae7404d8d0582ced/68747470733a2f2f692e696d6775722e636f6d2f6c4550363438632e706e67)](https://camo.githubusercontent.com/06e37ef8ad81d2fa5d42c3a497166e6e2520777c7553a141ae7404d8d0582ced/68747470733a2f2f692e696d6775722e636f6d2f6c4550363438632e706e67)

我們成功將我們要的 commit merge 到我們的 master 分支上了😙

想了解更多的使用方法，可參考官方文件 https://git-scm.com/docs/git-cherry-pick。

## git revert

假設我 commit history 為 A1 -> A2 -> A3 -> A4 -> A5 -> A6

我現在想要回 A4 這個 commit , 這時候我就可以使用 git revert ！！

先 revert A6

```
git revert A6
```

再 revert A5

```
git revert A5
```

假如你再看現在的 commit history , 他會長的像這樣

A1 -> A2 -> A3 -> A4 -> A5 -> A6 -> A6_revert -> A5_revert

這時候，其實你的 commit 就是在 A4 這個位置 。

使用 git revert 的好處，就是可以保留 commit history , 萬一你又後悔了，

也可以在 revert 回去。

## 解決衝突

在進行合併的時候，有時候會顯示出 **衝突conflicts** ，這時候就必須手動解決衝突後再送出。

通常我目前最容易遇到衝突 conflicts ，就是使用 pull 這個指令的時候

[![alt tag](https://camo.githubusercontent.com/8ddaca0a60c47fa07a641e42480713f9d1e9803f91c0c582613a22918cc2d8ff/68747470733a2f2f692e696d6775722e636f6d2f457068305677312e6a7067)](https://camo.githubusercontent.com/8ddaca0a60c47fa07a641e42480713f9d1e9803f91c0c582613a22918cc2d8ff/68747470733a2f2f692e696d6775722e636f6d2f457068305677312e6a7067)

仔細看這張圖，如果使用**pull**這個指令，會幫你 **自動 merge** ( 如圖裡的 Auto-merging Hello.py )，

然後接著看 CONFLICT ( content ) : Merge conflict in Hello.py ，又說 Automatic merge failed，

就是告訴你， Hello.py 這個檔案有衝突，然後你必須手動下去解決衝突。

git status 可以告訴我們衝突的文件。

[![alt tag](https://camo.githubusercontent.com/5ed923e483528c843cc83f5a62da990255d53c01def7d692fae0a41f9c241030/68747470733a2f2f692e696d6775722e636f6d2f766c5663586e382e6a7067)](https://camo.githubusercontent.com/5ed923e483528c843cc83f5a62da990255d53c01def7d692fae0a41f9c241030/68747470733a2f2f692e696d6775722e636f6d2f766c5663586e382e6a7067)

打開衝突文件我們會看到 Git 用 <<<<<<<，=======，>>>>>>> 標記出不同分支的內容，我們修改完畢後再提交：

[![alt tag](https://camo.githubusercontent.com/dac683325ee25dbdd65ca7431d918a398720bed8ec71c316e0978f6cca398aca/68747470733a2f2f692e696d6775722e636f6d2f726c504f61786e2e6a7067)](https://camo.githubusercontent.com/dac683325ee25dbdd65ca7431d918a398720bed8ec71c316e0978f6cca398aca/68747470733a2f2f692e696d6775722e636f6d2f726c504f61786e2e6a7067)

通常我們會手動下去修改衝突 conflicts，然後再加個 commit

```
git add Hello.py
git commit -m "conflict fixed"
```

### 假設今天我們想要放棄這個 merge 我們該怎麼做呢 ？

```
git merge --abort
```

或

```
git reset --hard HEAD
```

可以取消這次的 merge 回到 merge 前。

## git stash 指令

- [Youtube Tutorial - git stash 指令](https://youtu.be/CN065MNHtMY)

很多時候，我們正在開發一個新功能又或是 debug，然後突然有一個功能需要緊急修正，

但你又不想 commit 現在的狀況，因為根本沒意義，事情只做了一半，這時候 **stash**

這個實用的指令就派上用場了。

舉個例子，假設我們改了 A.py 和 B.py 這兩個檔案

[![alt tag](https://camo.githubusercontent.com/1798ef491d5cbb8cb0f7c5f35488eac90d8dcb38ad96ddfee422c1fd12f73055/68747470733a2f2f692e696d6775722e636f6d2f377858305431542e6a7067)](https://camo.githubusercontent.com/1798ef491d5cbb8cb0f7c5f35488eac90d8dcb38ad96ddfee422c1fd12f73055/68747470733a2f2f692e696d6775722e636f6d2f377858305431542e6a7067)

然後，現在突然有一個 bug 必須馬上(立刻)處理，

但是，啊我手上的事情還沒做完阿~~~~

這時候，可以利用以下指令

```
git stash
```

[![alt tag](https://camo.githubusercontent.com/2a5480e99b10a836f235df6111a3cfa6af8ea12172acd70e85c2bb13e3bc04d0/68747470733a2f2f692e696d6775722e636f6d2f63594348386d562e6a7067)](https://camo.githubusercontent.com/2a5480e99b10a836f235df6111a3cfa6af8ea12172acd70e85c2bb13e3bc04d0/68747470733a2f2f692e696d6775722e636f6d2f63594348386d562e6a7067)

假如你想要更清楚自己這次的 stash 原因是什麼，或是這是正在開發什麼功能 可以使用以下指令

範例

```
git stash save "我是註解"
git stash save -u "feature"
```

參數說明

```
-u` | `--include-untracked
-a` | `--all
```

[![alt tag](https://camo.githubusercontent.com/4710d7944aa9e7752316688f76ee4892322d58ac3e3bd490185f485736dd8ac1/68747470733a2f2f692e696d6775722e636f6d2f6e4753313150782e6a7067)](https://camo.githubusercontent.com/4710d7944aa9e7752316688f76ee4892322d58ac3e3bd490185f485736dd8ac1/68747470733a2f2f692e696d6775722e636f6d2f6e4753313150782e6a7067)

接下來你可以使用 status 指令，你會發現變乾淨了

[![alt tag](https://camo.githubusercontent.com/f00defff9acdfa83260505248192a76897616c323d4f639da9c424de3ab0a684/68747470733a2f2f692e696d6775722e636f6d2f5866353347664d2e6a7067)](https://camo.githubusercontent.com/f00defff9acdfa83260505248192a76897616c323d4f639da9c424de3ab0a684/68747470733a2f2f692e696d6775722e636f6d2f5866353347664d2e6a7067)

並且可以使用下列的指令來觀看 stash 裡面的東西

```
git stash list
```

[![alt tag](https://camo.githubusercontent.com/0e7f33121784ed956061b08b2803a96e22bffc6b6244ea78c4c406cecdf1f4a3/68747470733a2f2f692e696d6775722e636f6d2f6a5150695969582e6a7067)](https://camo.githubusercontent.com/0e7f33121784ed956061b08b2803a96e22bffc6b6244ea78c4c406cecdf1f4a3/68747470733a2f2f692e696d6775722e636f6d2f6a5150695969582e6a7067)

然後你很努力地解決這個 bug，commit 完之後， 可以再使用下列的指令把 stash 取回來，這指令取回後也會刪除 stash

```
git stash pop
```

假設今天你有很多的 stash，你可以指定，如下 (選自己喜歡的用法)

```
git stash pop 0
git stash pop stash@{0}
```

[![alt tag](https://camo.githubusercontent.com/786c335f597bbdd9e1616ad1cee9a53673f41f90896319089094efe91cd7f427/68747470733a2f2f692e696d6775722e636f6d2f7a5646376e6f322e6a7067)](https://camo.githubusercontent.com/786c335f597bbdd9e1616ad1cee9a53673f41f90896319089094efe91cd7f427/68747470733a2f2f692e696d6775722e636f6d2f7a5646376e6f322e6a7067)

你會發現剛剛的東西回來了~

如果你希望使用 stash 取回之後，不希望刪除 stash ，可以使用下列的指令

```
git stash apply
```

如下圖，你可以發現取回後， stash 並沒有被刪除

[![alt tag](https://camo.githubusercontent.com/4a0f26e3ffede6aee413e35513f7f8bab6b55503eb9f3ad954beb7bd20229b79/68747470733a2f2f692e696d6775722e636f6d2f773349703369572e6a7067)](https://camo.githubusercontent.com/4a0f26e3ffede6aee413e35513f7f8bab6b55503eb9f3ad954beb7bd20229b79/68747470733a2f2f692e696d6775722e636f6d2f773349703369572e6a7067)

如果你只是想要刪除暫存，可以使用下列的指令

```
git stash clear
```

從下圖可以發現，stash 裡面的東西被我們刪除了

[![alt tag](https://camo.githubusercontent.com/6a8959e3024f80479666d2d1e37c6d748bbc5e4c5db2711b0fc8a0df975fbc19/68747470733a2f2f692e696d6775722e636f6d2f50767a756662512e6a7067)](https://camo.githubusercontent.com/6a8959e3024f80479666d2d1e37c6d748bbc5e4c5db2711b0fc8a0df975fbc19/68747470733a2f2f692e696d6775722e636f6d2f50767a756662512e6a7067)

如果你想丟棄指定的 stash，可以使用 (選自己喜歡的用法)

```
git stash drop 0
git stash drop stash@{0}
```

## git tag

[Youtube Tutorial - git tag 教學](https://youtu.be/azciLlpr3Gs)

查看 tag

```
git tag
```

[![alt tag](https://camo.githubusercontent.com/beb68668d62e30ba845f749d6f4aa6978dd501e1270956a586ff97d03aafb427/68747470733a2f2f692e696d6775722e636f6d2f3866367a47666d2e706e67)](https://camo.githubusercontent.com/beb68668d62e30ba845f749d6f4aa6978dd501e1270956a586ff97d03aafb427/68747470733a2f2f692e696d6775722e636f6d2f3866367a47666d2e706e67)

指定關鍵字

```
git tag -l "v1.*"
-l` `--list
```

git tag 有 輕量級標籤(lightweight tag) 和 附註標籤(annotated tag).

輕量級標籤(lightweight tag)

如果想要建立一個輕量級的標籤，請不要指定 `-a` `-s`(GPG-signed) `-m`

```
git tag tag_name [commit_id]
```

如果只使用 `git tag tag_name` , 而沒加上後面 Commit id,

則會自動把 tag 放在目前的這個 Commit id 上.

顯示註解

```
git show v1.1-light
```

附註標籤(annotated tag)

```
git tag -a v1.1 -m "version 1.1"
-a` 就是標籤名稱 `--annotate
```

`-m` 代表該標籤說明(註解)

在指定的 commit 上設 tag

```
git tag -a v1.2 -m "version 1.1" [commit_id]
```

顯示註解

```
git show v1.1
```

輕量級標籤(lightweight tag) 和 附註標籤(annotated tag) 的差別就是是否能看到更多的細節,

附註標籤(annotated tag) 多了更多的資訊.

輕量級標籤(lightweight tag) 如下

[![alt tag](https://camo.githubusercontent.com/efa367e0ed84ac321102e9f3b50f79ce37ba64fa38262b712dc59f970230c03d/68747470733a2f2f692e696d6775722e636f6d2f3463556b6479512e706e67)](https://camo.githubusercontent.com/efa367e0ed84ac321102e9f3b50f79ce37ba64fa38262b712dc59f970230c03d/68747470733a2f2f692e696d6775722e636f6d2f3463556b6479512e706e67)

附註標籤(annotated tag) 如下

[![alt tag](https://camo.githubusercontent.com/3cd78827bfe46502360b438a45926f471749be1d8497032ba35c3ea05ab7c739/68747470733a2f2f692e696d6775722e636f6d2f445157423175682e706e67)](https://camo.githubusercontent.com/3cd78827bfe46502360b438a45926f471749be1d8497032ba35c3ea05ab7c739/68747470733a2f2f692e696d6775722e636f6d2f445157423175682e706e67)

當你執行 `git push` 預設是不會將 tag 推到 remote.

需要執行以下的指令, push tag 到 remote 端

```
git push origin [tagname]
```

一次 push 很多 tags (將會把你全部不在 remote 端的 tag 都 push 上去.)

```
git push origin --tags
```

當其他人執行 `git clone` 或 `git fetch` 就可以拿到這些 tags.

移除本地 tag

```
git tag -d [tagname]
```

刪除 remote tag

```
git push --delete origin [tagname]
```

## git show

一般來說，我只用他來看這個 commit 修改了哪些東西

```
git show <commit ID>
```

[![alt tag](https://camo.githubusercontent.com/8f1ff444e70f08220c2624f7d5c72e33f89c21edf8ef133059131ad77919a357/68747470733a2f2f692e696d6775722e636f6d2f726a706c38564c2e706e67)](https://camo.githubusercontent.com/8f1ff444e70f08220c2624f7d5c72e33f89c21edf8ef133059131ad77919a357/68747470733a2f2f692e696d6775722e636f6d2f726a706c38564c2e706e67)

```
git show [<options>] [<object>…]
```

其他更詳細的介紹，請參考 https://git-scm.com/docs/git-show

## git diff

以下為官方說明

```
 Show changes between commits, commit and working tree, etc
```

這邊舉幾個例子，

檔案還沒進入暫存區 ( Stage )，也就是執行 git add xxx 之前，

可以看做了那些修改，

[![alt tag](https://camo.githubusercontent.com/a927eec705b84d8866b95c1fb347e83955bc4718ded1aba4b3e387f5696c920b/68747470733a2f2f692e696d6775722e636f6d2f6e6a35477a35502e706e67)](https://camo.githubusercontent.com/a927eec705b84d8866b95c1fb347e83955bc4718ded1aba4b3e387f5696c920b/68747470733a2f2f692e696d6775722e636f6d2f6e6a35477a35502e706e67)

也可以看 commits 之間的差異

[![alt tag](https://camo.githubusercontent.com/e2df9b9035c1b58505d7ef270e4335ff2bd924956f5b972bd7d2052d6ec9e9e3/68747470733a2f2f692e696d6775722e636f6d2f4a4d4a34386a4f2e706e67)](https://camo.githubusercontent.com/e2df9b9035c1b58505d7ef270e4335ff2bd924956f5b972bd7d2052d6ec9e9e3/68747470733a2f2f692e696d6775722e636f6d2f4a4d4a34386a4f2e706e67)

其他更詳細的介紹，請參考 https://git-scm.com/docs/git-diff

## git grep

以下為官方說明

```
git-grep - Print lines matching a pattern
```

簡單說，就是可以幫你找出符合的 pattern，舉個例子，我希望找出內容

有包含 hello 這個 pattern 的檔案，這時候，就可以執行以下指令

```
git grep "hello"
```

[![alt tag](https://camo.githubusercontent.com/d0ecc21e640c7b284e5309969902b4a0327aafc6712514644845c1c8e6d39eb1/68747470733a2f2f692e696d6775722e636f6d2f743576787676702e706e67)](https://camo.githubusercontent.com/d0ecc21e640c7b284e5309969902b4a0327aafc6712514644845c1c8e6d39eb1/68747470733a2f2f692e696d6775722e636f6d2f743576787676702e706e67)

會顯示出該 pattern 在個檔案以及哪段程式碼有用到。

其他更詳細的介紹，請參考 https://git-scm.com/docs/git-grep

## git Submodule

由於這個內容稍微比較多，所以我另外寫了一篇，

- [Youtube Tutorial PART 1 - git Submodule tutorial - how to create submodule](https://youtu.be/IDMWLJCbCGo)
- [Youtube Tutorial PART 2 - git Submodule tutorial - how to update submodule](https://youtu.be/ogZoZOVyAYI)
- [Youtube Tutorial PART 3 - git Submodule tutorial - how to clone submodule](https://youtu.be/f5_O5Iu6pJo)
- [Youtube Tutorial PART 4 - git Submodule tutorial - how to remove submodule](https://youtu.be/imndFN7AvFA)

[git Submodule tutorial 📝](https://github.com/twtrubiks/Git-Tutorials/blob/master/git_submodule_turorial.md)

## git Subtree

由於這個內容稍微比較多，所以我另外寫了一篇，

- [Youtube Tutorial PART 1 - git subtree tutorial - how to create subtree](https://youtu.be/kEvgK2gH_vg)
- [Youtube Tutorial PART 2 - git subtree tutorial - how to push subtree](https://youtu.be/Df3zc1VOqN8)
- [Youtube Tutorial PART 3 - git subtree tutorial - how to pull/create subtree](https://youtu.be/dE-D2yrD4ws)

[git subtree tutorial 📝](https://github.com/twtrubiks/Git-Tutorials/blob/master/git_subtree_turorial.md)

## git 其他設定

我們已經設定了 user.name 以及 user.email ，但 Git 上其實還有很多可設定的東西

有時候，我們必須把某些檔案 ( 文件夾 ) 放到 Git 工作目錄中，但又不能提交它們，

像是密碼設定或是編譯器 IDE 產生出來的東西之類的，

每次 git status 都會看到紅紅的 Untracked files ，通常會覺得有點煩......

這問題 Git 也幫我們想過，只要在 Git 工作區的根目錄下新建一個特殊的 **.gitignore** 文件 ，

然後把要忽略的文件 ( 檔案 ) 名稱輸入進去， Git 就會自動忽略這些文件。

當然不需要自己從頭寫 .gitignore 文件， GitHub 已經幫我們準備了一些文件 [gitignore](https://github.com/github/gitignore)

**.gitignore** 檔案直接放在目錄底下即可

[![alt tag](https://camo.githubusercontent.com/1e306e19be845de36c941cd0e6060ff22bad5b14bd7574298fb1660d07f93a94/68747470733a2f2f692e696d6775722e636f6d2f387248507349492e6a7067)](https://camo.githubusercontent.com/1e306e19be845de36c941cd0e6060ff22bad5b14bd7574298fb1660d07f93a94/68747470733a2f2f692e696d6775722e636f6d2f387248507349492e6a7067)

### .gitignore 檔案格式範例

[![alt tag](https://camo.githubusercontent.com/a6dfa2b18651a782b2945a654d4e6a13ab614ef7b435a5cfc1b623b6acbd3434/68747470733a2f2f692e696d6775722e636f6d2f573363786b39722e6a7067)](https://camo.githubusercontent.com/a6dfa2b18651a782b2945a654d4e6a13ab614ef7b435a5cfc1b623b6acbd3434/68747470733a2f2f692e696d6775722e636f6d2f573363786b39722e6a7067)

### .gitignore (Temporarily and Permanently)

主要分 暫時(Temporarily) 和 永久(Permanently) 的ignore，

- Temporarily ignore

適合使用在 settings 的檔案，有時候我們在開發的時候，都會有自己的設定，

但這個設定未必是大家都需要的，這時候就可以暫時先忽略這個檔案的改變。

暫時忽略某個檔案

```
git update-index --skip-worktree <file>
```

恢復(Resume)暫時忽略某個檔案

```
git update-index --no-skip-worktree <file>
```

- Permanently ignore

這邊補充一個情境，假設今天 file 這個檔案已經被 commit 到 git 中了，

但是我想把他加入 .gitignore，這樣該怎麼辦❓

如果你在 .gitignore 中加入 file，你會發現還是沒有被 ignore😕

[![alt tag](https://camo.githubusercontent.com/77d13851026efd53556df3604d58d227cb6adecd2c5f819c70a89eba655cf827/68747470733a2f2f692e696d6775722e636f6d2f6f3932327061612e706e67)](https://camo.githubusercontent.com/77d13851026efd53556df3604d58d227cb6adecd2c5f819c70a89eba655cf827/68747470733a2f2f692e696d6775722e636f6d2f6f3932327061612e706e67)

這時候，正確的做法應該是要先執行已下指令，

```
git rm --cached <file>
```

執行完後再 commit 即可 ( 檔案不會從系統上刪除，只是要更新 git 的 index 而已 )

[![alt tag](https://camo.githubusercontent.com/f761ab10a77d7efd0b3c2947eb04a1fbe39ec908974d8fa1ee419d70d87202f4/68747470733a2f2f692e696d6775722e636f6d2f524a5a30384f512e706e67)](https://camo.githubusercontent.com/f761ab10a77d7efd0b3c2947eb04a1fbe39ec908974d8fa1ee419d70d87202f4/68747470733a2f2f692e696d6775722e636f6d2f524a5a30384f512e706e67)

這時候可以再嘗試更新 file 的內容，你會發現它成功被 ignore 了😄

### git alias

有時候常常手殘 key 錯指令或是記不起來

如果我們打 git st 就表示 git status 那該有多棒!!!

所以我們可以自己設定，讓 Git 以後打 **git st = git status** 如下圖，原本不能使用 git st ，設定完之後就可以使用了。

```
git config --global alias.st status
```

[![alt tag](https://camo.githubusercontent.com/b55b2a218f469c70ac05b045fea919981dccdb09602e59bc9918d8817d052c40/68747470733a2f2f692e696d6775722e636f6d2f344e4e617367422e6a7067)](https://camo.githubusercontent.com/b55b2a218f469c70ac05b045fea919981dccdb09602e59bc9918d8817d052c40/68747470733a2f2f692e696d6775722e636f6d2f344e4e617367422e6a7067)

```
git config --global alias.br branch
```

[![alt tag](https://camo.githubusercontent.com/838655e0cdf2619fe75d31aaa63dd3c20f7c9a0d6465f03b7e8a70226f8bf733/68747470733a2f2f692e696d6775722e636f6d2f4e49633731414f2e6a7067)](https://camo.githubusercontent.com/838655e0cdf2619fe75d31aaa63dd3c20f7c9a0d6465f03b7e8a70226f8bf733/68747470733a2f2f692e696d6775722e636f6d2f4e49633731414f2e6a7067)

```
git config --global alias.ck checkout
git config --global alias.cm commit
git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative"
```

將前面這一大串變成一個別名，這樣以後只需要執行 `git lg` 即可，

[![alt tag](https://camo.githubusercontent.com/badd0743645a8ce9a127b3713ea59260a3f84e3bd23f712549fda1967adafe07/68747470733a2f2f692e696d6775722e636f6d2f4976514c734d522e706e67)](https://camo.githubusercontent.com/badd0743645a8ce9a127b3713ea59260a3f84e3bd23f712549fda1967adafe07/68747470733a2f2f692e696d6775722e636f6d2f4976514c734d522e706e67)

可能有人會問，那這個設定檔文件在哪裡呢?

通常會在你的使用者底下，例如我這台電腦使用者為 HJ，設定檔文件就會在 **C:\Users\HJ** 底下，

他是一個 **隱藏文件.gitconfig** ，打開他的話格式如下。

[![alt tag](https://camo.githubusercontent.com/b6bdee07a47e0486c49309bce0614dd6a42f03dae6f22b76d694602305411e70/68747470733a2f2f692e696d6775722e636f6d2f69586a497176392e6a7067)](https://camo.githubusercontent.com/b6bdee07a47e0486c49309bce0614dd6a42f03dae6f22b76d694602305411e70/68747470733a2f2f692e696d6775722e636f6d2f69586a497176392e6a7067)

不知道大家有沒有注意到 `--global` 這個參數，他代表的意思是全域的，如果說你今天是執行

```
git config alias.stu status
```

代表只有在該目錄底下時才會有作用。

那這個有什麼用呢？ 試想一種情境，假設你在特定的資料夾底下，想要使用特定的信箱去 push，而其他的資料夾，

則一樣使用公司的信箱，這時候，就非常適合使用這種方法完成。

更多資訊細節可使用以下命令查看

```
man git-config
```

### git 更新

```
sudo add-apt-repository ppa:git-core/ppa
sudo apt-get update
sudo apt-get install git
```

[![alt tag](https://camo.githubusercontent.com/6af9ba178ee8b4b918baac1b93c2ef4fccfd7713f9d0a708c56f48c9437d1ae8/68747470733a2f2f692e696d6775722e636f6d2f5772514e5a6c6e2e706e67)](https://camo.githubusercontent.com/6af9ba178ee8b4b918baac1b93c2ef4fccfd7713f9d0a708c56f48c9437d1ae8/68747470733a2f2f692e696d6775722e636f6d2f5772514e5a6c6e2e706e67)

## 使用 Git 一次 Push 到多個不同的遠端 ( remote )

假如有一天 github 掛了，這樣是不是就不能 work 了，你可能會說本地端還有 ?

但......多備份絕對是好事 !! 再這裡介紹如何一次 Push 到多個不同的遠端 ( remote )

這裡用 [Bitbucket](https://bitbucket.org/product) 當作範例

先使用下方指令查看

```
git remote -v
```

[![alt tag](https://camo.githubusercontent.com/bc52b026fdef49acd282eaf4f4d4070cf2cc230bf60671f9b4edcb91045b044c/68747470733a2f2f692e696d6775722e636f6d2f51623556486f502e706e67)](https://camo.githubusercontent.com/bc52b026fdef49acd282eaf4f4d4070cf2cc230bf60671f9b4edcb91045b044c/68747470733a2f2f692e696d6775722e636f6d2f51623556486f502e706e67)

git remote 這個指令的更多說明可參考官方文件 [git-remote](https://git-scm.com/docs/git-remote)。

接著我們使用下列指令新增一個 origin 的遠端

```
git remote set-url --add origin <url>
git remote set-url --add origin git@github.com:twtrubiks/test2.git
```

[![alt tag](https://camo.githubusercontent.com/cc17d8b7c7f8eca9ff689f7972f286d369f38c3447e1b1e20a609e9855842980/68747470733a2f2f692e696d6775722e636f6d2f464b7a657856452e706e67)](https://camo.githubusercontent.com/cc17d8b7c7f8eca9ff689f7972f286d369f38c3447e1b1e20a609e9855842980/68747470733a2f2f692e696d6775722e636f6d2f464b7a657856452e706e67)

我們再用 git remote -v 查看一次，你會發現多了剛剛新增的遠端 ( remote )

[![alt tag](https://camo.githubusercontent.com/8995c3db68638d2997361509b6b860459fd51870a8cd83093748b366bf2bf7ec/68747470733a2f2f692e696d6775722e636f6d2f703171374334622e706e67)](https://camo.githubusercontent.com/8995c3db68638d2997361509b6b860459fd51870a8cd83093748b366bf2bf7ec/68747470733a2f2f692e696d6775722e636f6d2f703171374334622e706e67)

最後我們再 push

[![alt tag](https://camo.githubusercontent.com/4ff47c28f7a21eaea297c429e9c3e411e323f46728dfaedcdc6b2a285d6046e8/68747470733a2f2f692e696d6775722e636f6d2f36564b6838427a2e706e67)](https://camo.githubusercontent.com/4ff47c28f7a21eaea297c429e9c3e411e323f46728dfaedcdc6b2a285d6046e8/68747470733a2f2f692e696d6775722e636f6d2f36564b6838427a2e706e67)

仔細看，是不是一次 push 到多個不同的遠端 ( remote )，非常方便!!

***GitHub***

[![alt tag](https://camo.githubusercontent.com/8a2ef97a99dda56c0efb21eb014e9353a56e45f782c4b302e5063e37b6ae416a/68747470733a2f2f692e696d6775722e636f6d2f4a6c6a504a484a2e706e67)](https://camo.githubusercontent.com/8a2ef97a99dda56c0efb21eb014e9353a56e45f782c4b302e5063e37b6ae416a/68747470733a2f2f692e696d6775722e636f6d2f4a6c6a504a484a2e706e67)

***Bitbucket***

[![alt tag](https://camo.githubusercontent.com/fb621df0f88ea218ac6d7e8d6b2036c6d4c65ae2ebf3e29a997f3935799bf250/68747470733a2f2f692e696d6775722e636f6d2f726b59484e6c342e706e67)](https://camo.githubusercontent.com/fb621df0f88ea218ac6d7e8d6b2036c6d4c65ae2ebf3e29a997f3935799bf250/68747470733a2f2f692e696d6775722e636f6d2f726b59484e6c342e706e67)

P.S 設定檔在資料夾底下的隱藏檔 ".git" 底下，裡面有一個 config

[![alt tag](https://camo.githubusercontent.com/5c1d820c119537fe651de1deeeafe984252e966770b482036add874cc057d156/68747470733a2f2f692e696d6775722e636f6d2f343178623865752e706e67)](https://camo.githubusercontent.com/5c1d820c119537fe651de1deeeafe984252e966770b482036add874cc057d156/68747470733a2f2f692e696d6775722e636f6d2f343178623865752e706e67)

補充幾個 git remote 的指令，他也支援 rename 以及 remove ，

現在的 remote 如下，

[![alt tag](https://camo.githubusercontent.com/ad57cf535c99717da6078d7e266da41f99d5265a816a1a002ea421553180a771/68747470733a2f2f692e696d6775722e636f6d2f727239534533672e706e67)](https://camo.githubusercontent.com/ad57cf535c99717da6078d7e266da41f99d5265a816a1a002ea421553180a771/68747470733a2f2f692e696d6775722e636f6d2f727239534533672e706e67)

讓我們重新命名 remote，語法如下，

```
git remote rename <old> <new>
git remote rename origin2 origin
```

執行後，你會發現 remote 成功被修改成 origin 了，

[![alt tag](https://camo.githubusercontent.com/cfceceba1b41cf497d2f2c38c31d9902f878439f3723d6a2f22b988a017b0775/68747470733a2f2f692e696d6775722e636f6d2f6978503148375a2e706e67)](https://camo.githubusercontent.com/cfceceba1b41cf497d2f2c38c31d9902f878439f3723d6a2f22b988a017b0775/68747470733a2f2f692e696d6775722e636f6d2f6978503148375a2e706e67)

接下來我們試試 remove，語法如下，

```
git remote remove <name>
git remote remove origin
```

成功刪除，現在 remote 是空的了，

[![alt tag](https://camo.githubusercontent.com/af96961be9f577d70c7c09d37223a88f002c00f8eb56f4a5a767ca6e46a2319c/68747470733a2f2f692e696d6775722e636f6d2f4f5146525744672e706e67)](https://camo.githubusercontent.com/af96961be9f577d70c7c09d37223a88f002c00f8eb56f4a5a767ca6e46a2319c/68747470733a2f2f692e696d6775722e636f6d2f4f5146525744672e706e67)

接下來我們嘗試新增一個 remote，指令如下，

```
git remote add [-t <branch>] [-m <master>] [-f] [--[no-]tags] [--mirror=<fetch|push>] <name> <url>
git remote add origin git@github.com:blue-rubiks/t11.git
```

[![alt tag](https://camo.githubusercontent.com/491fee9938c02c45f4750c6066a0e2f25c1140bf94d653211ed5cf1fc4116d03/68747470733a2f2f692e696d6775722e636f6d2f634b73694242732e706e67)](https://camo.githubusercontent.com/491fee9938c02c45f4750c6066a0e2f25c1140bf94d653211ed5cf1fc4116d03/68747470733a2f2f692e696d6775722e636f6d2f634b73694242732e706e67)

如果我們想修改 origin 的 url，可以使用

```
git remote set-url origin git@blue.github.com:blue-rubiks/t11.git
```

[![alt tag](https://camo.githubusercontent.com/a23b8e8f702cb6e8deae3b5fbfb717c5ebe0059923de30b3332f0a504c52ec1d/68747470733a2f2f692e696d6775722e636f6d2f4c4a4943544e4d2e706e67)](https://camo.githubusercontent.com/a23b8e8f702cb6e8deae3b5fbfb717c5ebe0059923de30b3332f0a504c52ec1d/68747470733a2f2f692e696d6775722e636f6d2f4c4a4943544e4d2e706e67)

## Multiple SSH Keys settings for different github account

- [Youtube Tutorial - Multiple SSH Keys settings for different github account](https://youtu.be/gDxG-4tF7B8)

[Multiple SSH Keys settings for different github account](https://github.com/twtrubiks/Git-Tutorials/blob/master/Multiple_SSH_Keys_settings.md)

## Git-Flow 基本教學以及概念

- [Git-Flow Tutorials - youtube](https://youtu.be/zXlta66thZY)
- [Git-Flow SmartGit Tutorials - youtube](https://youtu.be/ualXHytifbg)

[Git-Flow 基本教學以及概念](https://github.com/twtrubiks/Git-Tutorials/tree/master/Git-Flow)

## PR (Pull Request) 教學

- [Youtube Tutorial - github PR (Pull Request) 教學](https://youtu.be/bXOdD-bKfkA) - [文章快速連結](https://github.com/twtrubiks/Git-Tutorials/tree/master/pr-tutorial#github-pr-pull-request-教學)
- [Youtube Tutorial - github CLI PR 教學](https://youtu.be/AD8X11lq3gQ) - [文章快速連結](https://github.com/twtrubiks/Git-Tutorials/tree/master/pr-tutorial#github-cli-pr-教學)

[PR (Pull Request) 教學](https://github.com/twtrubiks/Git-Tutorials/tree/master/pr-tutorial)

## Linux 注意事項

- [Youtube Tutorial - Linux 教學 - git 乎略 file mode (chmod) 改變](https://youtu.be/QCh2k903Yak)

這邊是和大家說一些同時在 windows 以及 linux 底下使用 git 可能會遇到的問題.

首先, 在 linux 底下執行以下指令

```
sudo chmod -R 777 folder
```

git 會默認它為改變, 要怎麼把它忽略呢 ? 請執行以下指令 ,

```
git config core.fileMode false
```

也可參考這篇文章 [Git ignore file mode (chmod) changes](https://stackoverflow.com/questions/1580596/how-do-i-make-git-ignore-file-mode-chmod-changes)

### 格式化

```
core.autocrlf
```

Windows 使用 Enter (Carriage Return 簡寫為 CR) 和 換行(Line Feed 簡寫為 LF) 這兩個字元來定義換行,

而 Mac 和 Linux 只使用一個換行 (Line Feed 簡寫為 LF) 字元.

所以會導致跨平台協作時出問題.

在 windows 上可以這樣設定 ( 代表 LF 會被轉換成 CRLF)

```
git config --global core.autocrlf true
```

Linux 或 Mac 系統

```
git config --global core.autocrlf input
```

以上這樣設定, 會在 Windows 上保留 CRLF，而在 Mac 和 Linux 以及 repo 中保留 LF.

如果你想更深入的了解, 可參考 [格式化-core.autocrlf](https://git-scm.com/book/zh-tw/v1/Git-客製化-Git-設定#格式化與空格).

### 修改 editor

```
git config --global core.editor "vim"
```