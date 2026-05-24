# llm_wiki

> 基于 Karpathy 模式构建的桌面知识库应用，Tauri + React + Rust。

## 基本信息

| | |
|---|---|
| **仓库** | [nashsu/llm_wiki](https://github.com/nashsu/llm_wiki) |
| **技术栈** | Tauri v2 (Rust) + React 19 + TypeScript + Vite |
| **Stars** | ⭐ 9,000 |
| **Forks** | 1,100 |
| **许可证** | GPL v3 |
| **最新版本** | v0.4.12（5 天前） |
| **安装** | dmg / msi / deb / AppImage 或源码构建 |
| **定位** | 增量构建持久知识库，替代传统 RAG |

## 项目定位

LLM Wiki 将你的文档自动转化为结构化的、相互链接的知识库。与传统 RAG（每次查询重新检索）不同，LLM **增量构建并维护**一个持续更新的 Wiki 体系。知识被编译一次并保持更新，而不是每次查询都重新推导。

基于 Karpathy 的 [LLM Wiki 设计模式](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)，但实现了完整的桌面应用。

## 技术分析

### 架构特点

**三层架构：** Raw Sources（不可变原文） → Wiki（LLM 生成的页面） → Schema（规则与配置）

**双步 CoT Ingest：**
1. **分析步骤** — LLM 读取原文 → 结构化分析（实体/概念/矛盾/与现有知识的关系）
2. **生成步骤** — LLM 根据分析 → 生成 Wiki 页面 + 交叉引用 + 索引

**知识图谱（4 信号相关性模型）：**
| 信号 | 权重 | 说明 |
|------|:---:|------|
| 直接链接 | ×3.0 | [[wikilinks]] 交叉引用 |
| 来源重叠 | ×4.0 | 共享同一原始文档 |
| Adamic-Adar | ×1.5 | 共同邻居节点 |
| 类型亲缘 | ×1.0 | 同类型页面（实体+实体） |

### 关键差异点（对比 Synthadoc / Hermes skill）

| 特性 | Hermes skill | Synthadoc | llm_wiki |
|------|:---:|:---:|:---:|
| 图形界面 | ❌ | ❌ | ✅ Tauri 桌面 |
| 知识图谱可视化 | ❌ | ❌ | ✅ sigma.js + ForceAtlas2 |
| Louvain 社群检测 | ❌ | ❌ | ✅ 自动知识聚类 |
| Deep Research | ❌ | ❌ | ✅ Tavily/SerpApi 联网搜索 |
| 多格式文档 | ❌ | ❌ | ✅ PDF/DOCX/PPTX/XLSX |
| Chrome 剪藏 | ❌ | ❌ | ✅ Manifest V3 扩展 |
| 向量搜索 | ❌ | ❌ | ✅ 可选 LanceDB |
| 本地 HTTP API | ❌ | ❌ | ✅ 可被 AI Agent 调用 |
| 增量缓存 | ❌ | ❌ | ✅ SHA256 去重 |
| 多轮对话+引用 | ❌ | ❌ | ✅ 独立会话+溯源 |
| 中英文 | ✅ | ❌ | ✅ i18n |

### 额外亮点

- **purpose.md** — 比 schema 多一层"为什么建这个 Wiki"，LLM 每次 ingest/query 都读取，方向感更强
- **Graph Insights** — 自动发现"异常连接"和"知识盲区"，一键触发 Deep Research
- **Review 系统** — 异步人工审核队列，LLM 在 ingest 过程中标记需要人类判断的事项
- **KaTeX 数学渲染** — 支持 $\LaTeX$ 公式（但不一定与你的场景相关）
- **可配置上下文** — 4K 到 1M token 滑动条，适配不同 LLM

## 与我相关

| 维度 | 评估 |
|------|------|
| Trinity 知识后台 | ⭐⭐⭐⭐⭐ — 最适合做文档知识库的骨架 |
| 酒店运营文档管理 | ⭐⭐⭐⭐ — 持续 ingest 酒店政策、竞对报告、行业研究 |
| API 集成 | ⭐⭐⭐⭐ — 本地 API 可被豆包/PMS 调用 |
| 价值 | 高——如果 Trinity 需要知识后台，这是最成熟的候选方案 |

## 个人判断

这是目前最完整的 Karpathy Wiki 模式实现。和 Synthadoc 可以配合使用——Synthadoc 做上游文档矛盾检测和来源校验，llm_wiki 做下游知识库管理展示。

但如果只是简单需求（少量文档、不做知识图谱），Hermes 已有的 llm-wiki skill 配合我（Hermes Agent）处理就够用了，不需要装桌面应用。llm_wiki 的价值在**长期大量文档的持续管理 + 知识图谱可视化 + Deep Research 自动化**时才能充分体现。

## 竞品/替代

- **Synthadoc**（轻量 CLI，侧重矛盾检测，⭐266）
- **Karpathy 原始模式**（通过 Hermes llm-wiki skill 已可用，无 GUI）
- **Obsidian + AI 插件**（更强的笔记生态，但无自动化 ingest）

## 注意点

- GPL v3 许可证——如果 Trinity 体系要整合它，需要注意传染性
- 需要额外配置 LLM API Key（不走 Hermes provider 体系）
- v0.4.x 还在快速迭代，生产环境需要评估稳定性

## TODO

- [ ] 实际安装试用，评估 ingest 质量
- [ ] 评估 API 与 Trinity 后端对接的可行性
- [ ] 与 Synthadoc 做对比测试
