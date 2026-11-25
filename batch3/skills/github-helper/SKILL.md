---
name: github-helper
description: 當使用者說「放上 GitHub」「做成公開網頁」「再放一次」「回到上一版」「給我網址」時，用 git 和 gh 把他的作品放到工作坊的 GitHub（Arnold-leyan）底下，並打開 GitHub Pages 給他一個公開網頁網址。
---

# GitHub 小幫手

這台主機只登入一個 GitHub 帳號：主辦方的 **Arnold-leyan**。所有學員的作品都放在 github.com/Arnold-leyan/ 底下，用學員的英文名字當 repo 名稱的開頭來區分。

## 第一次用
1. 問使用者的英文名字，轉成英文小寫，只留字母、數字和連字號（例：jason、amy-chang），當作他的「名字開頭」，記在你的記憶裡。之後他的每個 repo 都叫「名字開頭-內容」（例：jason-liming-office）。
2. 問他早上辦好的 GitHub 帳號名稱（沒有也可以跳過），記起來，放上去之後把他加成那個 repo 的協作者。

## 每次開始前
1. 執行 `gh api user --jq .login`，結果必須是 `Arnold-leyan`。不是、或顯示沒登入，就停下來告訴使用者「主機的 GitHub 登入有問題，請找主辦方」。
2. 絕對不要執行 `gh auth login`、`gh auth logout`、`gh auth switch`，也不要向使用者要密碼或登入代碼。登入由主辦方處理。

## 放上去（新的 repo）
1. 確認要放的資料夾。repo 名稱由你來取，使用者不用給：「名字開頭-內容的英文短名」（例：jason-liming-office），英文小寫、用連字號。使用者自己指定了名稱，也一樣在前面加上他的名字開頭。
2. 這個帳號很多人共用，名稱可能撞到。先用 `gh repo view Arnold-leyan/<repo 名稱>` 確認還沒被用過；已經有了，就在後面加 -2、-3……直到找到沒人用過的名稱。絕對不要推到已經存在的 repo。
   - 記住這個資料夾放到哪個 repo，之後使用者說「再放一次」就用同一個。
3. 在資料夾裡建立 `.gitignore`，至少寫入 `.env`。網頁的主檔要叫 `index.html`、放在資料夾最上層；不是的話先改名，網頁裡照片的路徑也要跟著對。
4. 安全檢查：列出要上傳的檔案。檔名或內容出現 .env、token、password、密碼、身分證、保單，就先停下來告訴使用者，不要上傳。
5. 用一句話告訴使用者：要上傳哪些檔案、要放到哪個 repo 名稱，以及「這會公開，任何人都看得到」。使用者說好之前，不要上傳。
6. `git init`（已經是 repo 就跳過）。只在這個資料夾裡設定作者（不要用 --global）：`git config user.name "<使用者的英文名字>"`；用 `gh api user --jq .id` 取得 id，`git config user.email "<id>+Arnold-leyan@users.noreply.github.com"`。
7. `git add -A` → `git commit -m "<一句話說明>"` → `gh repo create Arnold-leyan/<repo 名稱> --public --source . --push`。推送時如果要求輸入帳號密碼，停下來請使用者找主辦方。
8. 打開公開網頁（GitHub Pages）：用 `git branch --show-current` 取得分支名稱，執行 `gh api -X POST repos/Arnold-leyan/<repo 名稱>/pages -f "source[branch]=<分支名稱>" -f "source[path]=/"`。等大約 1 分鐘，用 `gh api repos/Arnold-leyan/<repo 名稱>/pages --jq .status` 確認變成 `built`。
9. 使用者給過自己的 GitHub 帳號的話：`gh api -X PUT repos/Arnold-leyan/<repo 名稱>/collaborators/<他的帳號> -f permission=push`，告訴他「GitHub 會寄邀請信給你，按接受就能自己改」。
10. 回覆兩個網址：
   - 公開網頁：https://arnold-leyan.github.io/<repo 名稱>/ （手機、電腦都打得開，給別人看就給這個）
   - 程式碼：https://github.com/Arnold-leyan/<repo 名稱>

## 再放一次
1. 到同一個資料夾，用 `git remote get-url origin` 確認這是 Arnold-leyan/「這位使用者的名字開頭-…」的 repo。不是就停下來。
2. 用 `git status` 看改了哪些檔案，列給使用者，做同樣的安全檢查。
3. `git add -A` → `git commit -m "<使用者說的這次改了什麼>"` → `git push`。
4. 告訴使用者：公開網頁大約 1 分鐘後更新，網址不變。

## 回到上一版
1. 一樣先確認 repo 是這位使用者的。
2. 用 `git log --oneline -5` 列出最近幾版，用白話說明每一版改了什麼。
3. 使用者選好要回到哪一版後，用 `git revert --no-edit <那一版>..HEAD`，再 `git push`。保留紀錄，不要用 reset。

## 絕對不做
- 只動名稱以這位使用者「名字開頭」開頭的 repo。別的學員的 repo、Arnold-leyan 原本就有的 repo，一個都不碰。
- 不刪除任何 repo，不把 public 改成 private 或反過來。
- 不執行 `gh auth login`、`logout`、`switch`，不在對話裡顯示 token。
- 不用 `git push --force`。
