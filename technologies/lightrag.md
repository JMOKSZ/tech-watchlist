# LightRAG

> 基于知识图谱的检索增强生成系统。不是传统向量 RAG，而是把文档建成实体关系图再检索。

## 基本信息

| | |
|---|---|
| **仓库** | [HKUDS/LightRAG](https://github.com/HKUDS/LightRAG) |
| **论文** | [arXiv 2410.05779](https://arxiv.org/abs/2410.05779) — EMNLP 2025 |
| **语言** | Python |
| **Stars** | ⭐ 35,600 |
| **Forks** | 5,000 |
| **Commits** | 7,853 |
| **安装** | `uv pip install lightrag-hku` 或 `pip install lightrag-hku` |
| **许可证** | MIT (未明确标注，参考常见开源) |

## 项目定位

LightRAG 是香港大学数据科学实验室（HKUDS）开发的图增强 RAG 系统。已发表于 EMNLP 2025。

与**传统 RAG**（将文档切段 → 向量化 → 按相似度检索）不同，LightRAG 在索引阶段先让 LLM 从文档中提取**实体**和**实体间关系**，构建一个知识图谱（Knowledge Graph），然后在该图谱上进行检索。检索过程分**双层级**：

- **低级检索** — 查具体实体（如"酒店 X 的评分"）
- **高级检索** — 查主题/关系（如"深圳南山区酒店的整体竞争格局"）

两个层级的检索结果合并后输入 LLM 生成答案。这种设计让 LLM 不仅能找到相关片段，还能理解实体之间的关联关系。

## 技术分析

### 架构

```
文档 → [LLM 实体+关系提取] → 知识图谱 (Neo4j / PostgreSQL / MongoDB / OpenSearch)
                                        ↓
用户查询 → [低级: 实体检索] + [高级: 关系检索] → [Reranker] → [LLM 生成] → 答案
```

### 核心技术区别

| 维度 | 传统 RAG | LightRAG |
|------|---------|----------|
| 索引方式 | 切段→向量化→存储 | 提取实体+关系→建KG→存图+向量 |
| 检索逻辑 | 向量相似度找段落 | 图遍历找关联实体 + 向量检索双通道 |
| 跨文档关联 | 隐含在向量空间（不可解释） | 显式实体关系链接（可解释） |
| 增量更新 | 重算或追加向量 | ✅ 增量更新 KG，无需全量重建 |

### 性能表现

LightRAG 在论文中全面优于 NaiveRAG、RQ-RAG、HyDE 和微软 GraphRAG：

| 对比对象 | 综合得分 | LightRAG 胜率 |
|---------|:------:|:------------:|
| vs NaiveRAG | 40.0% → **60.0%** | 全维度领先 |
| vs RQ-RAG | 40.0% → **60.0%** | 全维度领先 |
| vs HyDE | 42.4% → **57.6%** | 全维度领先 |
| vs GraphRAG (微软) | 50.4% → **52.8%** | 全面领先（CS/Legal/Agri 胜，Mix平） |

### 存储后端

支持四种统一存储方案：Neo4j（图数据库原生）、PostgreSQL（含 pgvector）、MongoDB、OpenSearch

### LLM 与嵌入要求

- **LLM**：推荐 ≥32B 参数，≥32K 上下文（64K 更佳）。索引阶段不建议使用推理模型
- **Embedding**：推荐 BAAI/bge-m3 或 text-embedding-3-large
- **Reranker**：可选，推荐 BAAI/bge-reranker-v2-m3 或 Jina

### 生态系统

| 项目 | 说明 |
|------|------|
| LightRAG | 核心：图检索 RAG |
| RAG-Anything | 多模态 RAG（图像/表格/公式/MinerU/Docling） |
| VideoRAG | 超长视频理解 RAG |
| MiniRAG | 用小型模型运行 RAG |

### 其他关键特性

- **增量更新** — 新文档只需更新相关实体，不重算全图
- **文档删除** — 删除文档后自动重建 KG
- **角色分离** — 支持为 EXTRACT（提取）、QUERY（查询）、KEYWORDS（关键词）、VLM（视觉）四个角色配置独立的 LLM
- **分块策略** — 支持 Fix / Recursive / Vector / Paragraph 四种分块策略
- **WebUI 可视化** — 知识图谱、拓扑布局、节点查询、子图过滤
- **Langfuse + RAGAS** — 可观测性与评估集成
- **Docker + Kubernetes** — 生产部署支持（Helm chart + Docker Compose）
- **Ollama 兼容 API** — 可伪装为 Ollama 模型供 Open WebUI 等前端调用

## 与我相关

| 维度 | 评估 |
|------|------|
| Trinity 知识问答引擎 | ⭐⭐⭐⭐⭐ — 酒店知识的关系型检索，传统 RAG 做不到 |
| 酒店竞对分析 | ⭐⭐⭐⭐⭐ — "哪些酒店跟我们是同档竞对"能从 KG 中推理 |
| 知识图谱可视化 | ⭐⭐⭐⭐ — 自带 WebUI 可直接给团队用 |
| 增量文档导入 | ⭐⭐⭐⭐⭐ — 酒店报告一篇篇增量加入即可 |
| 部署成本 | ⭐⭐⭐ — 需要 ≥32B LLM + 存储后端，不是零配置方案 |

## 与已有关注项目的对比

| | Synthadoc | llm_wiki | **LightRAG** |
|--|:---:|:---:|:---:|
| 本质 | 文档→Wiki | 知识库应用 | **图检索 RAG** |
| 产出 | 人类可读的结构化 Wiki | Obsidian 兼容 Vault | **LLM 可检索的知识图谱** |
| 消费方式 | 人阅读 | 人阅读 | **API → LLM 自动回答** |
| Stars | ⭐266 | ⭐9K | **⭐35.6K** |
| 互补而非替代 | ✅ 做文档预处理 | ✅ 做人工审核界面 | **✅ 做智能检索引擎** |

三者可以串联使用：**Synthadoc** 做文档矛盾检测 → **LightRAG** 做知识图检索 → **llm_wiki** 做人类可读的展示/审核界面。

## 个人判断

这是当前已关注项目中社区最活跃（35.6K⭐/7.8K commits）、学术基础最扎实（EMNLP 2025）的一个。与 llm_wiki / Synthadoc 不在同一赛道——它们是知识管理，LightRAG 是智能检索。

对于 Trinity 场景，如果目标是"人类阅读知识库"→ llm_wiki。如果目标是"让 LLM 能准确回答关于酒店数据的问题"→ **LightRAG 是更好的选择**。

最大门槛是 LLM 需求（≥32B），但在 2026 年这已经不是问题。建议用 PostgreSQL 后端先本地跑一个 demo，拿几篇酒店报告实测。

## TODO

- [ ] 安装试用：`uv pip install lightrag-hku`，用 PostgreSQL 做存储后端
- [ ] 用 3-5 篇酒店竞对报告测试检索质量
- [ ] 评估能否与 Trinity 的豆包/PMS 后端集成
- [ ] 对比 Neo4j vs PostgreSQL 后端的性能差异
