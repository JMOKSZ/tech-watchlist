# VoxCPM2

> Tokenizer-free TTS，30种语言，Voice Design（自然语言描述凭空生成声音）

## 基本信息

| | |
|---|---|
| **仓库** | [OpenBMB/VoxCPM](https://github.com/OpenBMB/VoxCPM) |
| **组织** | BAAI / OpenBMB（清华团队） |
| **Stars** | ⭐ 26,679 |
| **License** | Apache 2.0 |
| **首发** | 2025年9月（VoxCPM2: 2026年4月） |

## 项目定位

新一代 Tokenizer-free TTS 系统，绕过离散 token 化，直接通过端到端扩散自回归架构生成连续语音表示。VoxCPM2 是 2B 参数版本，200 万+小时多语种训练。

## 核心能力

| 能力 | 描述 |
|---|---|
| **多语言 TTS** | 30种语言（含粤语、四川话等方言），无需语言标签 |
| **Voice Design** 🎨 | 自然语言描述即可生成全新声音：`"(A young woman, gentle voice)Hello"` |
| **可控克隆** | 参考音频克隆音色 + 风格指令控制语速/情绪/语调 |
| **终极克隆** | 参考音频 + 精确转录，保留所有语音细节 |
| **质量** | 48kHz studio 级输出，无需外挂升采样器 |
| **实时流式** | RTF ~0.3 on RTX 4090，~0.13 with vLLM-Omni 加速 |

## 技术分析

- **架构**：MiniCPM-4 骨干 + Diffusion Autoregressive + AudioVAE V2
- **关键设计**：Token-free（连续表示），绕过离散 token 的语义损失
- **部署**：`pip install voxcpm`，三行代码出音频
- **成熟度**：26.7K Stars，持续活跃，有 HuggingFace 在线 Demo
- **社区**：飞书 + Discord 群，ReadTheDocs 文档齐全

## 同类对比

| 方案 | 克隆 | Voice Design | 语言 | 许可 | 状态 |
|---|---|---|---|---|---|
| **VoxCPM2** | ✅ 零样本 | ✅ 自然语言描述 | 30种 | Apache 2.0 | 活跃 |
| CosyVoice v3 | ✅ 零样本 | ❌ | 9语+18方言 | Apache 2.0 | 活跃 |
| GPT-SoVITS v2 | ✅ 1分钟微调 | ❌ | 5种 | MIT | 活跃 |
| Fish Speech S2 | ✅ 零样本 | ✅ 1.5万标签 | 80+种 | 限制商业 | 活跃 |

**独到之处**：唯一同时支持 Voice Design + 30语言 + Apache 2.0 完全开源的三合一方案。

## 限制

- **硬件**：需要 NVIDIA GPU（4090 推荐），不兼容 Apple Silicon / MPS
- **Python**：要求 ≥3.10 但 <3.13（当前环境 3.13 不可用）
- **体积**：2B 参数，16GB 显存起步

## 与我相关

| 维度 | 评估 |
|---|---|
| 粤语支持 | ⭐⭐⭐⭐⭐ — 直接支持粤语，这对大湾区场景非常关键 |
| 酒店业务 | ⭐⭐⭐ — 酒店多语言语音提示、自动电话应答场景有潜力 |
| H.OS 整合 | ⭐⭐⭐ — 可作为 H.OS 语音交互层的 TTS 引擎候选 |
| 本地部署 | ⭐⭐ — 需要独立 GPU 服务器，不能跑在 Mac mini 上 |

## 个人判断

当前最值得关注的 TTS 开源项目。Voice Design 是差异化亮点——其他方案（CosyVoice、GPT-SoVITS）在克隆质量上不输甚至更成熟，但凭空生成声音这个能力独此一家。Apache 2.0 许可对商业使用友好。

**下一步**：等 James 回 Mac mini 旁边，或在 VPS 上装一台带 GPU 的推理机，部署试用。

## 参考对比

详见 [TTS 开源方案横向对比表](../tts_comparison.md)。

## TODO

- [ ] 在有 NVIDIA GPU 的环境上部署测试
- [ ] 测试粤语 TTS + Voice Design 实际效果
- [ ] 评估作为 H.OS 语音引擎的可行性
