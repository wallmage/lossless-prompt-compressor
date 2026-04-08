# Lossless Prompt Compressor

[English](README.md) | [中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [한국어](README.ko.md) | [Español](README.es.md) | [Italiano](README.it.md) | [Português](README.pt-BR.md)

壓縮 AI 指令文件、規則檔、規劃文件和產品規格書。不丟任何事實。

這是壓縮，不是摘要。機器需要的事實全部保留，格式冗餘、重複內容、只給人看的部分通通砍掉。也可以選擇改寫成超高密度的 AI 專用速記。

## 模式

- **嚴格無損**（預設）：機械清理（Pass 1）加上經審批的保真編輯（Pass 2）。每條事實都在，人也能讀。
- **AI 無損**：在前面基礎上加 Pass 3。去掉教學、指導說明、排程這些只有人才看的內容。
- **AI 專用**：再加 Pass 4。完整的電報風格改寫，密度拉滿。

## 工作流程

| Pass | 類型 | 審批方式 | 典型壓縮率 |
|------|------|----------|-----------|
| 1 | 機械清理 | 自動 | 10-25% |
| 2 | 保真壓縮 | 逐項審批 | +5-15% |
| 3 | 去除人讀內容 | 逐項審批 | +15-25% |
| 4 | 電報改寫 | 需明確開啟 | +25-40% |

Pass 1 拆包裝。Pass 2 真空封裝。Pass 3 丟掉只有人才看的說明書。Pass 4 把一切轉成速記。

## 設計原則

- 預設嚴格無損：是壓縮，不是摘要
- 章節編號安全：交叉參照錨點不會被重編
- 審批把關：非機械編輯都要使用者確認
- 23 條編號技巧，附前後對比範例，見 `references/techniques.md`
- 12 種邊界情況，涵蓋程式碼密集型 prompt 到多語言文件

## 目錄結構

```
lossless-prompt-compressor/
├── SKILL.md              — 操作手冊（約 350 行）
└── references/
    └── techniques.md     — 完整技巧目錄及範例
```

## 作者

[Wallny](https://github.com/wallmage)
