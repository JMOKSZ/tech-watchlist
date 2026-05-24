# Understand Anything

> 把你的代码库、知识库或文档变成可交互的知识图谱。Tree-sitter + LLM 混合架构，多 Agent 管线。

## 基本信息

| | |
|---|---|
| **仓库** | [Lum1104/Understand-Anything](https://github.com/Lum1104/Understand-Anything) |
| **语言** | TypeScript（Monorepo，pnpm workspaces） |
| **Stars** | ⭐ 25,500 |
| **Forks** | 2,200 |
| **Commits** | 539 |
| **创建时间** | 2026年3月15日（2个月） |
| **许可证** | MIT |
| **平台支持** | Claude Code（原生）、Cursor、VS Code Copilot、Codex、Gemini CLI、**Hermes**、Cline、OpenCode、KIMI CLI 等 15 个平台 |

## 项目定位

Understand Anything 是一个 AI 编码助手插件（以 Claude Code Plugin 为原生形态），核心能力是**把代码库变成交互式知识图谱**。不是简单画图，而是让开发者能搜索、提问、理解代码架构的工具。

核心理念：*Graphs that teach > graphs that impress.*

解决了「加入新团队，面对 20 万行代码，从哪下手」这个经典问题。

## 技术分析

### 架构：Tree-sitter + LLM 混合

```
代码库 → [Tree-sitter 确定性解析] → [LLM 语义摘要] → [知识图谱 JSON] → [交互式 Dashboard]
                 ↓                          ↓
         function/class/import       人类可读摘要、标签、
         call chain（确定性）        架构分层（语义）
```

**Tree-sitter（确定性）** 负责：
- 解析 source → AST
- 提取 imports/exports、function/class 定义、调用点、继承关系
- 指纹变更检测（增量更新基础）
- 同样输入 → 同样输出，可重复

**LLM（语义）** 负责：
- 为每个节点写平实的英文摘要（what it does, not how）
- 分配标签、架构分层
- 生成引导 Tour
- 语言概念标注（12 种编程模式）

### 多 Agent 管线

`/understand` 命令编排 5 个专门 Agent：

| Agent | 职责 |
|-------|------|
| `project-scanner` | 发现文件、检测语言和框架 |
| `file-analyzer` | 提取 function/class/import，产出节点和边（最多 5 并发） |
| `architecture-analyzer` | 识别架构分层（API/Service/Data/UI/Utility） |
| `tour-builder` | 按依赖顺序生成学习引导 |
| `graph-reviewer` | 验证图谱完整性和引用一致性 |

另有 `domain-analyzer`（业务领域图）和 `article-analyzer`（Wiki 知识库）。

### Dashboard 可视化

React + Force Graph 交互式 Dashboard，支持：
- **力导向图** — 按架构层着色，可搜索可点击
- **领域视图** — 代码映射到业务 domain/flow/step 的水平图
- **Guided Tours** — 自动生成的架构学习路径
- **Diff Impact Analysis** — commit 前看波及范围
- **Persona-Adaptive UI** — 按角色（初级/PM/高级）调整信息密度
- **Fuzzy & Semantic Search** — 按名称或含义搜索
- **Layer Visualization** — 自动按 API/Service/Data 分组

### 30+ 语言支持

| 类别 | 语言 |
|------|------|
| 主流语言 | TypeScript、JavaScript、Python、Go、Rust、Java、Kotlin、C#、Ruby、PHP |
| 系统语言 | C、C++、Swift |
| Web | HTML、CSS、GraphQL |
| 数据 | SQL、Protobuf、Prisma |
| 配置 | YAML、JSON/JSONC、TOML、ENV |
| 基础设施 | Dockerfile、Kubernetes、Terraform |
| 文档 | Markdown、RST、Plaintext |
| CI/CD | GitHub Actions、Jenkinsfile、Makefile |

### 增量更新

第二次 `/understand` 只重新分析变更过的文件。基于指纹（fingerprint）检测变更类型：CONTENT（内容）、STRUCTURAL（结构）、DEPENDENCY（依赖）。STRUCTURAL 级别触发全量重分析。

### 知识图谱 Schema

13 种节点类型（file/function/class/module/concept/config/document/service/endpoint/pipeline/table/schema/resource）和 26 种边类型（imports/calls/contains/inherits/tested_by 等）。

### 跨平台兼容

| 平台 | 安装方式 |
|------|---------|
| Claude Code | 原生 Plugin Marketplace |
| Cursor / VS Code Copilot | 自动发现（clone 即用） |
| Codex / Gemini CLI / OpenCode | `install.sh <platform>` |
| **Hermes** | `install.sh hermes` → symlink 进 `~/.hermes/skills/` |
| Cline / KIMI CLI | `install.sh <platform>` |

## 与我相关

| 维度 | 评估 |
|------|------|
| Hermes 集成 | ⭐⭐⭐⭐⭐ — 已安装到本地 Hermes，`/understand` 可直接用 |
| 代码库理解 | ⭐⭐⭐⭐⭐ — 对 Trinity / Hermes Agent 等大型代码库，跑一次就出架构图 |
| 知识库分析 | ⭐⭐⭐⭐ — `/understand-knowledge` 支持 Karpathy 模式 Wiki（我 tech-watchlist 的格式） |
| 团队 Onboarding | ⭐⭐⭐⭐ — `/understand-onboard` 自动生成按依赖排序的学习路径 |
| 与 Hermes 互补 | ⭐⭐⭐⭐⭐ — Hermes 缺的就是「代码理解层」，这正是它的专长 |
| Dashboard | ⭐⭐⭐ — 需要本地浏览器，Telegram 场景用不了，但 `/understand-chat` 可替代 |

## 与已有关注项目的对比

| | LightRAG | **Understand Anything** | llm_wiki |
|---|---|---|---|
| 本质 | 图检索 RAG | **代码库/知识库 → 交互式图谱** | 桌面知识库应用 |
| 输入 | 文档（任意文本） | **代码 + Markdown Wiki** | 手动编辑 |
| 输出 | LLM 问答 API | **交互式可视化图谱 + Chat** | 人类可读的知识库 |
| 消费方式 | API 调用 | **Dashboard + `/understand-chat`** | 桌面 GUI |
| 适用场景 | 酒店数据问答 | **代码理解** | 知识管理 |
| Stars | ⭐35.6K | **⭐25.5K** | ⭐9K |

LightRAG 和 Understand Anything 其实互补：LightRAG 做「文本知识 → 图检索」，Understand Anything 做「代码结构 → 图可视化」。两者思路相似（图谱），但目标不同。

## 个人判断

这是目前为止最适合 Hermes 生态的外部工具。25.5K ⭐，2 个月达到，社区热度极高。核心设计（Tree-sitter + LLM 混合）和 Hermes 的「工具调用 + Agent 管线」哲学一致。

最大的实用价值：

1. **对 Hermes Agent 自身** — 跑一次 `/understand` 就出了 100 节点/105 边的工具层图谱，后续可以增量更新
2. **对 Trinity** — 如果 Trinity 代码在锦江机器上，跑一次能出架构全貌
3. **知识库分析** — `/understand-knowledge` 直接支持我 tech-watchlist 的 Wiki 格式

当前痛点：在 Telegram 上 Dashboard 用不了，但 `/understand-chat` 做文本问答够用了。

## TODO

- [x] 安装到本地 Hermes（`install.sh hermes`）
- [x] 修复 symlink skill 加载 bug
- [x] 对 Hermes Agent tools/ 跑完整分析 → 100 节点图谱
- [x] 配置 Dashboard（http://127.0.0.1:5173）
- [ ] 对 Hermes Agent 全量代码跑一次（3500 文件，看能不能撑住）
- [ ] 试 `/understand-knowledge` 分析 tech-watchlist wiki
- [ ] 评估能否在 Trinity CI 中集成 `/understand` 自动生成架构图
