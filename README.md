<a id="readme-top"></a>

<div align="center">

# LLM Wiki Skill for Backlog.md

### The LLM-maintained knowledge wiki built specifically for the Backlog.md workflow

Your Backlog.md tasks, docs, and decisions, compiled by the LLM into one persistent, interlinked Markdown knowledge codebase — no vector database and no extra folder tree.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)
[![Claude Code plugin](https://img.shields.io/badge/Claude%20Code-plugin-CC785C?logo=anthropic&logoColor=white)](https://github.com/kuwork/llm-wiki-for-backlog)
[![GitHub stars](https://img.shields.io/github/stars/kuwork/llm-wiki-for-backlog?logo=github)](https://github.com/kuwork/llm-wiki-for-backlog/stargazers)
[![GitHub issues](https://img.shields.io/github/issues/kuwork/llm-wiki-for-backlog?logo=github)](https://github.com/kuwork/llm-wiki-for-backlog/issues)

English ｜ [简体中文](README.zh-CN.md)

[Features](#features) ·
[Quick Start](#quick-start) ·
[Core Commands](#core-commands) ·
[Architecture](#architecture) ·
[Projects](#projects-using-this-skill) ·
[FAQ](#faq) ·
[License](#license)

</div>

<p align="center">
  <img src="https://images.javalearn.cn/blog/2026/04/ca4ced3ee79e5d70344731a61d1d7968.png" width="70%" alt="LLM Wiki three-layer knowledge architecture for Backlog.md, showing immutable Backlog.md source folders, the LLM-maintained wiki, and query outputs">
</p>

**LLM Wiki Skill for Backlog.md** is a skill purpose-built for the Backlog.md workflow, inspired by Andrej Karpathy's LLM Wiki pattern. Instead of re-running retrieval on every question like RAG, the LLM incrementally compiles the material already in your Backlog.md folders — `tasks/`, `docs/`, `decisions/`, and the rest — into a durable, cross-linked Markdown wiki that lives inside your Backlog.md project. You keep working in Backlog.md and ask questions; the LLM handles all summarizing, linking, filing, and linting.

> [!NOTE]
> Repository: [github.com/kuwork/llm-wiki-for-backlog](https://github.com/kuwork/llm-wiki-for-backlog) ｜ Concept: [Karpathy's LLM Wiki](https://karpathy.ai/blog/wiki.html) ｜ Install: the two `/plugin` commands below

## Features

- 🧠 **Compiled for Backlog.md, not one-off RAG retrieval** — Inside your Backlog.md project the LLM builds knowledge once and keeps it current, so every ingestion and every query compounds instead of re-running retrieval from scratch.
- 🗂️ **Your Backlog.md folders are the raw layer** — The skill reads the `tasks/`, `docs/`, `decisions/`, `drafts/`, `milestones/`, `archive/`, `completed/`, and `assets/` folders you already use in Backlog.md; there is no parallel `raw/` directory and no second structure to maintain.
- 🔎 **Incremental ingestion keyed to the Backlog.md log** — It reads the timestamp baseline in Backlog.md's `wiki/log.md`, then uses `git status --porcelain` and `git log --since` to reprocess only changed Backlog.md sources, falling back to a full directory scan when git is unavailable.
- 🔁 **Flowback into the Backlog.md wiki** — Reports, comparisons, and charts produced while answering Backlog.md questions are filed back into `wiki/`, so each query makes the same Backlog.md knowledge base richer.
- 🧬 **Rules co-evolve in the Backlog.md agent file** — Wiki conventions are injected into the project's existing Backlog.md agent instruction file (`CLAUDE.md`, `AGENTS.md`, or `GEMINI.md`) rather than a separate schema file, so they evolve together with the Backlog.md workflow.
- 🔗 **Wikilinks between Backlog.md sources** — Every cross-reference uses portable `[[wikilinks]]` (with aliases, attributes, and embedded media) to connect the concepts, entities, and decisions extracted from Backlog.md.
- 🛡️ **Backlog.md source files stay immutable** — The LLM never writes to the Backlog.md raw folders, and lint flags contradictions, orphan pages, and broken `source_path` back-references.

## What It Is

Standard RAG acts as a "temporary courier": it fetches fragments at question time with no memory or accumulation. The LLM Wiki pattern is a **knowledge compiler** — the LLM maintains a persistent, interlinked Markdown wiki as if it were maintaining a codebase. This skill adapts that vision specifically to Backlog.md: every behavior is defined around Backlog.md's folders, its `backlog init` setup, and its agent instruction file.

- **Your two jobs:** keep feeding work into Backlog.md, and ask good questions.
- **The LLM's job:** everything else — summarize, cross-reference, file, maintain, and lint the Backlog.md wiki.
- **The wiki:** a living, compiled artifact inside your Backlog.md project that only grows.

> "Backlog.md is the IDE. The LLM is the programmer. The wiki is the codebase."

## Quick Start

The recommended path is the Claude Code plugin marketplace — two commands, no runtime to install:

```bash
/plugin marketplace add kuwork/llm-wiki-for-backlog
/plugin install llm-wiki-for-backlog@kuwork/llm-wiki-for-backlog
```

> [!TIP]
> Backlog.md must be initialized first. If the project has no Backlog.md structure, the skill stops and asks you to run `backlog init` before building the wiki. After installing the plugin, say **"build wiki"** inside a Backlog.md project and the `wiki/` structure plus agent guidelines are created.

Prefer not to use the plugin? See the manual clone option below.

<details>
<summary><b>Manual install: load the skill from a clone</b></summary>

Clone the repository and have your AI read the skill definition directly at `skills/llm-wiki-for-backlog/SKILL.md`:

```bash
git clone https://github.com/kuwork/llm-wiki-for-backlog.git
```

</details>

## Core Commands

Run these by natural language inside a Backlog.md project; they are the four triggers the skill understands:

| Say (trigger) | What it does |
|---|---|
| `"build wiki"` (also `"init wiki"`) | Creates the Backlog.md `wiki/` structure and injects wiki guidelines into the Backlog.md agent instruction file, skipping injection if already present. |
| `"ingest this"` (also `"process this"`) | Reads changed Backlog.md sources, writes source summaries, extracts concepts and entities, updates `index.md`/`log.md`, and runs pairing-memory extraction. |
| `"wiki query"` | Answers from the compiled Backlog.md wiki with wikilink citations, producing chat answers, comparisons, reports, Marp slides, or charts, then offers to file the result back. |
| `"lint wiki"` (also `"health check"`) | Scans the Backlog.md wiki for contradictions, stale claims, orphan pages, missing cross-references, and broken `source_path` back-references. |

### Everyday Use

A three-step loop after the skill is enabled in a Backlog.md project:

1. **Initialize** — say "Build a wiki for this Backlog.md project."
2. **Keep working in Backlog.md** — your `tasks/`, `docs/`, and `decisions/` folders are already the raw materials.
3. **Ingest** — say "Ingest new Backlog.md materials and update the wiki."

Example prompts:

- "Build a wiki for this Backlog.md project."
- "Ingest all backlog content and update the index."
- "Compare the approaches in task-42 and task-87 based on the wiki."
- "Lint my wiki to find contradictions or orphan concepts."
- "What decisions in `decisions/` contradict the current wiki overview?"

**The flowback moment:** when the AI produces an analysis worth keeping, just say:

> "File this analysis back into the wiki's comparisons directory."

## Architecture

The Backlog.md project is organized into three layers, backed by rules that live in the Backlog.md agent instruction file rather than a separate schema:

1. **Raw layer (immutable)** — Backlog.md's `tasks/`, `docs/`, `decisions/`, `drafts/`, `milestones/`, `archive/`, `completed/`, and `assets/`. Sibling source directories such as `src/`, `lib/`, or `packages/` are optional extra sources when the Backlog.md directory lives inside a code repository.
2. **Wiki layer (LLM-owned)** — Backlog.md's `wiki/` holds `index.md`, `log.md`, `overview.md`, and the `sources/`, `concepts/`, `entities/`, `comparisons/`, `patterns/`, `decisions/`, `reasoning/`, `execution/`, `retrospectives/`, and optional `usermanual/` collections.
3. **Output layer (flowback)** — Backlog.md's `wiki_output/` holds generated `reports/`, Marp `slides/`, and `charts/`; valuable results flow back into `wiki/`.

**Iron rules:** Backlog.md source folders are never modified; only the LLM writes to `wiki/`; outputs flow back into the wiki; and `wiki/` and `wiki_output/` are never ingested as sources, which prevents recursion.

### Scale Guidance

| Scale | Navigation | Tooling |
|---|---|---|
| Small (under 50 Backlog.md sources) | `index.md` alone | None needed |
| Medium (50–200) | `index.md` plus search | Local BM25/vector search such as `qmd` |
| Large (200+) | Chunked index plus search | Full search infrastructure |

<details>
<summary><b>Show the full Backlog.md repository layout</b></summary>

```text
{project-root}/
├── src/                      # Optional project source code (extra raw source)
├── {backlog}/                # Backlog.md working directory
│   ├── tasks/                # Layer 1 (immutable): tasks, requirements, acceptance criteria
│   ├── docs/                 # Layer 1 (immutable): documents, guides, API references
│   ├── decisions/            # Layer 1 (immutable): ADRs and design decisions
│   ├── drafts/               # Layer 1 (immutable): drafts and work-in-progress notes
│   ├── milestones/           # Layer 1 (immutable): milestones and roadmap
│   ├── archive/              # Layer 1 (immutable): archived tasks and records
│   ├── completed/            # Layer 1 (immutable): final summaries of completed work
│   ├── assets/               # Layer 1 (immutable): images, attachments, resources
│   ├── wiki/                 # Layer 2 (LLM-maintained): compiled knowledge
│   │   ├── index.md          # Content catalog the LLM reads first
│   │   ├── log.md            # Append-only chronological operation log
│   │   ├── overview.md       # High-level synthesis of the whole knowledge base
│   │   ├── sources/          # One summary per backlog source, linked back via source_path
│   │   ├── concepts/         # In-depth concept articles
│   │   ├── entities/         # People, tools, projects, organizations
│   │   ├── comparisons/      # Cross-task/doc/decision analyses
│   │   ├── patterns/         # Reusable execution patterns (human-confirmed)
│   │   ├── decisions/        # Informal micro-decisions made during execution
│   │   ├── reasoning/        # AI planning traces and decomposition rationale
│   │   ├── execution/        # Reusable knowledge from implementation notes
│   │   ├── retrospectives/   # Data-driven periodic retrospectives
│   │   └── usermanual/       # Optional structured, book-style user manual
│   └── wiki_output/          # Layer 3: query products, eligible for flowback
│       ├── reports/
│       ├── slides/           # Marp format
│       └── charts/           # matplotlib / visualization
└── ...                       # Other project files (config, tests, etc.)
```

</details>

## Projects Using This Skill

Real projects in the Backlog.md ecosystem that ship or run this skill:

- **[Backlog.md](https://github.com/kuwork/Backlog.md)** — A Git-native, Markdown-driven task-management and kanban CLI for collaboration between humans and AI agents. It packages this very workflow as its built-in `llm-wiki-for-backlog` skill, installed through the `backlog wiki install` commands.
- **[DSH-Backlog.md](https://github.com/kuwork/DSH-Backlog.md)** — A DeepSeek Harness (DSH) bundle plugin that embeds the Backlog.md local web kanban (`backlog browser`) into the Harness UI and runs the local `backlog` CLI — the runtime that ships this wiki skill — with one isolated process per workspace.

## FAQ

### Do I need to install Node.js or Python to use it with Backlog.md?

No. The core skill is an instruction set the LLM executes natively inside your Backlog.md project, with no runtime to install. The only exception is the optional `scripts/merge.py`, a Python helper you run explicitly when compiling a Backlog.md `wiki/usermanual/` into a single document.

### Will it modify my Backlog.md tasks, docs, or decision files?

No. The Backlog.md source folders are immutable by rule — the LLM only writes inside `wiki/` and `wiki_output/`. The one existing project file it changes is the Backlog.md agent instruction file (`CLAUDE.md`/`AGENTS.md`/`GEMINI.md`), where `build wiki` adds a clearly marked wiki-guidelines block and skips it if already present.

### How is this different from running RAG on my Backlog.md?

RAG re-derives an answer from scratch on every query and accumulates nothing. Here the LLM compiles Backlog.md sources once into a persistent, cross-linked wiki and updates them incrementally, so knowledge about your Backlog.md work is retained and compounded rather than re-fetched.

### Does incremental ingestion work when the Backlog.md project is not a git repository?

Yes. When git is available it uses `git status --porcelain` and `git log --since` to find changed Backlog.md files; when git is missing or fails, it falls back to a full scan of the Backlog.md source directories cross-referenced against `log.md`.

### Can the wiki accidentally ingest its own output?

No. The Backlog.md `wiki/` and `wiki_output/` folders are unconditionally excluded from ingestion even though they live inside the Backlog.md directory, so generated content can never be recursively re-ingested as a raw source.

## Known Limitations

- **Wikilink rendering depends on the viewer.** `[[wikilinks]]` render as links in wikilink-aware Markdown tools; plain Markdown viewers may show them as literal text.
- **No bundled search infrastructure.** The skill ships no vector database; at medium (50–200) and large (200+) Backlog.md scale the scale guidance recommends bringing external search tooling.
- **Everything is local Backlog.md Markdown.** There is no hosted UI or database — the wiki is plain files inside your Backlog.md project, which keeps it portable but means browsing relies on your editor or Markdown tooling.

## Security Model

> [!CAUTION]
> The skill only **reads** Backlog.md source folders (and optional source directories such as `src/`) and never writes to them. It **writes** only within the Backlog.md `wiki/` and `wiki_output/` folders, and on `build wiki` appends a marker-delimited guidelines block to the Backlog.md agent instruction file, skipping the write if that block already exists. Change detection uses read-only git commands (`git status`, `git log`). All behavior is defined in the single, auditable `SKILL.md`; no background scripts run during normal operations, and `merge.py` executes only when you explicitly ask to compile a user manual.

## Credits

Inspired by [Andrej Karpathy's LLM Wiki concept](https://karpathy.ai/blog/wiki.html), adapted for the Backlog.md workflow.

## License

Released under the [MIT License](LICENSE).

<p align="right"><a href="#readme-top">↑ Back to top</a></p>
