---
title: GitHub 多組帳號與SSH key管理
date: 2021-12-25 21:43:11
updated: 2021-12-25 21:43:11
excerpt: 你也有Github多組帳號切換的問題嗎？同一台電腦怎麼分別管理公司和個人GitHub帳號？新生成的SSH key檔案要怎麼樣才不會覆蓋原本的檔案，來建立一個config管理Github多組帳號吧！
categories:
  - GitHub
---

## 當你需要用同一台電腦使用多組 GitHub 帳號

後疫情時代，人人都可能在家裡遠距上班，如果公司沒有給你公司用的電腦
同一台電腦該怎麼讓 GitHub 辨識兩個身份呢？
你說辦第 2 組 GitHub 帳號就好了吧（就跟 google 帳號一樣？  
但如果使用 GitHub 的新帳號開一個 Repo，在 terminal 依序輸入下方指令

```
git init
git add .
git commit -m 'some msg'
git branch -M main
git remote add origin 'your repo'
git push -u origin main
```

就會在最後一步跳出這個訊息

> ERROR: Permission to userB/repo.git denied to userA
> fatal: 無法讀取遠端版本庫。

commit 的時候被最初註冊的 userA 帳號 deny

Um...啊！  
<img src="./cat.jpg" alt="cat-meme" width="400"/>  
之前的帳號有設定一組對應的 SSH key 讓 GitHub 驗證身份，所以應該是 SSH key 的問題吧？  
有 2 組帳號，生 2 組 key 應該就沒問題了吧！  
欸不對，我的 terminal 在 git push 的時候，怎麼知道哪組帳號對應哪個 SSH key？

估狗後發現，其實可以寫一個 config 來讓不同帳號對應各自的 key

## 多組 SSH key 設置流程

下方使用者名稱以 aaa 為範例（可自由代入）

1. 產生 SSH key `ssh-keygen -t rsa -C "aaa@example.com"`
2. 決定要儲存的檔名(路徑)
   在 Enter file in which to save the key (/Users/XXX/.ssh/id_rsa):  
   後方填入 `/Users/aaa/.ssh/id_rsa_aaa`  
   \*如果沒有新的命名，就會覆蓋原本的 id_rsa
3. Enter passphrase(看個人，可以直接 enter 不設定密碼)
4. Enter same passphrase again(如果有設定要再輸入一次，沒有就直接 enter)
5. 把對應的公鑰(id_rsa_aaa.pub 裡面的文字)新增到 GitHub 的 SSH key，[官方圖文教學](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)
6. 在.ssh 目錄下，touch 一個 config(不用副檔名)

```
Host gh.aaa
HostName github.com
User aaa
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa_aaa
```

7. 把 key 加入管理 `ssh-add ~/.ssh/id_rsa_aaa`
8. 嘗試連線看看對應 host 是否有生效`ssh -T git@gh.aaa`
   > Hi aaa! You've successfully authenticated, but GitHub does not provide shell access.

看到上方訊息表示有成功連線囉！  
第 2 個帳號照著上方步驟寫在同個 config 檔案即可

```
Host gh.aaa
HostName github.com
User aaa
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa_aaa

Host gh.bbb
HostName github.com
User bbb
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa_bbb
```

注意：經過上面的設定檔後，repo 的位置會從預定的
`git@github.com:ooo/ooo.git` 變成 `git@gh.aaa:ooo/ooo.git`
後續在新增 repo 連結時需注意，不然可能吃不到 config 設定檔唷

參考文章:
[[Git] 多個 SSH Key 與帳號的設定(Mac)](https://dotblogs.com.tw/as15774/2018/04/30/174737 "[[Git] 多個SSH Key與帳號的設定(Mac)")
