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

[English](README.md) | 中文 | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [Deutsch](README.de.md) | [Français](README.fr.md) | [한국어](README.ko.md) | [Español](README.es.md) | [Italiano](README.it.md) | [Português](README.pt-BR.md)

## 为什么做这个

你 system prompt 里的每一个 token，都是每次 API 调用要交的税。一份 35,000 词的指令文件意味着大约 48,000 token 的上下文——模型还没读到用户消息呢，窗口就先被吃掉一大半。一天几千次请求乘上去，你其实是在为格式、重复内容和只有人才看的废话烧钱。

Lossless Prompt Compressor 就是干这个的。把 AI 指令文档——system prompt、CLAUDE.md、agent 规则文件、产品规格书、总体规划——拿过来压缩。不是摘要啊，是压缩。每条事实、每个字段名、每个约束、每个交叉引用都原封不动留着。砍掉的是包装：模型根本不需要的 Markdown 格式、重复表述、教程脚手架、鸡汤废话，还有那些花 token 但不带信息量的语法连接词。

一份典型的产品规格书，四趟跑完能压掉 50-65%。实打实省钱——每次调用都把更多上下文窗口留给真正重要的对话。

这个压缩器也是 [vibecheck](https://github.com/wallmage/vibecheck) 里压缩环节的核心。

## "无损"到底啥意思

这不是摘要。摘要是模型觉得什么重要就留什么，剩下扔了。指令文件这么搞很危险——少一条约束，模型就可能幻觉出你明确排除掉的功能。

无损的意思是：原文写"限制免费层补贴不超过月收入的 3%"，压缩后也说一模一样的话。没有事实被丢掉、改含糊、或者悄悄合并。章节编号钉死不动，"见 S8.7.3" 这种交叉引用不会断。AI 从压缩版生成的代码和架构决策，跟从原版生成的一样。

靠审批门来保证——每个非机械性的编辑都会以 diff 形式提给你看，你确认了才生效。

## 4 轮扫描，23 条技巧

压缩器分四轮逐步深入，每轮比上一轮更激进。你来决定停在哪。

### Pass 1 -- 机械清理（自动执行，压缩 10-25%）

没有主观判断，去掉的都是模型有没有都一样解析的格式：

- **Markdown 剥离**：`##` 标题、`**加粗**`、`- ` 列表符、`| 表格 |` 管道符、` ``` ` 代码围栏、`> ` 引用符、`---` 分隔线——全部去掉。文字和结构保留，装饰去掉。
- **格式转换**：表格变分号分隔行，列表变行内分号分隔项，链接语法 `[文本](url)` 收缩为 URL。
- **校验扫描**：剥离后全文扫一遍，抓漏网的 Markdown 残留。十六进制颜色（`#FF0000`）、正则通配符（`*.txt`）、shell 管道、YAML frontmatter 会被识别为内容，原样保留。

对应技巧目录的技巧 1-2。什么文档都能安全跑。典型节省：10-25% 词数。

### Pass 2 -- 保真压缩（需审批，额外 +5-15%）

这一轮开始有主观判断了。每个编辑提议都会展示给你，附预估节省量，批了才改：

- **交叉引用去重**（技巧 3）：三个地方说同一件事，保留一处规范表述，其余换成指针如"见 S6"。
- **代码块压缩**（技巧 4）：struct/class 定义变紧凑行内描述，字段名、类型、枚举值都留。Python/YAML/Makefile 例外，空白本身就是语义。
- **JSON 配置压缩**（技巧 5）：完整 JSON 示例变 schema 描述。
- **版本明细裁剪**（技巧 6）：汇总表和逐版本明细说的一样？明细砍掉。
- **鸡汤块压缩**（技巧 7）："为什么这很重要" 段落压到核心技术点。
- **设计理由压缩**（技巧 8）：四段论证变一句话加关键原因。
- **用例列表压缩**（技巧 9）：啰嗦的多段用例，每个压成一句。
- **ASCII 线框图压缩**（技巧 10）：费 token 的 ASCII 图变紧凑文字描述。

每条编辑都过校验：事实全在、AI 做出同样决策、没歧义、事实能追溯到规范章节。

### Pass 3 -- 去掉人读内容（需审批，额外 +15-25%）

判断标准就一个："去掉这段，AI 输出会变差吗？" 不会？那就删。

- **新手教程**（技巧 11）："从应用商店安装你的 IDE，大概要 30-60 分钟……" 压成装什么的清单，不教怎么装。
- **练习题和学习活动**（技巧 12）：AI 不需要练习题。
- **逐日排期**（技巧 13）：起床时间、咖啡休息砍掉，功能名和技术要求留。
- **鸡汤和激励内容**（技巧 14）："别慌""庆祝一下！" 对模型没有信息量。
- **验证矩阵**（技巧 15）：30 行表格每个功能都写着"发布"，压成两行就够了。
- **竞品对比表**（技巧 16）："我们全面领先" 激励人，不激励模型。
- **人类工作流示例**（技巧 17）："你的一天是这样的" 之类的走读。

Pass 3 保留"做什么"和"为什么"，去掉"人怎么做"。

### Pass 4 -- 电报改写（需明确同意，额外 +25-40%）

整篇文档用超高密度速记体重写。每个词都得带信息，纯粹为了人读着顺而存在的词，砍。适合 AI 是唯一读者的场景。

- **桥接短语消除**（技巧 18）："这意味着""从而使得""值得注意的是"——全砍。
- **分号分隔片段**（技巧 19）：完整句子变键值片段，冠词、"是/将"、填充主语都丢。
- **多句折叠**（技巧 20）：五句话解释一个概念？压成核心事实。
- **隐含主语省略**（技巧 21）：标题写了"搜索功能"，正文就不用每句再提了。
- **紧凑记法**（技巧 22）：斜杠表选项（`immediate/batched/silent`），箭头表流程（`offline -> local queue -> sync`），冒号表关系（`Auth: JWT + refresh`）。
- **重复结构模式压缩**（技巧 23）：描述一次模式，后面全用速记。

现代 LLM 读电报风格没问题，分号、箭头、斜杠分隔选项都认得。

## 模式

三种模式，逐级递进：

- **严格无损**（默认）：Pass 1 + Pass 2。事实一条不少，人还能读。适合你和 AI 都会查阅的文件——CLAUDE.md、共享 agent 规则、在维护的规格书。

- **AI 无损**：加上 Pass 3。教程、鸡汤、逐日排期、验证矩阵——只帮人类读者的内容全去掉。AI 拿到的信号一样。适合 system prompt 和 agent 指令这种你很少回头看的文件。

- **AI 专用**：加上 Pass 4。电报风格全面重写，密度拉满，人读着不舒服。适合高频生产 prompt——一天几千次调用，省一个 token 就是省一份钱。

## 工作流

| Pass | 干啥 | 审批 | 典型压缩幅度 |
|------|------|------|-------------|
| 1 | 剥离 Markdown 格式，转换表格/列表 | 自动 | 10-25% |
| 2 | 事实去重，压缩代码/JSON/论证 | 逐项 | +5-15% |
| 3 | 去教程、鸡汤、排期、人读脚手架 | 逐项 | +15-25% |
| 4 | 全面电报风格重写，片段代替句子 | 需明确同意 | +25-40% |

典型产品规格书四轮全跑：**压缩 50-65%**。35,000 词的文档，每次 API 调用少吃 25,000-32,000 个 token。

## 设计原则

- **默认无损**：Strict Lossless 是默认模式，想更激进得自己开。
- **章节编号不动**：交叉引用锚点不会被重新编号，哪怕周围章节删了。"见 S8.7.3" 始终有效。
- **审批把关**：非机械性的编辑都带 diff 和预估节省量提给你，不点头不改。
- **23 条技巧带示例**：完整前后对比目录在 `references/techniques.md`。
- **12 种边界情况都处理了**：短文档、代码密集型 prompt、已压缩输入、非英语/CJK 文本、嵌入的 URL、混合语言文档、讲 Markdown 的文档、文学/法律文本、超大文档，等等。

## 目录

```
lossless-prompt-compressor/
├── SKILL.md              — 操作手册（约 350 行）
└── references/
    └── techniques.md     — 完整技巧目录，带前后对比示例
```

## 作者

[Wallny](https://github.com/wallmage)
