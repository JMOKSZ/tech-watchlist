# GLM-OCR

> 智谱AI 开源 0.9B 多模态 OCR 模型 — OmniDocBench V1.5 排名第一，表格/公式/手写/印章全支持。

## 基本信息

| | |
|---|---|
| **仓库** | [zai-org/GLM-OCR](https://github.com/zai-org/GLM-OCR) |
| **语言** | Python |
| **Stars** | ⭐ 6,787（2026.2 创建，4 个月） |
| **许可** | 代码 Apache 2.0，模型 MIT |
| **定位** | 面向复杂文档理解的多模态 OCR 模型，SDK 一行安装，支持 Agent 集成 |

## 项目定位

GLM-OCR 是智谱AI（ZhipuAI）开源的轻量级 OCR 专用模型，基于 GLM-V 编解码架构，集成了 CogViT 视觉编码器 + GLM-0.5B 语言解码器。核心思路是两阶段流水线：先用 PP-DocLayout-V3 做版面检测切出文本区域，再用 MTP（Multi-Token Prediction）增强的 GLM-OCR 模型对每个区域并行 OCR。

与通用 VLM 做 OCR 不同，GLM-OCR 专为文档理解优化 — 表格转 Markdown、公式转 LaTeX、手写识别、印章等复杂场景都是内置能力。573 万 HuggingFace 下载量，有 20+ 作者的学术论文（arxiv.org/abs/2603.10910）背书。

## 技术分析

- **架构**: 两阶段流水线 — PP-DocLayout-V3 版面检测 → GLM-OCR 0.9B 并行识别 → ResultFormatter 输出 JSON/Markdown
- **模块化**: PageLoader（预处理）、OCRClient（模型服务）、PPDocLayoutDetector（版面）、ResultFormatter（后处理）四个可组合模块
- **部署**: 四种方式 — MaaS API（云端，无需 GPU）、vLLM/SGLang 自部署、Ollama/MLX（苹果 Silicon）、SDK Server+Client（GPU 机服务端 + 任意客户端）
- **Agent 集成**: 提供 Hermes/Claude Code 兼容的 Skill 定义（skills/ 目录），`pip install glmocr` + set API key 即被 agent 调用
- **成熟度**: v0.1.5，Alpha 阶段。最后推送 2026-04-21，核心开发可能告一段落。5 个实质贡献者，CI/CD 完善

## 与我相关

| 维度 | 评估 |
|------|------|
| H.OS 酒店视觉 | ⭐⭐⭐⭐⭐ — 证件/发票/菜单/合同识别直接可用 |
| Agent 工作流集成 | ⭐⭐⭐⭐⭐ — 已有 Hermes Skill，CLI 一行调用 |
| 文档数字化 | ⭐⭐⭐⭐⭐ — PDF/图片→Markdown+JSON，适合报表批量处理 |
| 表格与公式 | ⭐⭐⭐⭐ — 酒店财务报表的数据提取 |
| 部署成本 | ⭐⭐⭐⭐ — MaaS API 模式零 GPU 需求，pip install 即用 |

## 个人判断

目前最强的开源轻量 OCR 模型。与之前关注的 MiniCPM-V-4.6 相比，OCR 专精度更高（OmniDocBench #1），安装和调用简单太多（`pip install glmocr` + `glmocr parse image.png`）。MaaS 模式适合日常使用，高频场景可切换到 vLLM 本地部署。

建议先接入 Hermes agent — 群里有图片直接 OCR 返回文字，证件/发票识别场景最实用。

## TODO

- [ ] `pip install glmocr` 并配置智谱 API key
- [ ] 将 skills/glmocr/SKILL.md 接入 Hermes 技能库
- [ ] 用酒店发票/证件实测识别精度
- [ ] 评估是否纳入 H.OS 视觉 Agent 的标准工具
