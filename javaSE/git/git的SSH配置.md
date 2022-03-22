## 前言

有時候我們可能會遇到這種情境，就是同一台電腦上同時需要有多個 github 帳號，為什麼呢?

當你工作的電腦和你自己的 side project 開發都在同一台電腦的時候，你就需要這篇文章的幫忙

了 😙

相信我，你不會希望用公司的帳號推 commit 到自己的 side project 上😅

## 教學

第一步，先產生 ssh key，如果你不知道怎麼產生，

可參考 [Generating a new SSH key](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) 或是 [github基本教學 - 從無到有](https://www.youtube.com/watch?v=py3n6gF5Y00)，

打開你的 Git Bash，

```
ssh-keygen -t rsa -b 4096 -C "blue555236sa56@gmail.com"
```

( 請將信箱換成自己的 )

這裡的重點是記得自行輸入新的 rsa 名稱，像是下圖的 `/c/Users/twtrubiks/.ssh/id_rsa_rubik` 這樣，

這樣才不會覆蓋舊有的 rsa，

[![alt tag](https://camo.githubusercontent.com/c63976b821aeaa4d3d6d5219c9cb4ba53e32139ff85304b9989080b8a5d9cb6b/68747470733a2f2f692e696d6775722e636f6d2f324d73723531552e706e67)](https://camo.githubusercontent.com/c63976b821aeaa4d3d6d5219c9cb4ba53e32139ff85304b9989080b8a5d9cb6b/68747470733a2f2f692e696d6775722e636f6d2f324d73723531552e706e67)

接下來，你的 `.ssh` 路徑底下會多出你剛剛建立的檔案

[![alt tag](https://camo.githubusercontent.com/6f31457975f57c9f2c8f7293e5072651e3e80325e170be6c47fbf34e04c5fddd/68747470733a2f2f692e696d6775722e636f6d2f4234673946514f2e706e67)](https://camo.githubusercontent.com/6f31457975f57c9f2c8f7293e5072651e3e80325e170be6c47fbf34e04c5fddd/68747470733a2f2f692e696d6775722e636f6d2f4234673946514f2e706e67)

再把你的 `id_rsa_rubik.pub` key 貼到你的 gihub 上，

[![alt tag](https://camo.githubusercontent.com/3c77cb0b61daa221c56ff1a58d1b7d2a18a35439ffee117e84fb66f17f8b41f6/68747470733a2f2f692e696d6775722e636f6d2f7466517a46634a2e706e67)](https://camo.githubusercontent.com/3c77cb0b61daa221c56ff1a58d1b7d2a18a35439ffee117e84fb66f17f8b41f6/68747470733a2f2f692e696d6775722e636f6d2f7466517a46634a2e706e67)

通常你新增成功的時候，信箱會收到一封信。

再輸入下方指令

```
ssh-agent -s
```

上面這個指令一定要執行，如果跳過這個步驟，就會出現以下錯誤，

Could not open a connection to your authentication agent.

所以請記得一定要執行。

在 `.ssh` 目錄底下建立一個名稱為 `config` 的檔案 ( 檔名就是 `config`，副檔名不用填 )，

[![alt tag](https://camo.githubusercontent.com/ba9701dfb52248dc1e10ebea8b9f922a0aa0d3eac6f32d7a8611716fd66b96eb/68747470733a2f2f692e696d6775722e636f6d2f534a4278726f362e706e67)](https://camo.githubusercontent.com/ba9701dfb52248dc1e10ebea8b9f922a0aa0d3eac6f32d7a8611716fd66b96eb/68747470733a2f2f692e696d6775722e636f6d2f534a4278726f362e706e67)

下方是一個範例，

```
# github - twtrubiks
    Host github.com
    HostName github.com
    IdentityFile ~/.ssh/id_rsa
    IdentitiesOnly yes
    User twtrubiks

# github - blue-rubiks
    Host blue.github.com
    HostName github.com
    IdentityFile ~/.ssh/id_rsa_rubik
    IdentitiesOnly yes
    User blue-rubiks
```

可用以下指令測試，如果顯示出自己的使用者名稱，代表設定成功

```
ssh -T git@github.com
```

[![alt tag](https://camo.githubusercontent.com/fffc72fcabe9733d41f3021c3d191904d3e5b980fe72d39f5471cfdbf05c59f0/68747470733a2f2f692e696d6775722e636f6d2f72644c663469582e706e67)](https://camo.githubusercontent.com/fffc72fcabe9733d41f3021c3d191904d3e5b980fe72d39f5471cfdbf05c59f0/68747470733a2f2f692e696d6775722e636f6d2f72644c663469582e706e67)

```
ssh -T git@blue.github.com
```

[![alt tag](https://camo.githubusercontent.com/1e0dc6388e3bd5722626c07e8bd7dd1b733b027f1a7941cd405fc4dcedf6cd48/68747470733a2f2f692e696d6775722e636f6d2f59544e4850664e2e706e67)](https://camo.githubusercontent.com/1e0dc6388e3bd5722626c07e8bd7dd1b733b027f1a7941cd405fc4dcedf6cd48/68747470733a2f2f692e696d6775722e636f6d2f59544e4850664e2e706e67)

ya 😍 我們設定成功惹~

如果有問題，也可以用 debug 模式看問題出在哪裡，通常是 `config` 輸入有誤

```
ssh -vT git@github.com
```

接下來先和大家說明一下，

```
git config --global user.name "blue-rubiks"
git config --global user.email "blue555236sa56@gmail.com"
```

`--global` 顧名思義就是全域的意思，如果沒輸入，就代表是目錄底下的 repo 會生效，

現在我們 clone 一個 repo 來玩玩看 (你可以自己建立一個)

```
git clone git@github.com:blue-rubiks/github-ssh-test.git
```

接著，切換到目錄資料夾底下，輸入以下指令

```
git config user.name "blue-rubiks"
git config user.email "blue555236sa56@gmail.com"
```

注意，這邊沒加上 `--global`，所以只有該目錄底下的 repo 會生效，

我們找到 repo 中的 `.git` 資料夾 , 再找到 `config` 檔案，內容大致如下

[![alt tag](https://camo.githubusercontent.com/52f7bfa77a277ef8a3d0f5e0fee4f609b0c11c544d60d9a096c559d4b8bb6403/68747470733a2f2f692e696d6775722e636f6d2f765436476959522e706e67)](https://camo.githubusercontent.com/52f7bfa77a277ef8a3d0f5e0fee4f609b0c11c544d60d9a096c559d4b8bb6403/68747470733a2f2f692e696d6775722e636f6d2f765436476959522e706e67)

主要就是修改第 9 行成第 10 行，修改為你設定的 `Host`，

最後，可以安心 commit 以及 push 了。

如果你真的聽不懂我在說什麼，建議看影片說明，我會帶大家操作一波 😆