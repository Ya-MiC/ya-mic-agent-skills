# 任務提示詞：修復 ya-mic-os 託管 + README 介紹化

> 交辦對象：幹活 Agent（本地工具，具有 Ya-MiC GitHub 倉庫寫入權限）
> 倉庫：`Ya-MiC/ya-mic-os`（私有，main 分支）
> 預計耗時：30 分鐘以內
> 規則：所有修改走分支 + PR，不直接覆寫 main；每次外部寫入前向我確認。

---

## 背景（已診斷，不需要你重新排查）

本倉庫是 Vite 專案結構（根目錄有 `package.json`、`vite.config.js`、`public/`），
但 `index.html` 是純靜態頁面，所有資源為寫死的相對路徑，啟動數據請求：

```
fetch('public/data/portfolio.json')
fetch('public/data/tasks.json')
fetch('public/data/starred.json')
fetch('public/data/private.enc')
```

Netlify 檢測到 Vite 會自動執行 `npm run build`，建構時 Vite 把 `public/` 複製到 `dist/` 根目錄，
導致線上路徑變成 `/data/portfolio.json`，而代碼仍請求 `public/data/...` → 404 →
`boot()` 崩潰 → 面板全空、地圖停在「載入中…」。

**修復策略：讓 Netlify 不建構、直接發布倉庫根目錄。** 用檔案配置（netlify.toml）實現，人類不需要去 Netlify 後台點任何按鈕。

---

## 任務一：寫 netlify.toml（倉庫根目錄，新建）

```toml
[build]
  command = ""
  publish = "."
```

## 任務二：寫 .netlifyignore（倉庫根目錄，新建）

避免把非網站檔案暴露到公開站點：

```
/.github
/docs
/tools
/runs
/node_modules
package.json
package-lock.json
vite.config.js
.gitignore
README.md
LICENSE
未命名.md
版本介紹.md
```

## 任務三：重寫 README.md 為「介紹文件」

現有 README 是工作紀錄風格，改寫成別人第一眼看得懂的專案介紹，必須包含以下段落（順序固定）：

1. **專案名 + 一句話**：「Ya-MiC Portfolio OS — 卡片式的 GitHub 資產治理面板」
2. **這是什麼**：59 倉庫 + 42 Starred + 6 個 Notion 頁，一個網站看全部；Skill 3.0 評分 × 15 項裁決 × 6 張可縮放地圖
3. **核心功能**（5–7 條列表，每條一行）：
   - 資產畫廊：倉庫/星標/Notion 卡片，篩選、排序、詳情彈窗
   - 評分算法：11 因子 → FinalSPI，逐倉計算器與雷達圖
   - 八張 SVG 地圖：依賴總圖、產品線架構、生態關係、網路拓撲等，滾輪縮放/全屏/節點可點
   - 三條 GitHub Actions 流水線：每 6 小時數據同步、裁決留言自動回收、Pages 自動部署
   - 私有視圖：口令本地 AES-GCM 解密，不上傳伺服器
4. **線上地址**：`https://lighthearted-licorice-2a2426.netlify.app`（修復部署後生效）
5. **技術**：純靜態 HTML/CSS/JS + 確定性 SVG 地圖引擎，無後端
6. **Roadmap**（三條，作為未來介紹）：
   - 通用化：任何人輸入自己的 GitHub 用戶名，一鍵生成自己的面板
   - 桌面版：Tauri 封裝 exe，本地一鍵部署
   - OAuth：桌面版內登錄自己的 GitHub，自動拉取資料
7. **授權**：本站自寫代碼 MIT；第三方組件各自遵循原始協議

寫作要求：繁體中文；總長 100–150 行以內；不要貼大段內部治理細節（那是 docs/ 的事）。

## 任務四：刪除 `未命名.md`

該文件為 0 位元組空文件，直接從倉庫刪除。

---

## 禁止事項（違反任何一條即停止）

- 不修改 `index.html`、`assets/`、`public/`、`.github/` 的任何現有內容
- 不修改 `data/` 目錄與三條 workflow
- 不重新命名、不公開、不刪除任何倉庫
- 不動 `版本介紹.md`（本次只刪 `未命名.md`）
- 不引入任何新依賴、不執行 npm install

## 驗收標準

1. `git diff` 顯示：新增 2 個文件（netlify.toml、.netlifyignore）+ 重寫 README.md + 刪除 未命名.md，其餘零變更
2. 本地根目錄直接打開 `index.html` 仍然完全正常（說明靜態結構未被破壞）
3. 提交後 Netlify 自動重新部署，`https://lighthearted-licorice-2a2426.netlify.app` 面板數據、卡片、地圖全部正常顯示
4. 部署的站點上不應能存取 `/.github/`、`/docs/`、`/runs/` 下的檔案

## 提交規範

- 分支名：`fix/netlify-static-deploy`
- 提交訊息：`fix: switch Netlify to static root publish; rewrite README as intro`
- 開 PR 到 main，PR 描述附上本提示詞的驗收標準勾選清單
