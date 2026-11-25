# 差異式上線資安檢查

這是一個 Codex Skill，協助在 Git 推送或 FTP 部署前，依照本次變更和上傳清單檢查環境檔、機密資訊、FTP 排除項目，以及受影響的後台頁面和 API 登入保護。

檢查範圍以本次變更為準，不會為一般上傳掃描整個網站。

## 功能

- 確認本次 Git 推送不包含 .env 或存有真實憑證的環境檔。
- 確認 FTP 清單排除 .env、AGENTS.md、ANGINT.md、GEMINI.md 和 CLAUDE.md。
- 在本次新增、修改、推送或上傳的檔案中，尋找寫死的 API Key、密碼、Token、私鑰、連線字串等機密。
- 只有後台頁面、路由或登入驗證程式有相關變更時，才檢查受影響的頁面登入保護。
- 只有後台 API 或其驗證程式有相關變更時，才檢查受影響的 API 登入保護。
- 不重複檢查未變更的區域，不做整站掃描。

## 安裝（Codex）

將此倉庫的 SKILL.md 和 agents 資料夾放入 Codex 個人 Skills 目錄：

- Windows：%USERPROFILE%\.codex\skills\change-scoped-security-check
- macOS / Linux：~/.codex/skills/change-scoped-security-check

安裝後目錄結構如下：

    change-scoped-security-check/
    ├── SKILL.md
    └── agents/
        └── openai.yaml

## 使用方式

可直接要求 Codex：

- 請用 change-scoped-security-check 檢查這次 Git 推送。
- 請依照 FTP 上傳清單做差異式資安檢查。
- 請檢查這次修改的後台 API 是否仍要求登入。

也可以在 Codex 中明確叫用 Skill：$change-scoped-security-check

請提供本次預計推送的變更範圍或 FTP 上傳清單。清單不明時，Skill 會先請你補充，不會擴大成整站掃描。

## .env 與機密資訊

Git 推送和 FTP 批次上傳都不得包含 .env。FTP 流程不會自動上傳、覆蓋或刪除主機上的 .env；首次部署或環境設定變更時，Skill 會提醒使用者自行確認並安全處理主機設定。

實際 API Key、帳密、Token 和其他憑證不得寫在一般程式碼、設定檔、文件、日誌、測試資料、前端程式或公開產出內容中。可使用 .env 或專案明確採用的機密管理服務保存機密。範例環境檔只能放假值或佔位文字。

若在變更中發現疑似機密，Skill 只回報檔案、行號和類型，不顯示機密值；若憑證已推送或上傳，會提醒使用者輪替。

## 檢查範圍與限制

後台登入檢查只涵蓋本次變更影響的頁面、路由、API 和直接相關的驗證程式。若程式碼不足以確認保護狀態，結果會標示為無法確認。

本 Skill 不執行整站掃描，也不代替滲透測試。檢查結果僅代表本次提供的變更和部署清單。

## 回報格式

每個適用項目會標示通過、未通過、略過或無法確認，並附上相關檔案、行號或上傳清單。略過的檢查會簡述原因。
