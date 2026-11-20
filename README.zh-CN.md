<a id="readme-top"></a>

<div align="center">

# LLM Wiki Skill for Backlog.md

### 专为 Backlog.md 工作流打造、由 LLM 全程维护的知识维基

把你在 Backlog.md 中的任务、文档与决策，由 LLM 编译成一个持久、互连的 Markdown 知识代码库——无需向量数据库，也不增加额外目录。

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)
[![Claude Code plugin](https://img.shields.io/badge/Claude%20Code-plugin-CC785C?logo=anthropic&logoColor=white)](https://github.com/kuwork/llm-wiki-for-backlog)
[![GitHub stars](https://img.shields.io/github/stars/kuwork/llm-wiki-for-backlog?logo=github)](https://github.com/kuwork/llm-wiki-for-backlog/stargazers)
[![GitHub issues](https://img.shields.io/github/issues/kuwork/llm-wiki-for-backlog?logo=github)](https://github.com/kuwork/llm-wiki-for-backlog/issues)

[English](README.md) ｜ 简体中文

[功能亮点](#功能亮点) ·
[快速开始](#快速开始) ·
[核心命令](#核心命令) ·
[架构](#架构) ·
[关联项目](#关联项目) ·
[常见问题](#常见问题) ·
[许可证](#许可证)

</div>

<p align="center">
  <img src="https://images.javalearn.cn/blog/2026/04/ca4ced3ee79e5d70344731a61d1d7968.png" width="70%" alt="面向 Backlog.md 的 LLM Wiki 三层知识架构图，展示不可变的 Backlog.md 原材料目录、LLM 维护的维基层与查询产物层">
</p>

**LLM Wiki Skill for Backlog.md** 是一款专为 Backlog.md 工作流打造的技能，灵感来自 Andrej Karpathy 的 LLM Wiki 构想。它不像 RAG 那样每次提问都重新检索，而是让 LLM 把你 Backlog.md 各目录（`tasks/`、`docs/`、`decisions/` 等）中已有的材料，增量编译成一个持久、互连、就存放在 Backlog.md 项目内的 Markdown 维基。你继续在 Backlog.md 里工作并提问，其余的摘要、互连、归档与体检都由 LLM 完成。

> [!NOTE]
> 仓库：[github.com/kuwork/llm-wiki-for-backlog](https://github.com/kuwork/llm-wiki-for-backlog) ｜ 概念来源：[Karpathy 的 LLM Wiki](https://karpathy.ai/blog/wiki.html) ｜ 安装：下方两条 `/plugin` 命令

## 功能亮点

- 🧠 **为 Backlog.md 编译，而非一次性 RAG 检索**：在你的 Backlog.md 项目内，LLM 只构建一次知识并持续保鲜，每次摄取与提问都在复利积累，而不是每次从零重新检索。
- 🗂️ **Backlog.md 目录本身就是原材料层**：技能直接读取你在 Backlog.md 中已在用的 `tasks/`、`docs/`、`decisions/`、`drafts/`、`milestones/`、`archive/`、`completed/`、`assets/` 目录，无需平行的 `raw/` 目录，也不维护第二套结构。
- 🔎 **以 Backlog.md 日志为基线的增量摄取**：读取 Backlog.md 中 `wiki/log.md` 的时间戳基线，再用 `git status --porcelain` 与 `git log --since` 只重新处理变更过的 Backlog.md 来源；git 不可用时回退为全目录扫描。
- 🔁 **回流进 Backlog.md 维基**：回答 Backlog.md 问题时产出的报告、对比和图表会归档回 `wiki/`，让每一次提问都使同一个 Backlog.md 知识库更丰富。
- 🧬 **规则在 Backlog.md agent 文件中共进化**：维基规范注入项目现有的 Backlog.md agent 指令文件（`CLAUDE.md`、`AGENTS.md` 或 `GEMINI.md`），而非独立 schema 文件，随 Backlog.md 工作流一起演进。
- 🔗 **用维基链接串起 Backlog.md 来源**：所有交叉引用都使用可移植的 `[[wikilinks]]`（支持别名、属性与媒体嵌入），把从 Backlog.md 抽取的概念、实体与决策连接起来。
- 🛡️ **Backlog.md 原材料始终不可变**：LLM 绝不写入 Backlog.md 原材料目录；lint 体检会标记矛盾点、孤儿页面与失效的 `source_path` 回链。

## 这是什么

传统 RAG 只是“临时搬运工”，在提问时抓取片段，没有记忆与积淀。LLM Wiki 模式则是一台**知识编译器**——LLM 像维护代码库一样维护一个持久、互连的 Markdown 维基。本技能把这一构想专门适配到 Backlog.md：所有行为都围绕 Backlog.md 的目录、它的 `backlog init` 初始化以及 agent 指令文件来设计。

- **你的两件事**：持续向 Backlog.md 投入工作，并提出好问题。
- **LLM 的事**：其余一切——为 Backlog.md 维基做摘要、互连、归档、维护与体检。
- **维基本身**：存放在你 Backlog.md 项目内、一个只增不减的编译型产物。

> “Backlog.md 是 IDE，LLM 是程序员，维基则是代码库。”

## 快速开始

推荐通过 Claude Code 插件市场安装，两条命令即可，无需安装任何运行时：

```bash
/plugin marketplace add kuwork/llm-wiki-for-backlog
/plugin install llm-wiki-for-backlog@kuwork/llm-wiki-for-backlog
```

> [!TIP]
> 必须先初始化 Backlog.md。如果项目还没有 Backlog.md 结构，技能会停下并提示你先运行 `backlog init`，然后再构建维基。安装插件后，在 Backlog.md 项目里说 **“build wiki”**，即可创建 `wiki/` 结构并注入 agent 规范。

如果不想使用插件，可参考下方的手动克隆方式。

<details>
<summary><b>手动安装：从克隆仓库加载技能</b></summary>

克隆仓库，并让 AI 直接读取 `skills/llm-wiki-for-backlog/SKILL.md` 中的技能定义：

```bash
git clone https://github.com/kuwork/llm-wiki-for-backlog.git
```

</details>

## 核心命令

在 Backlog.md 项目内用自然语言触发即可，技能识别以下四类指令：

| 指令（触发词） | 功能 |
|---|---|
| `“build wiki”`（亦作 `“init wiki”`） | 创建 Backlog.md 的 `wiki/` 结构，并向 Backlog.md agent 指令文件注入维基规范；若已存在则跳过注入。 |
| `“ingest this”`（亦作 `“process this”`） | 读取变更的 Backlog.md 来源、撰写来源摘要、抽取概念与实体、更新 `index.md`/`log.md`，并执行配对记忆抽取。 |
| `“wiki query”` | 基于编译好的 Backlog.md 维基、带维基链接引用作答，产出对话回答、对比、报告、Marp 幻灯片或图表，并提示是否回流归档。 |
| `“lint wiki”`（亦作 `“health check”`） | 扫描 Backlog.md 维基中的矛盾点、过时结论、孤儿页面、缺失交叉引用，以及失效的 `source_path` 回链。 |

### 日常使用

在 Backlog.md 项目中启用技能后，遵循三步循环：

1. **初始化**：说“为这个 Backlog.md 项目构建维基”。
2. **继续在 Backlog.md 中工作**：你的 `tasks/`、`docs/`、`decisions/` 目录本身就是原材料。
3. **摄取**：说“摄取新的 Backlog.md 材料并更新维基”。

示例提示语：

- “为这个 Backlog.md 项目构建维基。”
- “摄取所有 backlog 内容并更新索引。”
- “基于维基对比 task-42 和 task-87 的方案。”
- “体检我的维基，找出矛盾点或孤儿概念。”
- “`decisions/` 里有哪些决策与当前维基综述相冲突？”

**回流时刻**：当 AI 产出值得保留的分析时，只需说：

> “把这份分析回流归档到维基的 comparisons 目录。”

## 架构

Backlog.md 项目分为三层，相关规则存放在 Backlog.md agent 指令文件中，而非独立 schema 文件：

1. **原材料层（不可变）**：Backlog.md 的 `tasks/`、`docs/`、`decisions/`、`drafts/`、`milestones/`、`archive/`、`completed/` 与 `assets/`。当 Backlog.md 目录位于代码仓库内时，`src/`、`lib/`、`packages/` 等同级源码目录可作为可选的额外来源。
2. **维基层（LLM 维护）**：Backlog.md 的 `wiki/` 包含 `index.md`、`log.md`、`overview.md`，以及 `sources/`、`concepts/`、`entities/`、`comparisons/`、`patterns/`、`decisions/`、`reasoning/`、`execution/`、`retrospectives/` 和可选的 `usermanual/` 等集合。
3. **产物层（可回流）**：Backlog.md 的 `wiki_output/` 存放生成的 `reports/`、Marp `slides/` 与 `charts/`；有价值的产物会回流进 `wiki/`。

**铁律**：Backlog.md 原材料目录永不修改；只有 LLM 写入 `wiki/`；产物回流进维基；`wiki/` 与 `wiki_output/` 永远不会被当作来源摄取，从而避免递归。

### 规模指引

| 规模 | 导航方式 | 工具 |
|---|---|---|
| 小型（少于 50 个 Backlog.md 来源） | 仅 `index.md` | 无需工具 |
| 中型（50–200） | `index.md` 加搜索 | 本地 BM25/向量搜索，如 `qmd` |
| 大型（200+） | 分块索引加搜索 | 完整搜索基础设施 |

<details>
<summary><b>查看完整 Backlog.md 目录布局</b></summary>

```text
{project-root}/
├── src/                      # 可选的项目源码（额外原材料）
├── {backlog}/                # Backlog.md 工作目录
│   ├── tasks/                # 第 1 层（不可变）：任务、需求、验收标准
│   ├── docs/                 # 第 1 层（不可变）：文档、指南、API 参考
│   ├── decisions/            # 第 1 层（不可变）：ADR 与设计决策
│   ├── drafts/               # 第 1 层（不可变）：草稿与进行中的笔记
│   ├── milestones/           # 第 1 层（不可变）：里程碑与路线图
│   ├── archive/              # 第 1 层（不可变）：已归档的任务与记录
│   ├── completed/            # 第 1 层（不可变）：已完成工作的最终总结
│   ├── assets/               # 第 1 层（不可变）：图片、附件、资源
│   ├── wiki/                 # 第 2 层（LLM 维护）：编译后的知识
│   │   ├── index.md          # LLM 优先读取的内容目录
│   │   ├── log.md            # 仅追加的时序操作日志
│   │   ├── overview.md       # 整个知识库的高层综述
│   │   ├── sources/          # 每个 backlog 来源一篇摘要，经 source_path 链回
│   │   ├── concepts/         # 深度概念文章
│   │   ├── entities/         # 人物、工具、项目、组织
│   │   ├── comparisons/      # 跨任务/文档/决策的分析
│   │   ├── patterns/         # 可复用的执行模式（需人工确认）
│   │   ├── decisions/        # 执行过程中的非正式微型决策
│   │   ├── reasoning/        # AI 规划轨迹与拆解理由
│   │   ├── execution/        # 从实现笔记中提炼的可复用知识
│   │   ├── retrospectives/   # 数据驱动的周期性复盘
│   │   └── usermanual/       # 可选的结构化、书籍式用户手册
│   └── wiki_output/          # 第 3 层：查询产物，可回流
│       ├── reports/
│       ├── slides/           # Marp 格式
│       └── charts/           # matplotlib / 可视化
└── ...                       # 其他项目文件（配置、测试等）
```

</details>

## 关联项目

Backlog.md 生态中实际内置或运行本技能的真实项目：

- **[Backlog.md](https://github.com/kuwork/Backlog.md)**：一款 Git 原生、Markdown 驱动的任务管理与看板 CLI，用于人类与 AI Agent 之间的协作。它把这套工作流作为内置的 `llm-wiki-for-backlog` 技能打包，通过 `backlog wiki install` 命令安装。
- **[DSH-Backlog.md](https://github.com/kuwork/DSH-Backlog.md)**：一个 DeepSeek Harness（DSH）bundle 插件，把 Backlog.md 本地 Web 看板（`backlog browser`）嵌入 Harness 界面，并运行本地 `backlog` CLI——即打包本维基技能的运行时，每个工作区使用独立进程。

## 常见问题

### 在 Backlog.md 中使用它需要安装 Node.js 或 Python 吗？

不需要。核心技能是 LLM 在你的 Backlog.md 项目中原生执行的指令集，无需安装运行时。唯一例外是可选的 `scripts/merge.py`，它是一个 Python 辅助工具，仅在你明确要把 Backlog.md 的 `wiki/usermanual/` 合并编译为单个文档时才运行。

### 它会修改我在 Backlog.md 里的任务、文档或决策文件吗？

不会。Backlog.md 原材料目录按规则不可变——LLM 只写入 `wiki/` 与 `wiki_output/`。它会改动的唯一现有项目文件是 Backlog.md agent 指令文件（`CLAUDE.md`/`AGENTS.md`/`GEMINI.md`）：`build wiki` 时会加入一个带明确标记的维基规范块，若已存在则跳过。

### 这和在 Backlog.md 上跑 RAG 有什么区别？

RAG 每次提问都从零推导答案，不积累任何东西；而本技能让 LLM 把 Backlog.md 来源一次性编译成持久、互连的维基并增量更新，关于你 Backlog.md 工作的知识得以保留和复利，而不是重复抓取。

### 当 Backlog.md 项目不在 git 仓库里时，增量摄取还能用吗？

可以。git 可用时，它用 `git status --porcelain` 与 `git log --since` 找出变更的 Backlog.md 文件；当 git 缺失或命令失败时，会回退为结合 `log.md` 对 Backlog.md 来源目录做全量扫描。

### 维基会不会意外摄取它自己的产物？

不会。即使 Backlog.md 的 `wiki/` 与 `wiki_output/` 位于 Backlog.md 目录内部，它们也被无条件排除在摄取范围之外，生成内容永远不会被当作原材料递归摄取。

## 已知限制

- **维基链接的渲染取决于查看器**：`[[wikilinks]]` 在支持维基链接的 Markdown 工具中会渲染为链接；普通 Markdown 查看器可能把它显示为字面文本。
- **不内置搜索基础设施**：技能不附带向量数据库；在中型（50–200）与大型（200+）的 Backlog.md 规模下，规模指引建议引入外部搜索工具。
- **一切都是 Backlog.md 本地 Markdown**：没有托管式界面或数据库——维基是 Backlog.md 项目内的纯文本文件，可移植性强，但浏览体验依赖你的编辑器或 Markdown 工具。

## 安全模型

> [!CAUTION]
> 本技能只**读取** Backlog.md 原材料目录（以及 `src/` 等可选源码目录），绝不写入；它只在 Backlog.md 的 `wiki/` 与 `wiki_output/` 内**写入**。在 `build wiki` 时，它会向 Backlog.md agent 指令文件追加一个由标记分隔的规范块，若该块已存在则跳过写入。变更检测使用只读 git 命令（`git status`、`git log`）。所有行为都定义在唯一、可审计的 `SKILL.md` 中；常规操作不运行任何后台脚本，`merge.py` 仅在你明确要求编译用户手册时才执行。

## 致谢

灵感来自 [Andrej Karpathy 的 LLM Wiki 构想](https://karpathy.ai/blog/wiki.html)，并为 Backlog.md 工作流做了适配。

## 许可证

基于 [MIT License](LICENSE) 开源。

<p align="right"><a href="#readme-top">↑ 回到顶部</a></p>
