# Lossless Prompt Compressor

[English](README.md) | [中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [한국어](README.ko.md) | [Español](README.es.md) | [Italiano](README.it.md) | [Português](README.pt-BR.md)

给 AI 指令文档、规则文件、规划文档、产品规格书做压缩。事实一条不丢。

注意啊，这是压缩，不是摘要。机器要用的事实全留着，格式水分、重复内容、写给人看的部分直接砍。想要更狠的话，还能改写成 AI 专用速记，密度拉满。

## 模式

- **严格无损**（默认）：先做机械清理（Pass 1），再加审批过的保真编辑（Pass 2）。事实一条没少，人照样能读。
- **AI 无损**：多跑一个 Pass 3，把教程、指导说明、排期这些人才看的东西去掉。
- **AI 专用**：再来 Pass 4，电报风格全面改写，密度直接拉到头。

## 工作流

| Pass | 干啥 | 审批 | 压缩幅度 |
|------|------|------|---------|
| 1 | 机械清理 | 自动 | 10-25% |
| 2 | 保真压缩 | 逐项过 | +5-15% |
| 3 | 去人读内容 | 逐项过 | +15-25% |
| 4 | 电报改写 | 要明确开 | +25-40% |

Pass 1 拆包装，Pass 2 抽真空，Pass 3 扔掉人才看的说明书，Pass 4 全转速记。

## 设计思路

- 默认就是严格无损，压缩不是摘要
- 章节编号不动：交叉引用锚点不会乱
- 改了得批：非机械编辑都要你点头
- 23 条技巧带前后对比，看 `references/techniques.md`
- 12 种边界情况都考虑了，代码多的 prompt、混合语言文档都能处理

## 目录

```
lossless-prompt-compressor/
├── SKILL.md              — 操作手册（约 350 行）
└── references/
    └── techniques.md     — 技巧目录，带示例
```

## 作者

[Wallny](https://github.com/wallmage)
