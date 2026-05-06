# LLM Wiki Skill for Backlog.md

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)

[中文文档](#-为什么选择它) | [English Document](#-why-this)

---

## 🚀 为什么选择它？

**源自大神 Andrej Karpathy 的“第二大脑”构想。**

传统的 RAG（检索增强生成）只是“临时搬运工”，在你提问时临时抓取片段，没有积淀。而 Karpathy 提倡的 **LLM Wiki** 模式则是 **“知识编译器”**。

相比于其他的 CLI 命令行工具或简单的 RAG 插件，本项目的优势在于：

1.  **AI 原生实现 (AI-Native)**：无需安装 Node/Python 环境。它不是死板的代码逻辑，而是直接注入 AI 的“大脑指令集”，利用 AI 的原生推理能力处理复杂的文件合并与冲突。
2.  **零额外 raw 目录**：直接复用 Backlog.md 现有的 `tasks/`、`docs/`、`decisions/` 等目录作为原材料层，无需维护两套文件结构。
3.  **Git 感知增量摄取**：批量摄取时自动通过 `git status` 和 `git log --since` 检测自上次摄取以来的变更文件，跳过未更改内容，大幅提速。
4.  **核心回流机制 (Flowback)**：这不仅是资料的“摄取器”，更是知识的“生成器”。它强调将对话中产生的深度分析报告**自动归档**回 Wiki，实现知识的闭合复利。
5.  **共进化的 Schema**：Wiki 规范直接注入项目现有的 agent instruction 文件（`CLAUDE.md` / `AGENTS.md` / `GEMINI.md`），随项目一起进化，无需单独维护 schema 文件。

> “Backlog.md 是 IDE，LLM 是程序员，Wiki 则是代码库。”

## 📂 目录架构

<img src="https://images.javalearn.cn/blog/2026/04/ca4ced3ee79e5d70344731a61d1d7968.png" width="50%" alt="LLM Wiki 架构图">

```text
{project-root}/
├── src/                      # 项目源码（可选的额外原材料）
├── {backlog}/                # Backlog.md 工作目录
│   ├── tasks/                # 不可变层：任务、需求、验收标准
│   ├── docs/                 # 不可变层：文档、指南、API 参考
│   ├── decisions/            # 不可变层：ADR、设计决策、决策依据
│   ├── drafts/               # 不可变层：草稿、头脑风暴、待完善笔记
│   ├── milestones/           # 不可变层：里程碑定义、路线图
│   ├── archive/              # 不可变层：已归档的任务和记录
│   ├── completed/            # 不可变层：已完成任务的最终总结
│   ├── assets/               # 不可变层：图片、附件、资源
│   ├── wiki/                 # LLM 维护层。编译后的知识产物。
│   │   ├── index.md          # 导航地图：供 LLM 快速检索
│   │   ├── log.md            # 操作日志：追加记录历史操作（带精确时间戳）
│   │   ├── overview.md       # 高层综述：对整个库的系统总结
│   │   ├── sources/          # 来源摘要：链接回 backlog 原始文件
│   │   ├── concepts/         # 概念解析：对特定主题的深度探讨
│   │   ├── entities/         # 实体：人物、工具、项目、组织
│   │   └── comparisons/      # 横向对比：跨任务/文档/决策的交叉分析
│   └── wiki_output/          # 结果层：生成的报告、PPT 等，可“回流”至 wiki/
│       ├── reports/
│       ├── slides/           # Marp 格式
│       └── charts/           # matplotlib / 可视化
└── ...                       # 其他项目文件（配置、测试等）
```

## 🛠️ 核心操作

| 指令 (Trigger) | 功能描述 |
|---|---|
| `build wiki` | 初始化 `backlog/wiki/` 结构，并将规范注入项目 agent instruction 文件。 |
| `ingest this` | 摄取原始素材，并将其织入维基百科的织锦中。 |
| `wiki query` | 基于已有的维基知识库提问，并生成可回流的产物。 |
| `lint wiki` | 健康检查：发现知识盲区、矛盾点和孤儿页面。 |

## 📦 安装与使用 (Installation & Usage)

你可以通过以下两种方式将此技能赋予你的 AI：

### 模式 A：插件安装 (推荐)
适用于 Claude Code 用户，一键集成到你的指令集：
```bash
/plugin marketplace add cosen1024/llm-wiki-skill
/plugin install llm-wiki-skill@cosen1024/llm-wiki-skill
```

### 模式 B：技能加载 (手动)
如果你不希望安装插件，可以手动克隆仓库，并让 AI 直接读取 `skills/llm-wiki-for-backlog/SKILL.md` 的内容：

```bash
git clone https://github.com/cosen1024/llm-wiki-skill.git
```

---

## 🚀 Why this?

**Inspired by Andrej Karpathy's "Second Brain" Vision.**

Standard RAG is just a "temporary courier" — it fetches fragments when you ask, but has no memory or accumulation. Karpathy's **LLM Wiki** pattern is a **"Knowledge Compiler"**.

This project transforms that high-level vision into an **AI-Native Skill tailored for Backlog.md**:
- **The Vision**: Move away from complex vector databases. Let the LLM maintain a persistent, interlinked Markdown Wiki as if it were maintaining a codebase. Every ingestion is an integration; every query is a compounding interest.
- **Backlog-Native**: No separate `raw/` folder needed. Your existing `tasks/`, `docs/`, `decisions/`, `drafts/`, `milestones/`, `archive/`, `completed/`, and `assets/` directories are the raw layer. Project source directories (`src/`, `lib/`, `packages/`) can optionally be included too.
- **Git-Aware Incremental Ingestion**: Uses `git status` and `git log --since` to detect only changed files since the last ingestion, skipping untouched content for massive speedups.
- **AI-Native Implementation**: This is not just software; it's an **"Instruction Set for the Brain."** It leverages the native file-op capabilities of Claude Code directly, making it the most lightweight and intelligent way to manage knowledge.

## 📖 Usage Guide

After enabling the skill, interact with the AI using **natural language**:

### 1. Three-Step Quick Start
1.  **Initialize**: "Build a knowledge base for [Your Domain]."
2.  **Feed Sources**: 在 Backlog.md 的 `tasks/`、`docs/`、`decisions/` 等目录中继续你的日常工作。这些目录本身就是原材料。
3.  **Ingest Knowledge**: "Ingest new materials and update the wiki."

### 2. Example Prompts
*   "Build a wiki for this project."
*   "Ingest all backlog content and update the index."
*   "Compare the approaches in task-42 and task-87 based on the wiki."
*   "Lint my wiki to find contradictions or orphan concepts."
*   "What decisions in `decisions/` contradict the current wiki overview?"

### 3. The Magic: Flowback
When the AI provides a brilliant analysis, simply say:
> "File this analysis back into the wiki's comparisons directory."

---

## 🤝 Credits

Inspired by [Andrej Karpathy's LLM Wiki concept](https://karpathy.ai/blog/wiki.html).

## 📄 License

MIT
