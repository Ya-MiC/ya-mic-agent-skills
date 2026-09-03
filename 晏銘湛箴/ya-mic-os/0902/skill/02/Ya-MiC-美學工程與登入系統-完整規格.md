# Ya-MiC 美學工程 × 登入系統 × 第二大腦儀表盤　完整規格 v1.0

> 本文件回應你這輪提出的四件事，一件不少：
> 1. **美學工程**——面板要換一套「專業又俏皮」的視覺語言，參考 Duolingo / Wise / Nexo 的設計邏輯，明確排除幣安（CZ）式與 PayPal 式風格。
> 2. **一個程序**——把美學系統變成可執行的設計代幣（Design Tokens）與元件庫程式碼，不是空談。
> 3. **登入系統全考量**——Apple / Google / 郵箱驗證碼 / 手機號 / 微信類，以及背後的電信運營商問題，全部攤開講清楚。
> 4. **第二大腦 + 儀表盤**——把美學系統與登入系統，接回到 `ya-mic-os` 儀表盤與你的長期第二大腦架構上。
>
> 我先讀取了你先前貼過的 `audit-agent-blueprint` 倉庫裡的四份文件（原始創意、我的理解、調研綜述、README），裡面已經有 Logto / Supabase、短信、微信登入的初步調研，本規格會直接承接、不重複造輪子，並補齊你這次新問的「電信運營商」細節。 [github_mcp_direct:1]

---

## 目錄

- 第一部分　美學定位：三個參照 + 兩個禁區
- 第二部分　設計哲學與語氣（Tone of Voice）
- 第三部分　色彩系統（原創色板，非直接挪用品牌色）
- 第四部分　字體與排版系統
- 第五部分　空間、圓角、陰影與動態規則
- 第六部分　元件庫規格（按鈕／卡片／徽章／進度條／導覽）
- 第七部分　流程圖／資料流／產品圖的視覺語言（呼應 Archify 的偏好）
- 第八部分　深色／淺色雙模式
- 第九部分　無障礙與國際化排版
- 第十部分　登入系統總覽：六種登入方式全解析
- 第十一部分　電信運營商深水區：簡訊驗證碼的真實運作
- 第十二部分　微信、支付寶與中國大陸合規登入
- 第十三部分　Apple 登入與 Google 登入的技術細節
- 第十四部分　統一身份層架構（Logto/Supabase 選型與帳號綁定）
- 第十五部分　安全機制：速率限制、驗證碼防刷、Session 與雙因素
- 第十六部分　國際版與中國版雙軌部署策略
- 第十七部分　「你要我寫的程序」：設計代幣程式碼與元件示範
- 第十八部分　把美學與登入接回 `ya-mic-os` 儀表盤
- 第十九部分　第二大腦架構：從儀表盤到知識網絡
- 第二十部分　落地順序與人類裁決清單
- 附錄　速查表全集

---

# 第一部分　美學定位：三個參照 + 兩個禁區

## 1.1 你認可的三個參照，各自貢獻什麼

| 參照 | 貢獻的設計邏輯 | 不該照抄的部分 |
|---|---|---|
| **Duolingo** | 圓潤、俏皮、遊戲化激勵（連續天數、進度條、徽章）、單一飽和主色只用在「進度/行動」語境 | 吉祥物插畫風格（那是它的品牌資產，不適合搬到審計/資產治理產品） |
| **Wise** | 幾何色塊拼貼、透明溝通式文案（把費用、匯率講清楚而不是藏起來）、專屬字體帶來的品牌辨識度 | 匯款/跨境支付的產品敘事（你的產品不是支付） |
| **Nexo** | 深色高級感背景＋單一高亮強調色、資料卡片化呈現資產與收益、tier/徽章制造專業信任感 | 加密資產「保證高收益」式的行銷語言（見下方禁區） |
| **Archify（你貼的連結）** | 用節點化流程圖清楚呈現工作流、資料流、產品結構，「一張圖看懂系統」 | 它的具體配色與吉祥物（若有），只借「圖解邏輯」 |

## 1.2 兩個明確禁區

**禁區一：CZ / 幣安式**
- 特徵：高對比金黃配黑、強烈行銷語氣（「財富自由」「All in」）、倒數計時式緊迫感、meme 化的社群語言。
- 為何排除：你的產品是審計、資料分析、知識治理，需要的是**可信賴的專業感**，不是投機式的興奮感。金融相關頁面尤其要避免暗示保證回報的視覺與文案語言（呼應你在 GitHub 治理 Skill 中已經定義的 `non-investment-advice` 紅線）。

**禁區二：PayPal 式**
- 特徵：冷藍＋純白、高度制式化、大量法律免責文字擠在一起、缺乏個性與呼吸感。
- 為何排除：這是「安全但無感」的反面案例——使用者會覺得可靠，但不會想每天打開它。你要的「俏皮」正是 PayPal 缺乏的那一層。

## 1.3 你的美學公式

```text
Ya-MiC 美學 =
  Wise 的透明幾何色塊
  + Duolingo 的節制俏皮（只在「完成/進度/成功」時刻俏皮，其他時候克制）
  + Nexo 的深色資料卡專業感
  + Archify 的節點流程圖解邏輯
  - CZ 的浮誇金黑與投機語言
  - PayPal 的冷漠制式感
```

---

# 第二部分　設計哲學與語氣（Tone of Voice）

## 2.1 三條設計原則

1. **克制的俏皮（Restrained Playfulness）**
   俏皮只出現在「使用者完成一件事」的瞬間——分類完一批倉庫、通過一次人類裁決、儀表盤刷新成功。平時的資料呈現保持沉穩，不濫用表情符號或誇張動畫。

2. **透明優先（Radical Clarity）**
   仿照 Wise 的溝通邏輯：任何分數（FinalSPI）、任何風險標籤、任何「AI 生成待審核」狀態，都必須在畫面上直接可見，不藏在 tooltip 或第三層選單裡。

3. **證據感（Evidence-first Visuals）**
   仿照 Nexo 的資料卡片邏輯：每個資產卡片都要有「證據來源」的視覺標記（metadata / README / 人工確認），讓使用者一眼分辨這是事實還是推論。

## 2.2 文案語氣對照表

| 情境 | ❌ 避免（CZ 式或 PayPal 式） | ✅ 採用 |
|---|---|---|
| 資產評分高 | 「王炸資產！立即梭哈投入！」 | 「這項資產的複利潛力較高，建議優先投入。」 |
| 錯誤/風險 | 「錯誤代碼 0x8A2」（純技術冷感） | 「這裡缺少關鍵資訊，需要你來決定——只要 30 秒。」 |
| 完成任務 | （毫無反饋） | 「已完成本輪分類 🎉　剩餘 3 項待你確認。」（俏皮但不誇張） |
| 金融/量化內容 | 「歷史回測證明穩賺」 | 「這是歷史數據分析，不構成投資建議。」 |

---

# 第三部分　色彩系統（原創色板，非直接挪用品牌色）

> 重要聲明：Duolingo 的 `#58CC02`／`#89E219`、Wise 的 `#163300`／`#9FE870` 都是各自的註冊品牌色，直接照搬有品牌混淆與商標風險。 [web:37][web:36] 以下色板是**借用同一套「深色基底＋單一高亮綠」的設計邏輯**，但重新調製出屬於 Ya-MiC 自己的色值，避免與任一品牌撞色。

## 3.1 主色板

```css
:root {
  /* 基底：仿 Nexo 的深色高級感，但用偏暖的墨色而非純黑 */
  --ymc-ink-900: #14171A;   /* 主背景（深色模式） */
  --ymc-ink-800: #1E2226;
  --ymc-ink-700: #2A2F34;

  /* 高亮綠：借用 Wise/Duolingo「單一飽和綠代表行動/成長」邏輯，
     但取一個介於兩者之間、明顯不同的獨立色值 */
  --ymc-lime-500: #7ED957;  /* 主行動色 Primary Action */
  --ymc-lime-600: #63B843;  /* Hover/按下狀態 */
  --ymc-lime-100: #E8F7E0;  /* 淺色模式的背景色塊 */

  /* 輔助強調色：柑橘（用於警示/待裁決，取代黃色警示的刺眼感） */
  --ymc-amber-500: #F2A93B;

  /* 風險紅：克制的磚紅，不用刺眼的純紅 */
  --ymc-brick-500: #E2593C;

  /* 中性色階（淺色模式主用） */
  --ymc-paper-000: #FDFBF6;  /* 帶一點暖白，不是死白 */
  --ymc-gray-100: #F1EFE9;
  --ymc-gray-400: #A8A296;
  --ymc-gray-700: #4A473F;
}
```

## 3.2 色彩語義映射（呼應第五部分 GitHub Skill 的分類體系）

| 語義 | 色彩 | 使用場景 |
|---|---|---|
| 行動 / 進度 / 已驗證 | `--ymc-lime-500` | 主按鈕、FinalSPI 高分徽章、「已完成」狀態 |
| 待裁決 / 需注意 | `--ymc-amber-500` | Human Review Queue 標記、`u ≥ 6` 的不確定性提示 |
| 風險 / 高風險標籤 | `--ymc-brick-500` | `security-sensitive`、`secret-exposure-review` 等風險 Tag |
| 中性資訊 | `--ymc-gray-*` | 一般文字、次要資訊、metadata |
| 深色基底 | `--ymc-ink-*` | 儀表盤主背景，呈現 Nexo 式的資料高級感 |

## 3.3 為什麼不用純黑純白

Nexo 的深色介面實際觀察偏向深藍灰／炭黑而非純黑，純黑在長時間閱讀資料密集畫面時對比過硬，容易疲勞；Ya-MiC 色板選擇帶一點暖調的墨色（`#14171A`）與暖白（`#FDFBF6`），閱讀感更柔和，這也是 Wise 式「溫暖但專業」語氣在色彩層的體現。

---

# 第四部分　字體與排版系統

## 4.1 字體選型原則

Duolingo 與 Wise 都投資了專屬字體（Wise 甚至有自己的 Wise Sans）來建立辨識度，但客製字體成本高。Ya-MiC 現階段建議：

```text
中文：Noto Sans TC / Noto Sans SC（開源、Google Fonts、覆蓋簡繁）
英文/數字：Inter（開源、幾何感強、與圓潤俏皮風格相容度高，
           大量金融科技產品如 Linear、Notion 都在用）
等寬（用於分數、程式碼、Run ID）：JetBrains Mono（開源）
```

不建議一開始就訂製字體，先用開源字體把資訊架構跑順，等產品成熟、有預算時再考慮訂製（如 Wise 的路徑）。

## 4.2 字階（Type Scale）

```css
:root {
  --ymc-text-xs: 12px;    /* metadata、時間戳 */
  --ymc-text-sm: 14px;    /* 次要說明 */
  --ymc-text-base: 16px;  /* 正文 */
  --ymc-text-lg: 20px;    /* 卡片標題 */
  --ymc-text-xl: 28px;    /* 頁面標題 */
  --ymc-text-2xl: 40px;   /* 首頁大標、FinalSPI 大數字 */
}
```

## 4.3 排版節制原則

- 一個畫面最多一種「俏皮」元素（例如一個表情符號徽章），不疊加。
- 數字類資訊（分數、金額、統計）一律用等寬字體對齊，避免像 Nexo 資料卡片那樣參差不齊。
- 中英雙語混排時，中文字級比英文略大 1–2px，因為中文字形視覺重量較重。

---

# 第五部分　空間、圓角、陰影與動態規則

## 5.1 圓角（呼應 Duolingo 的圓潤感，但不做成「氣泡」）

```css
:root {
  --ymc-radius-sm: 8px;    /* 標籤、徽章 */
  --ymc-radius-md: 14px;   /* 按鈕、輸入框 */
  --ymc-radius-lg: 20px;   /* 卡片 */
  --ymc-radius-full: 999px; /* 藥丸狀按鈕、進度條 */
}
```

## 5.2 陰影（呼應 Nexo 的資料卡「浮起感」，但降低強度避免厚重）

```css
:root {
  --ymc-shadow-card: 0 2px 12px rgba(20, 23, 26, 0.08);
  --ymc-shadow-card-hover: 0 6px 20px rgba(20, 23, 26, 0.12);
  --ymc-shadow-modal: 0 12px 40px rgba(20, 23, 26, 0.24);
}
```

## 5.3 動態規則（Motion）

```text
時長：120–200ms（比 Duolingo 的彈跳動畫略克制，避免資料密集畫面眼花）
曲線：ease-out（進場）／ease-in（退場）
俏皮動畫僅限：
  - 完成一項人類裁決後，卡片有一次輕微的「彈起+淡出」
  - FinalSPI 分數第一次載入時，數字從 0 滾動到實際值（強化「資料感」）
克制動畫：
  - 一般 hover 只做顏色/陰影過渡，不做位移彈跳
  - 列表捲動不加任何視差效果（避免像行銷網站，失去「工作台」的沉穩感）
```

---

# 第六部分　元件庫規格

## 6.1 按鈕（Button）

```text
Primary：實心 --ymc-lime-500 背景，深色文字（--ymc-ink-900），
         圓角 --ymc-radius-full（藥丸狀，呼應 Duolingo 的按鈕形狀）
Secondary：透明背景，1px 邊框 --ymc-gray-400，深色文字
Danger：實心 --ymc-brick-500，用於「確認刪除」「確認公開」等 Gate 4 操作
         （故意做得不那麼好按——不用圓角藥丸狀，改用 --ymc-radius-md 方角卡，
         降低「順手一按」的風險，這是刻意的反俏皮設計）
```

## 6.2 卡片（Asset Card / Repository Card）

```text
結構（由上到下）：
  1. 頂部：倉庫名稱 + Lifecycle 圓點（顏色見色彩系統）
  2. 中部：一句話價值主張（Wise 式的直白文案）
  3. 標籤列：Primary Domain / Business Role / Risk Tags（藥丸狀小標籤）
  4. 底部：FinalSPI 分數條（進度條視覺，仿 Duolingo 的技能條）
  5. 角落：證據來源小圖示（metadata 圖示 / README 圖示 / 人工確認勾勾）
```

## 6.3 徽章（Badge / Tag）

```css
.ymc-badge {
  padding: 4px 10px;
  border-radius: var(--ymc-radius-full);
  font-size: var(--ymc-text-xs);
  font-weight: 600;
}
.ymc-badge--domain { background: var(--ymc-lime-100); color: var(--ymc-ink-900); }
.ymc-badge--risk { background: rgba(226, 89, 60, 0.12); color: var(--ymc-brick-500); }
.ymc-badge--pending { background: rgba(242, 169, 59, 0.14); color: var(--ymc-amber-500); }
```

## 6.4 進度條 / 分數條（FinalSPI 視覺化）

```text
軌道：--ymc-gray-100 底色，高度 8px，全圓角
填充：依分數區間變色——
  0–19  --ymc-gray-400（記錄型，低調呈現，不用警示色，因為低分不代表壞）
  20–39 --ymc-amber-500 的低飽和版本
  40–59 --ymc-amber-500
  60+   --ymc-lime-500（核心投資，滿格時有一次性的輕微光澤動畫）
```

## 6.5 導覽（呼應你上一輪定案的「主導航大類 + 子項」結構）

```text
一級導航（左側或頂部固定）：GitHub / Notion / Sheets / （預留）WPS / PPT
  視覺：圖示 + 文字，選中態用 --ymc-lime-500 底色圓角矩形高亮
二級導覽（GitHub 大類下）：倉庫視圖 / Starred 視圖 / Human Review Queue / 能力矩陣
  視覺：頂部 Tab 樣式，選中底線用 --ymc-lime-500
```

---

# 第七部分　流程圖／資料流／產品圖的視覺語言

你明確提到欣賞 Archify 那種「說清楚不同工作流、資料流、產品圖」的呈現方式。以下是把這種節點化圖解，套進 Ya-MiC 視覺語言的規格。

## 7.1 三種圖各自的用途

```text
工作流圖（Workflow Diagram）：呈現「人類裁決子代理 → 深度評估 → 主控發布」
  這種階段性流程，節點是「動作」，箭頭是「順序」。

資料流圖（Data Flow Diagram）：呈現「GitHub API → repo-index.json →
  儀表盤前端」這種資料如何流動與轉換，節點是「資料狀態」，箭頭是「轉換」。

產品/架構圖（Product/System Diagram）：呈現「ya-mic-os、dsh、
  zhanzhen」等倉庫之間的依賴與角色關係，節點是「系統元件」。
```

## 7.2 節點視覺規格

```text
節點形狀：
  動作節點（Workflow）→ 圓角矩形，--ymc-radius-md
  資料節點（Data）→ 平行四邊形或圓角矩形加左側色條區分資料類型
  系統節點（Product）→ 方角卡片，帶小圖示代表技術棧（Docker/Cloudflare 等）

節點配色：
  依第五部分「Primary Domain」分類上色——
  AI-AGENT 節點用 --ymc-lime-500 邊框
  RISK/HUMAN-REVIEW 節點用 --ymc-amber-500 或 --ymc-brick-500 邊框
  RECORD/ARCHIVE 節點用 --ymc-gray-400 邊框（低調）

箭頭：
  實線 = 已驗證的依賴/流程
  虛線 = 推論中/待驗證的關係
  雙向箭頭 = 雙向同步（例如 GitHub ↔ Notion）
```

## 7.3 技術實作建議

沿用你 `audit-agent-blueprint` 調研裡已經選定的 `react-flow` / `Cytoscape.js`（MIT 授權，適合第 3 頁人類審核區的知識圖譜），這套技術棧同樣可以直接用在 `ya-mic-os` 儀表盤的 Panel 6（依賴與關係圖），一套元件庫兩處共用，不必重複選型。 [github_mcp_direct:1]

---

# 第八部分　深色／淺色雙模式

```text
深色模式（預設，呼應 Nexo 的專業資料感）：
  背景 --ymc-ink-900，卡片 --ymc-ink-800，文字暖白 --ymc-paper-000

淺色模式（呼應 Wise 的明亮通透感）：
  背景 --ymc-paper-000，卡片純白帶陰影，文字 --ymc-gray-700

切換開關：
  放在導覽列右上角，用一個小型藥丸開關（Duolingo/Wise 常見形式），
  不俏皮化（不用月亮/太陽跳動畫），保持工作台的沉穩調性
```

---

# 第九部分　無障礙與國際化排版

```text
色彩對比：所有文字與背景對比度須達 WCAG AA（4.5:1 以上），
  --ymc-lime-500 在深色背景上對比達標，在淺色背景上需搭配深色文字，
  不單獨用純綠色文字在白底上（可讀性不足）

色盲友善：風險標籤除了顏色，一律加圖示（⚠ 風險 / ✓ 已驗證 / ? 待確認），
  不能只靠顏色區分語義

多語言：介面預留中文（繁/簡）與英文雙語結構，字串外部化到 i18n JSON，
  不寫死在 HTML/JS 內，方便未來擴充韓文（你在韓國生活，未來可能需要）

RTL：目前不需要，但架構上用 CSS logical properties
  （margin-inline-start 而非 margin-left）預留彈性
```

---

# 第十部分　登入系統總覽：六種登入方式全解析

這是你這次最關切的技術硬需求。以下逐一攤開六種登入方式，包含成本、風險、適用場景。

## 10.1 六種登入方式總表

| 登入方式 | 適合對象 | 成本 | 技術複雜度 | 主要風險 |
|---|---|---|---|---|
| Google 登入（OAuth） | 全球用戶、開發者 | 免費 | 低 | 需 Google Cloud Console 註冊應用 |
| Apple 登入（Sign in with Apple） | iOS/Mac 用戶，**若上架 App Store 幾乎強制要求** | 開發者帳號年費約 $99 | 中 | 需處理「隱藏郵箱」轉發機制 |
| 郵箱＋密碼 | 所有用戶的保底方案 | 免費 | 低 | 需自行做密碼強度與洩漏檢查 |
| 郵箱＋驗證碼（Magic Link/OTP） | 不想記密碼的用戶 | 免費（用郵件服務） | 低 | 郵件送達率、垂圾信匣問題 |
| 手機號＋簡訊驗證碼 | 中國大陸與部分東南亞地區慣用 | **按條計費**（見第十一部分） | 中高 | 簡訊詐騙防範、電信商差異、國際漫遊 |
| 微信登入 | 中國大陸用戶的主流習慣 | 開放平台認證費約 300 元/年 | 高 | 需企業資質、審核週期 [web:file24] |

## 10.2 為什麼要六種都考慮，而不是選一種

你的使用場景橫跨：韓國生活（Google/Apple 為主流）、中國大陸業務對象（微信/手機號為主流）、國際開發者社群（GitHub/Google 為主流）。單一登入方式會篩掉一整群目標使用者。正確做法不是「選一種」，而是建立**統一身份層**（第十四部分），讓使用者用任何一種方式登入，背後都對應同一個帳號。

## 10.3 每種方式的使用者體感設計（呼應第一至九部分美學系統）

```text
Google/Apple 按鈕：使用官方規定的按鈕樣式與 Logo（不可自行改色，
  這是兩家公司的品牌合規要求，不是設計選擇）

郵箱/手機號輸入框：Wise 式的即時驗證反饋——
  輸入中即時判斷格式是否正確，錯誤時用 --ymc-brick-500 的溫和提示文字，
  不用刺眼的紅框閃爍

驗證碼輸入：六格獨立輸入框（業界標準模式），自動跳格，
  倒數重發計時器用等寬字體呈現（60s → 59s → …）

微信登入：僅在偵測到中國大陸 IP 或使用者手動切換「中國大陸版」時顯示，
  國際版介面不顯示微信選項（避免混亂）
```

---

# 第十一部分　電信運營商深水區：簡訊驗證碼的真實運作

你特別問到「通訊服務運營商怎麼辦」——這是大多數教學會跳過的部分，以下講清楚簡訊驗證碼背後實際發生的事。

## 11.1 簡訊驗證碼的完整鏈路

```text
你的後端
  ↓ 呼叫 API
簡訊服務商（阿里雲簡訊 / 騰訊雲簡訊 / Twilio / 國際 SMS 聚合商）
  ↓ 透過與電信商的短信通道協議（SMPP 等）
電信運營商（中國大陸：中國移動／中國聯通／中國電信；
           國際：各國本地電信商，由聚合商代理串接）
  ↓ 基站/網路發送
使用者手機
```

**關鍵理解**：你自己**不會**直接對接電信運營商，這是刻意設計的行業分工——電信商只跟持有牌照的簡訊服務商籤約，個人或小公司必須透過阿里雲/騰訊雲/Twilio 這類「聚合商」間接使用電信通道。

## 11.2 中國大陸的具體限制

```text
簡訊簽名（Sign Name）：
  發送簡訊必須帶「【簽名】」前綴，例如「【湛箴審計】您的驗證碼是123456」，
  簽名申請需要企業營業執照或個人身份認證，審核由運營商與服務商共同把關，
  目的是防止詐騙簡訊冒充品牌

簡訊模板審核：
  每一種簡訊內容模板都要單獨報備審核，不能發送任意文字，
  審核週期通常 1–3 個工作日

三大運營商差異：
  中國移動／中國聯通／中國電信在部分省份的到達率、延遲有差異，
  正規簡訊服務商（阿里雲/騰訊雲）已經處理好三網路由，
  你不需要自己選擇要走哪家運營商的通道

成本（依你 audit-agent-blueprint 調研中的資料）：
  約 0.04–0.05 元/條，新用戶通常有 100–200 條免費額度 [file:24]
```

## 11.3 國際簡訊的差異

```text
國際簡訊比中國大陸貴很多（不同國家電信商的國際簡訊互聯結算費用更高），
  Twilio 等國際聚合商一條驗證碼簡訊常見在 $0.02–$0.10 美元之間，依國家浮動

韓國本地（你目前所在地）：
  若要面向韓國用戶，需要使用支援韓國本地電信商（SKT/KT/LG U+）路由的
  國際簡訊聚合商，Twilio、MessageBird 等主流聚合商都支援韓國號碼段

漫遊與虛擬號段風險：
  部分聚合商對 VOIP/虛擬號碼會拒發驗證碼（防止批量註冊濫用），
  這是正常的反詐騙機制，不是系統故障
```

## 11.4 為什麼很多產品乾脆放棄簡訊、改用郵箱或第三方登入

簡訊驗證碼是六種方式裡**成本最高、合規最複雜**的一種。實務建議：

```text
最小可行版本（MVP 階段）：只做 Google 登入 + 郵箱驗證碼
  → 零成本，一個下午可以接完（延續 audit-agent-blueprint 的建議）[file:24]

正式產品版本（有中國大陸用戶且有企業主體後）：
  → 才加手機號簡訊與微信登入，因為這時候申請簽名、企業認證才有意義
```

## 11.5 簡訊防刷機制（技術對策）

```text
1. 同一手機號 60 秒內只能請求一次驗證碼
2. 同一 IP 每小時最多請求 N 次（例如 10 次）
3. 前端加入行為驗證（滑塊/圖形驗證碼）在請求簡訊之前先過濾機器人
4. 驗證碼 5 分鐘過期，且只能使用一次
5. 監控異常模式：同一時段大量不同號碼從同一 IP 段請求
   （這是简訊轟炸攻擊的典型特徵，服務商如阿里雲通常有內建風控，
   但應用層仍要做基本防護）
```

---

# 第十二部分　微信、支付寶與中國大陸合規登入

## 12.1 微信登入的完整要求

```text
前提：微信開放平台開發者資質認證，年費約 300 元 [file:24]
      需要企業營業執照（個人開發者目前對「網站應用」類別的支援有限，
      這是微信平台的政策限制，非技術限制）

技術流程：
  使用者掃碼 → 微信 App 內確認登入 → 微信回傳 code
  → 你的後端用 code 換 access_token 與 openid
  → 用 openid 作為使用者唯一識別碼綁定你自己的帳號系統

視覺呈現：微信官方規定登入按鈕必須使用其標準綠色圖示，
          不可自行改色（品牌合規要求）
```

## 12.2 支付寶登入

```text
支付寶登入接入本身免費，但同樣需要企業/個體工商戶資質 [file:24]
適合已有 ToB 商業主體、且客戶多為中國大陸企業的階段
```

## 12.3 ICP 備案與伺服器位置的連動關係

```text
如果你的伺服器（VPS）架設在中國大陸境內 → 必須完成 ICP 備案（約 1–3 週，免費）
如果伺服器架設在境外（例如你熟悉的 Cloudflare/海外 VPS）
  → 不需要 ICP 備案，但中國大陸使用者訪問速度與穩定性可能受影響，
     且微信/支付寶對「境外網站」的登入接入審核可能更嚴格

建議路徑（延續你 audit-agent-blueprint 調研的既有結論）：
  國際版：海外伺服器 + Google/Apple/郵箱登入，零合規成本
  中國版：等有企業主體後，境內或香港伺服器 + 微信/手機號登入 [file:24]
```

---

# 第十三部分　Apple 登入與 Google 登入的技術細節

## 13.1 Google 登入（Google Identity / OAuth 2.0）

```text
註冊：Google Cloud Console 建立 OAuth 用戶端 ID，免費
所需資訊：應用名稱、Logo、隱私權政策連結、授權網域
使用者體感：一鍵選擇 Google 帳號，免密碼
資料取得：Email、姓名、頭像（需使用者授權同意）
成本：完全免費，無使用量上限（一般規模）
```

## 13.2 Apple 登入（Sign in with Apple）

```text
註冊：需要 Apple Developer Program 帳號，年費約 99 美元
特殊機制「隱藏我的郵箱」：
  使用者可以選擇不提供真實郵箱，Apple 會生成一個轉發用的隨機郵箱
  （例如 abc123@privaterelay.appleid.com），郵件仍會轉發到使用者真實信箱，
  但你的資料庫拿到的是這個轉發位址——這是 Apple 的隱私保護機制，
  不是系統錯誤，設計資料庫時要預留這種「非常規但合法」的郵箱格式

強制要求場景：
  如果你的產品未來上架 App Store 且提供了其他第三方登入（如 Google），
  Apple 政策通常要求「必須同時提供 Sign in with Apple」作為選項，
  這是你要上架 iOS App 時必須規劃的合規成本
```

## 13.3 郵箱登入的兩種模式比較

```text
模式一：郵箱＋密碼
  優點：使用者掌控感強，離線可用（不需要每次收信）
  缺點：需要自行做密碼強度檢查、忘記密碼流程、洩漏密碼庫比對

模式二：郵箱＋一次性驗證碼／Magic Link
  優點：不需要記密碼，減少「忘記密碼」客服負擔
  缺點：依賴郵件送達速度與垂圾信匣分類，國際郵件服務商
        （SendGrid/Resend/Amazon SES）的到達率需要監控

建議：兩者並存，讓使用者自己選——這正是 Wise 式「把選擇權交給使用者」的
      透明化設計邏輯
```

---

# 第十四部分　統一身份層架構（Logto/Supabase 選型與帳號綁定）

## 14.1 為什麼不要自己從零寫登入系統

自建登入系統需要處理密碼雜湊、Session 管理、OAuth 各家協議差異、簡訊/郵件發送、防刷機制——這些都是「重複造輪子且極易出安全漏洞」的領域。你 `audit-agent-blueprint` 的調研已經指向正確方向：使用開源統一身份層。 [file:24]

## 14.2 選型對照

| 方案 | 免費額度 | 支援登入方式 | 適合場景 |
|---|---|---|---|
| **Logto**（開源 CIAM，中國團隊背景） | 自托管完全免費；雲版 50k 月活免費 | 郵箱密碼/Magic Link、手機簡訊（可接阿里雲）、微信、Google、Apple、GitHub 等 | 你的场景最推薐——中英文雙語支援好，且對中國大陸整合友善 |
| **Supabase Auth** | 50k 月活免費 | 郵箱、Google、GitHub 等主流 OAuth | 若你同時要用 Supabase 的 Postgres/pgvector 資料庫，一次性整合更省事 |

## 14.3 帳號綁定（Account Linking）架構

**核心問題**：同一個使用者可能先用 Google 登入，之後又想用手機號登入，系統要判斷這是「同一人」還是「兩個新帳號」。

```text
綁定策略：
  1. 以「已驗證的 Email」作為主要綁定鍵——
     若 Google 帳號的 Email 與已有帳號的 Email 相同，自動合併
  2. 手機號與微信 openid 沒有天然的 Email 對應，
     需要「登入後主動綁定」流程：
     使用者用手機號登入 → 系統提示「綁定 Google/Apple 以防丟失帳號」
  3. 絕不自動合併「未驗證」的聯絡方式，避免帳號被盜用者接管
```

## 14.4 資料庫層的使用者模型

```yaml
users:
  id: uuid  # 系統內部唯一 ID，所有業務資料都關聯這個，不是 email 或 openid
  primary_email: string | null
  email_verified: boolean

identities:  # 一對多：一個 user 可以有多個 identity
  - provider: google | apple | email | phone | wechat | alipay
    provider_uid: string  # 各平台自己的唯一識別碼
    verified: boolean
    linked_at: timestamp
```

---

# 第十五部分　安全機制：速率限制、驗證碼防刷、Session 與雙因素

## 15.1 速率限制（Rate Limiting）

```text
登入嘗試：同一帳號 5 次失敗後鎖定 15 分鐘
簡訊/郵件驗證碼請求：見第十一部分 11.5
API 請求：依 IP 與使用者 ID 雙重限流，避免單一帳號被用來打其他服務
```

## 15.2 Session 與 Token 管理

```text
建議使用 JWT（短效 Access Token，15–30 分鐘）+ Refresh Token（長效，7–30 天）
Refresh Token 存於 HttpOnly Secure Cookie，避免 JavaScript 可讀取
    （防範 XSS 竊取 Token）
登出時，後端主動將 Refresh Token 加入黑名單，不只是前端清除
```

## 15.3 雙因素驗證（2FA，建議中長期加入）

```text
對於涉及審計資料、企業帳號的高權限使用者，建議強制開啟：
  Authenticator App（TOTP，如 Google Authenticator）最推薐——
    免費、不依賴電信商、離線可用
  簡訊 2FA 作為備選（但成本與電信商依賴問題見第十一部分）
```

---

# 第十六部分　國際版與中國版雙軌部署策略

延續你 `audit-agent-blueprint` 調研已定的方向，具體到登入與美學層面的落地：

| 維度 | 國際版 | 中國版 |
|---|---|---|
| 伺服器 | Cloudflare / 海外 VPS | 境內雲或香港，需 ICP 備案（境內） |
| 登入方式 | Google、Apple、郵箱 | 手機號簡訊、微信、（可選支付寶） |
| 啟動成本 | ¥0 | 約 300–500 元起（簽名認證＋微信年費） |
| 美學呈現 | 深色模式預設（Nexo 式專業感） | 可考慮淺色模式為主（中國大陸使用者對深色 App 的接受度因產品類型而異，審計類產品建議提供淺色選項更保守） |
| 文案語氣 | 中英雙語，Wise 式透明文案 | 純中文，語氣可以更貼近本土審計行業用語習慣 |

---

# 第十七部分　「你要我寫的程序」：設計代幣程式碼與元件示範

你說「我應該寫一個程序」——這裡把美學系統變成真正可以貼進專案、立即生效的程式碼骨架，而不是空談規格。

## 17.1 設計代幣主檔（`design-tokens.css`）

```css
/* Ya-MiC Design Tokens v1.0 */
:root {
  /* 色彩 */
  --ymc-ink-900:#14171A; --ymc-ink-800:#1E2226; --ymc-ink-700:#2A2F34;
  --ymc-lime-500:#7ED957; --ymc-lime-600:#63B843; --ymc-lime-100:#E8F7E0;
  --ymc-amber-500:#F2A93B; --ymc-brick-500:#E2593C;
  --ymc-paper-000:#FDFBF6; --ymc-gray-100:#F1EFE9;
  --ymc-gray-400:#A8A296; --ymc-gray-700:#4A473F;

  /* 字體 */
  --ymc-font-sans: "Inter", "Noto Sans TC", "Noto Sans SC", sans-serif;
  --ymc-font-mono: "JetBrains Mono", monospace;
  --ymc-text-xs:12px; --ymc-text-sm:14px; --ymc-text-base:16px;
  --ymc-text-lg:20px; --ymc-text-xl:28px; --ymc-text-2xl:40px;

  /* 圓角 */
  --ymc-radius-sm:8px; --ymc-radius-md:14px;
  --ymc-radius-lg:20px; --ymc-radius-full:999px;

  /* 陰影 */
  --ymc-shadow-card: 0 2px 12px rgba(20,23,26,0.08);
  --ymc-shadow-card-hover: 0 6px 20px rgba(20,23,26,0.12);
  --ymc-shadow-modal: 0 12px 40px rgba(20,23,26,0.24);

  /* 動態 */
  --ymc-motion-fast: 120ms ease-out;
  --ymc-motion-base: 200ms ease-out;
}

[data-theme="light"] {
  --ymc-bg: var(--ymc-paper-000);
  --ymc-surface: #FFFFFF;
  --ymc-text-primary: var(--ymc-gray-700);
}
[data-theme="dark"] {
  --ymc-bg: var(--ymc-ink-900);
  --ymc-surface: var(--ymc-ink-800);
  --ymc-text-primary: var(--ymc-paper-000);
}
```

## 17.2 元件範例（`components.css` 節錄）

```css
.ymc-btn {
  font-family: var(--ymc-font-sans);
  font-weight: 600;
  padding: 10px 24px;
  border-radius: var(--ymc-radius-full);
  border: none;
  cursor: pointer;
  transition: background var(--ymc-motion-fast), box-shadow var(--ymc-motion-fast);
}
.ymc-btn--primary {
  background: var(--ymc-lime-500);
  color: var(--ymc-ink-900);
}
.ymc-btn--primary:hover { background: var(--ymc-lime-600); }

.ymc-btn--danger {
  background: var(--ymc-brick-500);
  color: #fff;
  border-radius: var(--ymc-radius-md); /* 刻意不用藥丸狀，降低誤觸風險 */
}

.ymc-card {
  background: var(--ymc-surface);
  border-radius: var(--ymc-radius-lg);
  box-shadow: var(--ymc-shadow-card);
  padding: 20px;
  transition: box-shadow var(--ymc-motion-base);
}
.ymc-card:hover { box-shadow: var(--ymc-shadow-card-hover); }

.ymc-badge {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  padding: 4px 10px;
  border-radius: var(--ymc-radius-full);
  font-size: var(--ymc-text-xs);
  font-weight: 600;
}
.ymc-badge--domain { background: var(--ymc-lime-100); color: var(--ymc-ink-900); }
.ymc-badge--risk { background: rgba(226,89,60,0.12); color: var(--ymc-brick-500); }
.ymc-badge--pending { background: rgba(242,169,59,0.14); color: var(--ymc-amber-500); }

.ymc-progress {
  height: 8px;
  border-radius: var(--ymc-radius-full);
  background: var(--ymc-gray-100);
  overflow: hidden;
}
.ymc-progress__fill {
  height: 100%;
  border-radius: var(--ymc-radius-full);
  transition: width var(--ymc-motion-base);
}
.ymc-progress__fill--high { background: var(--ymc-lime-500); }
.ymc-progress__fill--mid { background: var(--ymc-amber-500); }
.ymc-progress__fill--low { background: var(--ymc-gray-400); }
```

## 17.3 登入表單骨架（`auth-form.html` 節錄，示意結構非完整程式）

```html
<div class="ymc-card" style="max-width:400px">
  <h2 style="font-family:var(--ymc-font-sans)">登入 Ya-MiC OS</h2>

  <button class="ymc-btn" style="background:#fff;border:1px solid var(--ymc-gray-400);width:100%">
    <!-- Google 官方按鈕樣式，Logo 不可自行改色 -->
    使用 Google 帳號登入
  </button>

  <button class="ymc-btn" style="background:#000;color:#fff;width:100%;margin-top:8px">
    <!-- Apple 官方按鈕樣式 -->
    使用 Apple 帳號登入
  </button>

  <div style="text-align:center;margin:12px 0;color:var(--ymc-gray-400)">或</div>

  <input type="email" placeholder="輸入郵箱以取得驗證碼"
         style="width:100%;padding:10px;border-radius:var(--ymc-radius-md);
                border:1px solid var(--ymc-gray-400)">
  <button class="ymc-btn ymc-btn--primary" style="width:100%;margin-top:8px">
    發送驗證碼
  </button>

  <!-- 中國大陸版才顯示 -->
  <div data-region="cn" style="display:none;margin-top:16px">
    <input type="tel" placeholder="輸入手機號">
    <button class="ymc-btn" style="background:#07C160;color:#fff;width:100%;margin-top:8px">
      微信登入
    </button>
  </div>
</div>
```

## 17.4 建議的實作優先序（程式化落地）

```text
第 1 步：把 design-tokens.css 貼進 ya-mic-os，取代現有雜亂的內嵌樣式
第 2 步：用 components.css 重寫既有的資產卡片，驗證視覺效果
第 3 步：Logto 雲版免費起步，先接 Google + 郵箱登入
第 4 步：把 auth-form.html 接上 Logto 的 SDK（Logto 提供現成的
         Web SDK，不需要自己實作 OAuth 流程）
第 5 步：待有明確中國大陸客戶需求時，才開通微信/簡訊模組
```

---

# 第十八部分　把美學與登入接回 `ya-mic-os` 儀表盤

延續上一輪已定案的單頁 PWA 架構（見前一份 GitHub Portfolio OS Skill 文件第二十一部分），本規格新增：

```text
1. index.html 引入 design-tokens.css 與 components.css，
   取代舊有頁面各自的內嵌樣式（gallery.html / guide.html / start.html /
   tutorial.html 目前應該各自有一套樣式，合併後統一）

2. 登入狀態決定可見範圍：
   未登入 → 只能看公開倉庫的只讀展示（呼應你這次說的
           「我的倉庫展示只是示範，如果別人認可我，我接受他們也這樣」——
           這意味著公開展示層應該獨立於你的私人治理層，
           私有倉庫、Human Review Queue、風險細節只有登入後的你能看到）
   已登入（你本人）→ 完整治理面板，含 Gate 3/4 的操作入口

3. 深色模式與美學系統綁定使用者偏好設定，存在 Logto 的 user profile
   custom claim 裡，跨裝置同步
```

---

# 第十九部分　第二大腦架構：從儀表盤到知識網絡

你提到「後續展望我也説了我一定需要第二大腦和儀表盤」，這裡把兩者的關係講清楚，避免變成兩套互相脫節的系統。

## 19.1 分工原則

```text
儀表盤（ya-mic-os）＝ 資產的「狀態呈現層」
  回答：現在有什麼、狀態如何、需不需要我裁決

第二大腦（Notion 為主，未來可能加 Obsidian）＝ 知識的「思考與連結層」
  回答：這些資產背後的想法、決策脈絡、跨領域的關聯是什麼

兩者關係：儀表盤是第二大腦的「即時儀表」，
  第二大腦是儀表盤數據背後的「敘事與記憶」
```

## 19.2 具體連接方式

```text
1. 儀表盤每個資產卡片提供「在 Notion 開啟相關筆記」的連結
   （若該倉庫在 Notion 治理頁面中有對應段落，如你已有的
   「🟣 A｜湛箴・審計智能體」頁面）[notion_mcp:2]

2. Human Review Queue 的裁決結果，同步寫回 Notion 的 Decision Ledger 頁面，
   形成長期可查詢的「為什麼當初這樣決定」的記錄

3. 第二大腦裡的長期構想文件（例如你的 audit-agent-blueprint 四份文件）
   應該被儀表盤引用，而不是重複整理一份——
   儀表盤的 AI-AGENT / PRODUCT-SAAS 分類卡片直接連回這些原始構想文件
```

## 19.3 知識圖譜視覺化（呼應第七部分的節點圖語言）

```text
用同一套 react-flow 節點視覺，在第二大腦層面呈現：
  「原始創意 → 我的理解 → 調研綜述 → README」的文件演化鏈
  這與儀表盤 Panel 6 的資產依賴圖，共用同一套視覺元件與配色邏輯，
  讓使用者感覺這是「一套系統」而不是兩個各自為政的工具
```

---

# 第二十部分　落地順序與人類裁決清單

## 20.1 建議執行順序

```text
Phase 0（本週，¥0 成本）：
  1. 確認本文件的色彩/字體/元件規格
  2. 我可以先把 design-tokens.css + components.css 實際套用到
     ya-mic-os 的一個頁面做視覺驗證（Gate 2，需你批准才寫入倉庫）

Phase 1（1–2 週）：
  3. Logto 雲版註冊，接 Google + 郵箱登入
  4. 儀表盤加入登入狀態判斷（公開展示層 vs 私人治理層）

Phase 2（視你的中國大陸業務進度）：
  5. 企業資質到位後，開通微信登入與簡訊驗證碼

Phase 3（長期）：
  6. 第二大腦與儀表盤的雙向連結（Notion API 讀寫）
  7. 知識圖譜視覺化上線
```

## 20.2 需要你裁決的問題

```text
Q1. 深色模式是否作為預設？（本文件建議是，因為呼應 Nexo 的專業資料感）
Q2. 是否要在 MVP 階段就做「公開展示層」與「私人治理層」的區分，
    還是先全部公開、之後再收緊權限？
Q3. 微信/簡訊登入的優先級——你目前有無明確的中國大陸企業主體規劃時間表？
Q4. 是否同意 design-tokens.css 的原創色板方案（而非更接近 Wise/Duolingo
    的具體色值）？
```

---

# 附錄　速查表全集

## A. 美學禁區速查

```text
❌ CZ/幣安式：金黑配色、投機語言、倒數緊迫感
❌ PayPal 式：冷藍純白、制式化、無個性
```

## B. 色彩速查

```text
主行動 --ymc-lime-500 #7ED957
待裁決 --ymc-amber-500 #F2A93B
風險   --ymc-brick-500 #E2593C
深色基底 --ymc-ink-900 #14171A
暖白   --ymc-paper-000 #FDFBF6
```

## C. 登入方式速查

```text
Google（免費/低複雜度）→ MVP 必做
Apple（$99/年，上架強制）→ 上 App Store 前必做
郵箱驗證碼（免費）→ MVP 必做
郵箱密碼（免費）→ 可選
手機簡訊（按條計費+合規複雜）→ 中國大陸業務成熟後做
微信（¥300/年+企業資質）→ 中國大陸業務成熟後做
支付寶（免費但需資質）→ 視 ToB 客戶需求
```

## D. 電信運營商速查

```text
你不直接對接電信商 → 透過阿里雲/騰訊雲/Twilio 等聚合商間接使用
中國大陸簡訊需要「簽名」與「模板」雙重審核
國際簡訊比境內貴，且部分聚合商拒發 VOIP 號段
2FA 首選 Authenticator App，不依賴電信商
```

## E. 統一身份層速查

```text
推薦：Logto（開源，中國團隊，雲版 50k MAU 免費，支援本文件全部六種登入）
資料模型：users（唯一內部 ID）+ identities（多對一綁定各登入方式）
```

---

> 本文件目前僅為規格與程式碼草稿，**未對 `ya-mic-os` 或任何 GitHub 倉庫執行實際寫入**。若你認可色彩與元件方向，下一步請明確回覆「同意，開始把 design-tokens.css 寫入 ya-mic-os」，我會依 Gate 2 協定先列出精確的檔案變更清單，等你批准後才寫入。
