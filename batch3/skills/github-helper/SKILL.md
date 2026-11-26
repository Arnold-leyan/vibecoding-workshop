---
name: github-helper
description: 當使用者說「放上 GitHub」「做成公開網頁」「再放一次」「回到上一版」「給我網址」「加協作者」時，用 git 和 gh 把他的作品放到「他自己的」GitHub 帳號底下，並打開 GitHub Pages 給他一個公開網頁網址。
---

# GitHub 小幫手

每位學員的工作區已經連上**他自己的** GitHub 帳號。作品放在他自己的帳號底下：github.com/<他的帳號>/<repo 名稱>。

## 每次開始前
1. 執行 `gh api user --jq .login`，拿到這個工作區登入的帳號名稱，記作 <帳號>。
   - 第一次用時，跟使用者確認一句：「你的 GitHub 帳號是 <帳號>，對嗎？」他說不對就停下來，請他找主講。
   - 顯示沒登入或出錯，就停下來告訴使用者「這個工作區的 GitHub 還沒連好，請找主講」。
2. 絕對不要執行 `gh auth login`、`gh auth logout`、`gh auth switch`，也不要向使用者要密碼、token 或登入代碼。登入由主講處理。

## 放上去（新的 repo）
1. 確認要放的資料夾。repo 名稱由你來取：內容的英文短名，英文小寫、用連字號（例：liming-office、quiz-game）。使用者自己指定了名稱就用他的。
2. 先用 `gh repo view <帳號>/<repo 名稱>` 確認還沒有同名的 repo；已經有了，就問使用者要「更新那一個」還是「換個名字」，不要直接蓋過去。
   - 記住這個資料夾放到哪個 repo，之後使用者說「再放一次」就用同一個。
3. 在資料夾裡建立 `.gitignore`，至少寫入 `.env`。網頁的主檔要叫 `index.html`、放在資料夾最上層；不是的話先改名，網頁裡照片、影片、遊戲的路徑也要跟著對。
4. 安全檢查：列出要上傳的檔案。檔名或內容出現 .env、token、password、密碼、金鑰、身分證、保單、客戶真實姓名，就先停下來告訴使用者，不要上傳。
5. 用一句話告訴使用者：要上傳哪些檔案、要放到哪個 repo，以及「這會公開，任何人都看得到」。使用者說好之前，不要上傳。
6. `git init`（已經是 repo 就跳過）。只在這個資料夾裡設定作者（不要用 --global）：`git config user.name "<帳號>"`；用 `gh api user --jq .id` 取得 id，`git config user.email "<id>+<帳號>@users.noreply.github.com"`。
7. `git add -A` → `git commit -m "<一句話說明>"` → `gh repo create <帳號>/<repo 名稱> --public --source . --push`。推送時如果要求輸入帳號密碼，停下來請使用者找主講。
8. 打開公開網頁（GitHub Pages）：用 `git branch --show-current` 取得分支名稱，執行 `gh api -X POST repos/<帳號>/<repo 名稱>/pages -f "source[branch]=<分支名稱>" -f "source[path]=/"`。等大約 1 分鐘，用 `gh api repos/<帳號>/<repo 名稱>/pages --jq .status` 確認變成 `built`。
9. 回覆兩個網址：
   - 公開網頁：https://<帳號小寫>.github.io/<repo 名稱>/ （手機、電腦都打得開，給別人看就給這個）
   - 程式碼：https://github.com/<帳號>/<repo 名稱>

## 再放一次
1. 到同一個資料夾，用 `git remote get-url origin` 確認這是 <帳號> 底下的 repo。不是就停下來。
2. 用 `git status` 看改了哪些檔案，列給使用者，做同樣的安全檢查。
3. `git add -A` → `git commit -m "<使用者說的這次改了什麼>"` → `git push`。
4. 告訴使用者：公開網頁大約 1 分鐘後更新，網址不變。

## 加協作者（讓夥伴一起改）
1. 問夥伴的 GitHub 帳號名稱。
2. `gh api -X PUT repos/<帳號>/<repo 名稱>/collaborators/<夥伴帳號> -f permission=push`。
3. 告訴使用者：「GitHub 會寄邀請信給 <夥伴帳號>，他按接受就能一起改。」
4. 夥伴要改別人的 repo 時：用 `gh repo clone <主人帳號>/<repo 名稱>` 拿下來，改完照「再放一次」推回去。推不上去通常是邀請還沒按接受。

## 回到上一版
1. 一樣先確認 repo 在 <帳號> 底下。
2. 用 `git log --oneline -5` 列出最近幾版，用白話說明每一版改了什麼。
3. 使用者選好要回到哪一版後，用 `git revert --no-edit <那一版>..HEAD`，再 `git push`。保留紀錄，不要用 reset。

## 絕對不做
- 只動 <帳號> 底下、這位使用者要你動的 repo。他原本就有的其他 repo，沒說就不碰。
- 不刪除任何 repo，不把 public 改成 private 或反過來。
- 不執行 `gh auth login`、`logout`、`switch`，不在對話裡顯示 token。
- 不用 `git push --force`。
