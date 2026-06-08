# turbovec

> Google TurboQuant 算法的高性能向量索引 — Rust 内核 + Python 绑定，比 FAISS 快 12-20%，内存省 16 倍

## 基本信息

| | |
|---|---|
| **仓库** | [RyanCodrai/turbovec](https://github.com/RyanCodrai/turbovec) |
| **论文** | [TurboQuant: Online Vector Quantization with Near-optimal Distortion Rate](https://arxiv.org/abs/2504.19874) (ICLR 2026) |
| **语言** | Rust 内核 / Python 绑定 |
| **Stars** | ⭐ 8,181 |
| **Forks** | 768 |
| **许可证** | MIT |
| **创建** | 2026-03-26（~2.5 个月） |
| **安装** | `pip install turbovec` |

## 项目定位

turbovec 是 Google Research TurboQuant 论文的完整 Rust 实现 + Python 绑定。它解决的是向量搜索中最核心的矛盾：**精度 vs 内存 vs 速度**。

传统方案 FAISS PQ 需要训练阶段、需要调参、ARM 上性能一般。turbovec 的方案是**数据无关量化** — 利用高维空间的一个数学性质：任何归一化后的向量在随机旋转后，每个坐标都服从已知分布（Beta → Gaussian）。所以不需要从数据中学习码本，Lloyd-Max 码本可以直接从数学推导预计算。

核心特性：
- **在线 ingest**，无需 train 阶段，无参数调优
- **2-bit / 4-bit 量化**，10M 文档 31GB → 4GB
- **SIMD 内核**：NEON (ARM) / AVX-512BW (x86)，手写汇编级优化
- **内核级过滤**：allowlist 直接在 SIMD 块内过滤，无需 over-fetch
- **纯本地**：无外部服务，可配合开源 embedding 做完全隔离的 RAG
- **框架集成**：LangChain / LlamaIndex / Haystack / Agno 四套 drop-in 替换

## 技术分析

### 架构

```
输入向量 (float32)
    ↓
归一化 → 存 norm (float)
    ↓
随机旋转 (固定正交矩阵)
    ↓
TQ+ 逐坐标校准 (shift + scale, 首次 add 时拟合)
    ↓
Lloyd-Max 标量量化 (2-bit: 4 桶, 4-bit: 16 桶)
    ↓
Bit-pack → 压缩存储
    ↓
搜索时: 旋转 query → SIMD LUT scoring → 长度重归一化 → top-k
```

### 性能数据（100K 向量, k=64）

| 平台 | 对比 FAISS | 2-bit | 4-bit |
|------|-----------|-------|-------|
| ARM (M3 Max) | 单线程 | +16% | +18% |
| ARM (M3 Max) | 多线程 | +12% | +20% |
| x86 (Sapphire Rapids) | 单线程 | ~持平 | +1-6% |

### 质量

- **Rust 测试 14 个文件 133+ 项** + **Python 测试 7 个文件 313+ 项** = 446+ 测试
- 测试代码 8,588 行，超过生产代码（Rust 4,136 行）
- **v0.7.0 审计驱动发布**：修复 14 个无声数据损坏 bug（含 NaN/Inf 污染、空加冻结校准等竞品不易发现的问题）
- CI 覆盖 Linux/macOS/Windows 三平台
- 单人开发 + Claude Opus 4.7 辅助，但代码质量经审计验证

### 限制

- **一人项目**：bus factor = 1
- 无 GitHub Releases（只有 git tag）
- 8181 stars / 40 subscribers — engagement 比例偏低（0.5%），但 star 时间分布渐变，非买榜
- 需要外部 embedding 模型配合使用

## 与我相关

| 维度 | 评分 | 说明 |
|------|:----:|------|
| H.OS 向量存储 | ⭐⭐⭐⭐⭐ | 直接替换 FAISS，`pip install turbovec` 即可，内存省 16 倍 |
| OTA 智能简报 | ⭐⭐⭐⭐ | 酒店数据 embedding 后语义搜索，turbovec 做底层存储 |
| 店总知识库 | ⭐⭐⭐⭐⭐ | LangChain 集成一行改 import，纯本地 RAG |
| 视觉 Embedding | ⭐⭐⭐⭐ | CLIP/MiniCPM embedding → 2-bit 量化，1M 张图 ~400MB |
| Mac Mini M3 | ⭐⭐⭐⭐⭐ | NEON 内核原生优化，比 x86 FAISS 还快 |

## 个人判断

**直接用。** 不是"研究一下"的程度。

对 H.OS 的向量存储层来说，turbovec 提供的 16 倍压缩 + ARM 原生加速 + 无需训练 + 框架集成，基本上是零成本升级。在 M3 Max 上比 FAISS 快 12-20%，这对 OTA 简报和知识库的实时性直接有利。

### 什么时候用

- 需要用 embedding 做语义搜索的任何 H.OS 组件
- 内存敏感的本地部署场景
- 需要纯本地/离线 RAG 的场景

### 什么时候不用

- 已经深度绑定 FAISS 特定功能（IndexIDMap 等）且迁移成本高
- 需要完全成熟的社区生态和长期维护保证（一人项目风险）

### 对比现有方案

| 维度 | FAISS | turbovec |
|------|-------|----------|
| 训练 | 需要 train + 调参 | 无需，在线 ingest |
| 压缩 | PQ 8-bit (~8x) | 2/4-bit (16x) |
| ARM 性能 | 一般 | 快 12-20% |
| 集成成本 | 需适配 | pip install 即替换 |
| 过滤搜索 | 先搜再过滤 | 内核级 allowlist |
| 生态成熟度 | 极成熟 | 早期，一人项目 |

### 建议行动

1. `pip install turbovec` 在 Mac Mini 上跑 benchmark
2. 对比当前 H.OS 向量存储的 recall 和速度
3. 如果满足需求，并入 H.OS RAG 管道

### TODO

- [ ] `pip install turbovec` 实测 benchmark
- [ ] 对比当前 FAISS 方案的 recall/speed/memory
- [ ] 如通过，替换 H.OS 向量存储层
