# Tel-Agent

> 把电话线接到 AI 的开源网关：来电先按规则分流 → AI 实时对话 → 转人工 / 记留言，全程录音转写可检索；同一 agent 兼管 9 个文本渠道。自托管、自带 API key，维也纳 Dpro GmbH 出品。

## 基本信息

| | |
|---|---|
| **仓库** | [Dpro-at/Tel-Agent](https://github.com/Dpro-at/Tel-Agent) |
| **组织** | Dpro GmbH（奥地利维也纳软件 / AI 咨询公司，FN 631492s） |
| **主导者** | Mohamed Alarade（GitHub: 9mtm，193 次提交中占 136） |
| **Stars** | ⭐ 282（fork 68） |
| **语言** | Python（FastAPI + LiveKit Agents）+ TypeScript（Next.js） |
| **License** | AGPL-3.0 + CLA（闭源集成需向 Dpro 购买商业授权） |
| **时间线** | 创建 2026-08-16 · 最新版 v0.1.1（2026-09-05，Win/macOS/DEB/RPM/Docker） |
| **状态** | pre-alpha；CI 当前为红（ruff 格式 + 语言包校验） |

## 项目定位

开源版的"AI 前台"。真正的入口是电话：来电先过路由规则（白名单 / 黑名单 / 营业时间 → 放行、拦截、交给 AI），AI 实时语音对话，可调用工具（转接、留言、查日历、调任意 HTTP 接口），全程录音并转写入库、可全文检索。同一套 agent 同时接管文本渠道，商家沿用自有 PBX（3CX / Asterisk / FreePBX）加一个分机即可接入。

**差异化**：自托管 + 自带凭据 + 不分渠道的单一 agent + 录音不出自己机器。对手（Retell / Vapi / Synthflow 等）清一色是云托管 SaaS，按分钟计费、数据在别人家。

## 实现状态（宣称 vs 实际）

| 能力 | 状态 |
|---|---|
| 9 个文本渠道：网页聊天、WhatsApp、Telegram、Messenger、Instagram、Discord、Slack、邮件、短信 | ✅ 已跑通 |
| 第二波 14 个渠道：Teams、Signal、Viber、Google Chat、Mattermost、Matrix、IRC、LINE、微信、企业微信、QQ、钉钉、飞书、iMessage | ⏳ 全部是未关闭的 issue，尚未实现 |
| **电话通道** | ⚠️ 代码已写（LiveKit 房间桥），作者自己在源码注释里标注 **"NOT proven"** —— 没接过真电话；官方里程碑里电话排 **11/12**，最后一位 |
| 语音栈 | Deepgram STT / 任意 OpenAI 兼容 LLM / ElevenLabs TTS；本地模型（Whisper / Piper / Ollama）排在 v1.1 |
| 延迟预算 | 端到端 <800ms，首句边说边生成，支持打断（barge-in，TTS 强制 `cancel()`） |
| 路由规则 / 工具 / Webhooks / MCP / 实时接管 / 健康告警 | 里程碑 4–9，未完成 |
| 界面语言 | en / de / ar / es / nl；**中文语言包为空（zh-Hans 只有占位文件）** |

> 首页挂 24 个渠道 logo，实际可用的是 9 + 1 个"未验证"。这是蓝图，不是交付物。

## 值得借鉴的架构（比产品本身更值钱）

1. **三分进程边界**：`agent/`（音频路径，800ms 硬预算，任何同步调用都算 bug）、`api/`（普通 Web 服务）、`web/`，三者**禁止互相调用，数据库是唯一边界**——防止慢查询 / 报表 / 导出把通话卡死。这条约束写在 `docs/ARCHITECTURE.md` 里，是一份很好的模板。
2. **渠道扩展契约 + `.claude/skills/channel-extension/SKILL.md`**：用一份 skill 教会 coding agent「加一个渠道要改哪 9 个文件、哪些签名/凭据规则不可谈、测试长什么样」，渠道实现从"复制上一个"变成"定义 + 传输"。
3. **文档纪律**：`docs/SPEC.md`（66KB 完整规格）+ ROADMAP（12 里程碑，**步 N+1 不在 N 跑通前开始**）+ 决策日志 `D-0xx` + `CLAUDE.md` 作为唯一工作契约。
4. **商业模型**：AGPL 免费自用（改了就开源）、闭源集成卖商业授权、官方明说"免费版不阉割"——开核模式的标准打法。

## 风险与保留

- **指标存疑**：282★ 但只有 **1 个 watcher**，fork/star 比 24% 偏高；star 时间线拉不到（API 需额外权限），暂不下结论。
- **徽章未验证**：官网挂 "Product of the day – 1st"，但在 Product Hunt 上搜不到对应条目。
- **巴士系数 1**：193 次提交里 136 次来自同一人；其余多为语言包的一次性 PR。
- **AI 大规模生成**：README 自认 "Built with Antigravity and Codex"，25 天 237 个 PR、2.1MB Python、80 个测试文件——不是缺陷，但属于"写得多、验证少"，alpha 阶段不能当成品。
- **国内落地障碍**：STT/TTS 只有 Deepgram/ElevenLabs、短信走 Twilio，均需替换；微信/飞书/钉钉渠道未做；无中文界面。LLM 侧无碍（OpenAI 兼容口，DeepSeek/Qwen 可用）。
- **隐私面是亮点**：代码里无任何遥测（已 grep 确认）、密钥落库加密、默认只监听 127.0.0.1、录音默认有告知提示。

## 同类对比

| 方案 | 形态 | 电话 | 中文 | 许可 | 备注 |
|---|---|---|---|---|---|
| **Tel-Agent** | 自托管开源 | 未验证 | ❌ 界面未汉化 | AGPL-3.0 + CLA | 渠道蓝图最全，架构纪律最好 |
| Bolna | 自托管开源 | ✅ 有 | 部分 | MIT | ⭐752，活跃三年，语音链路更成熟 |
| Retell / Vapi / Synthflow | 云 SaaS | ✅ 成熟 | ✅ | 商业 | 按分钟计费，数据在供应商侧 |

**独到之处**：把"来电先分流、后 AI"这条产品逻辑讲得最清楚的开源实现，并且自带一套可复制的工程纪律（进程边界、渠道契约、里程碑门禁）。

## 与我相关

| 维度 | 评估 |
|---|---|
| 酒店电话场景 | ⭐⭐⭐⭐ — "先分流再 AI"（总机 / 预订电话按号码和营业时间分流）比全 AI 接听现实得多；录音转写可检索是刚需 |
| 国内可落地性 | ⭐⭐ — 需自换 STT/TTS（CosyVoice/FunASR 或国内供应商），渠道与中文界面未跟上 |
| 架构借鉴 | ⭐⭐⭐⭐⭐ — 三分进程边界 + 渠道声明式契约 + 里程碑纪律，可直接抄进自研系统 |
| 商业参考 | ⭐⭐⭐ — AGPL + CLA 的开核模型：自用免费、闭源集成收费、免费版不阉割 |

## 个人判断

**值得抄架构，不值得现在部署。** 它的价值不在语音对话本身（作者自己都承认没接过一通真电话），而在于把"来电先分流、再交给 AI、录音转写全留档"这条产品逻辑，以及一套能落地的工程纪律写清楚了。

判断标准很简单：**等 Milestone 11（真实来电的延迟与打断）跑通再回来评估落地**。在那之前，把它当架构参考书，不当工具。

## TODO

- [ ] 等 Milestone 11（真实来电）跑通后复评语音可用性
- [ ] 抽出「三分进程边界」规则，写进自研系统的 ARCHITECTURE 约束
- [ ] 参考 `channel-extension/SKILL.md` 的写法，为自家渠道/集成写扩展 skill
- [ ] 跟踪 v1.1 本地模型（Whisper/Piper/Ollama）是否落地——决定国内可用性
- [ ] track fork 的 upstream 变更（已 fork 到 JMOKSZ/Tel-Agent）
