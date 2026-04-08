# Lossless Prompt Compressor

[![GitHub stars](https://img.shields.io/github/stars/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/network/members)
[![GitHub watchers](https://img.shields.io/github/watchers/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/watchers)
[![GitHub last commit](https://img.shields.io/github/last-commit/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor/commits/main)
[![GitHub repo size](https://img.shields.io/github/repo-size/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Top language](https://img.shields.io/github/languages/top/wallmage/lossless-prompt-compressor?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Compression](https://img.shields.io/badge/compression-lossless-111927?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Focus](https://img.shields.io/badge/focus-prompt%20compaction-0f766e?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Works with](https://img.shields.io/badge/works%20with-LLM%20%7C%20agent%20%7C%20chat-4f46e5?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Output](https://img.shields.io/badge/output-smaller%20context-b45309?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)
[![Guarantee](https://img.shields.io/badge/guarantee-no%20facts%20lost-2563eb?style=flat-square)](https://github.com/wallmage/lossless-prompt-compressor)

[English](README.md) | [中文](README.zh-CN.md) | 繁體中文 | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [한국어](README.ko.md) | [Español](README.es.md) | [Italiano](README.it.md) | [Português](README.pt-BR.md)

## 為什麼做這個

你 system prompt 裡的每一個 token，都是每次 API 呼叫要繳的稅。一份 35,000 字的指令檔意味著大約 48,000 token 的上下文，模型還沒讀到使用者訊息，視窗就先被吃掉一大半了。一天幾千次請求乘上去，你其實是在為格式、重複內容和只有人才看的廢話燒錢。

我做這個就是為了解決這件事。Lossless Prompt Compressor 拿到 AI 指令文件——system prompt、CLAUDE.md、agent 規則檔、產品規格書、總體規劃——然後用力壓縮。注意，不是摘要，是壓縮。每條事實、每個欄位名、每個約束、每個交叉參照全都原封不動留著。砍掉的是包裝：模型根本不需要的 Markdown 格式、重複的描述、教學類的鷹架、勵志廢話、還有那些花 token 但不帶資訊量的語法連接詞。

一份典型的產品規格書，四輪跑完能壓掉 50-65%。這是實打實省下來的錢——每次 API 呼叫都在省，每次呼叫都把更多上下文視窗留給真正重要的對話。

這個壓縮器也是 [vibecheck](https://github.com/wallmage/vibecheck) 裡壓縮環節的核心。

## 「無損」到底是什麼意思

這不是摘要。摘要是它覺得什麼重要就留什麼，其餘丟掉。指令檔這樣搞很危險——少了一條約束，模型就可能幻覺出你明確排除的功能。

無損的意思是：如果原文寫著「限制免費層補貼不超過月營收的 3%」，壓縮後的版本也說一模一樣的話。沒有事實被丟掉、被改到含糊、或者悄悄合併。章節編號釘死不動，「見 S8.7.3」這類交叉參照永遠不會斷。AI 從壓縮版產生的程式碼和架構決策，跟從原版產生的完全一致。

這個保證靠審批門來執行——每個非機械性的編輯都會以前後對比的形式提給你看，只有你確認了才會生效。

## 4 輪掃描，23 條技巧

壓縮器分四輪逐步深入，每輪比上一輪更激進。你來決定停在哪。

### Pass 1 -- 機械清理（自動執行，壓縮 10-25%）

沒有主觀判斷。這一輪去掉的是模型有沒有都一樣解析的格式：

- **Markdown 剝離**：`##` 標題、`**粗體**`、`- ` 列表符、`| 表格 |` 管道符、` ``` ` 程式碼圍欄、`> ` 引用符、`---` 分隔線——全部去掉。文字和結構保留，裝飾去掉。
- **格式轉換**：表格變分號分隔行，列表變行內分號分隔項，連結語法 `[文字](url)` 收縮為 URL。
- **校驗掃描**：剝離後全文掃一遍，抓漏網的 Markdown 殘留。十六進制色碼（`#FF0000`）、正規表示式萬用字元（`*.txt`）、shell 管道、YAML frontmatter 會被辨識為內容，原樣保留。

對應技巧目錄的技巧 1-2。什麼文件都能安全跑。典型節省：10-25% 字數。

### Pass 2 -- 保真壓縮（需審批，額外 +5-15%）

這一輪開始有創造性了。每個提議的編輯都會展示給你，附帶預估節省量，批了才改：

- **交叉參照去重**（技巧 3）：三個地方說同一件事的，保留一處規範表述，其餘替換成指標如「見 S6」。
- **程式碼區塊壓縮**（技巧 4）：struct/class 定義變成緊湊的行內描述，保留所有欄位名、型別、列舉值。例外：Python/YAML/Makefile 原樣保留，因為空白是語義的一部分。
- **JSON 設定壓縮**（技巧 5）：完整 JSON 範例變成 schema 描述。
- **版本明細裁剪**（技巧 6）：如果彙總表和逐版本明細說的一樣，明細砍掉。
- **勵志區塊壓縮**（技巧 7）：「為什麼這很重要」類的段落壓縮到核心技術洞察。
- **設計理由壓縮**（技巧 8）：四段論證變成一句話加關鍵原因。
- **用例列表壓縮**（技巧 9）：囉嗦的多段用例變成每個一句話。
- **ASCII 線框圖壓縮**（技巧 10）：費 token 的 ASCII 圖變成緊湊文字描述。

每條編輯都要過嚴格校驗：事實全在、AI 做出同樣決策、沒有歧義、每條事實能追溯到規範章節。

### Pass 3 -- 去掉人讀內容（需審批，額外 +15-25%）

對每個候選項的判斷標準就一個：「去掉這段，AI 的輸出會變差嗎？」如果不會，那就是目標。

- **新手教學**（技巧 11）：「從 App Store 安裝你的 IDE，大概要 30-60 分鐘……」變成一個裝什麼的清單，不教怎麼裝。
- **練習題和學習活動**（技巧 12）：AI 不需要別人的練習題。
- **逐日排程**（技巧 13）：起床時間和咖啡休息砍掉。功能名和技術需求留著。
- **勵志和激勵內容**（技巧 14）：「別慌」和「慶祝一下！」對模型資訊量為零。
- **驗證矩陣**（技巧 15）：30 列表格每個功能都寫著「出貨」的，壓成兩行。
- **競品比較表**（技巧 16）：「我們全面領先」激勵人的，不激勵模型。
- **人類工作流程範例**（技巧 17）：「你的一天是這樣的」類走讀。

Pass 3 保留「做什麼」和「為什麼」，去掉「人怎麼做」。

### Pass 4 -- 電報改寫（需明確同意，額外 +25-40%）

對整篇文件做全面重寫，用超高密度速記體。每個字都必須攜帶資訊；如果一個字只是為了讓人讀著順暢而存在，砍。這個模式適用於 AI 是唯一消費者的文件。

- **橋接短語消除**（技巧 18）：「這意味著」、「從而使得」、「為了」、「值得注意的是」——全砍。
- **分號分隔片段**（技巧 19）：完整句子變成鍵值片段。冠詞、「是/將」、填充主語全丟。
- **多句摺疊**（技巧 20）：五句話解釋一個概念的，壓成核心事實。
- **隱含主語省略**（技巧 21）：章節標題寫了「搜尋功能」，每句話就不用再說「搜尋功能」了。
- **緊湊記法**（技巧 22）：斜線表選項（`immediate/batched/silent`），箭頭表流程（`offline -> local queue -> sync`），冒號表關係（`Auth: JWT + refresh`）。
- **重複結構模式壓縮**（技巧 23）：描述一次模式，後面全用速記。

現代 LLM 解析電報風格完全沒問題。分號、箭頭、斜線分隔選項——沒有哪個模型系列搞不定。

## 模式

三種模式，層層遞進：

- **嚴格無損**（預設）：Pass 1 + Pass 2。事實一條不少，文件人還能讀。適合你和 AI 都會查閱的檔案——CLAUDE.md、共享 agent 規則、持續維護的規格書。穩妥、保守、實在省錢。

- **AI 無損**：加上 Pass 3。去掉教學、勵志、逐日排程、驗證矩陣——所有只幫助人類讀者的內容。AI 拿到的訊號完全一樣。適合 system prompt 和 agent 指令這種 AI 消費但你很少回頭看的檔案。

- **AI 專用**：加上 Pass 4。全面電報風格重寫。密度拉滿，人讀著不舒服。適合高頻生產環境的 prompt——每省一個 token，一天幾千次呼叫全在省。

## 工作流程

| Pass | 做什麼 | 審批 | 典型壓縮幅度 |
|------|--------|------|-------------|
| 1 | 剝離 Markdown 格式，轉換表格/列表 | 自動 | 10-25% |
| 2 | 事實去重，壓縮程式碼/JSON/論證 | 逐項 | +5-15% |
| 3 | 去教學、勵志、排程、人讀鷹架 | 逐項 | +15-25% |
| 4 | 全面電報風格重寫，片段代替句子 | 需明確同意 | +25-40% |

一份典型產品規格書四輪全跑：**壓縮 50-65%**。35,000 字的文件大約每次 API 呼叫少吃 25,000-32,000 個 token。

## 設計原則

- **預設無損**：Strict Lossless 是預設模式，想更激進得你自己明確開啟。
- **章節編號神聖不可侵犯**：交叉參照錨點永遠不會被重新編號，即使周圍的章節被刪了。「見 S8.7.3」始終有效。
- **審批把關**：每個非機械性的編輯都會帶著 diff 和預估節省量提給你。不點頭就不改。
- **23 條技巧帶範例**：完整的前後對比目錄在 `references/techniques.md`。
- **12 種邊界情況都處理了**：短文件、程式碼密集型 prompt、已壓縮輸入、非英語/CJK 文字、嵌入的 URL、混合語言文件、講 Markdown 的文件、文學/法律文字、超大文件，等等。

## 目錄結構

```
lossless-prompt-compressor/
├── SKILL.md              — 操作手冊（約 350 行）
└── references/
    └── techniques.md     — 完整技巧目錄，附前後對比範例
```

## 作者

[Wallny](https://github.com/wallmage)
