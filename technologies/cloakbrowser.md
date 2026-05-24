# CloakBrowser

> C++ 源码级隐身的 Chromium 浏览器，通过全部 30 项反爬检测。

## 基本信息

| | |
|---|---|
| **仓库** | [CloakHQ/CloakBrowser](https://github.com/CloakHQ/CloakBrowser) |
| **语言** | Python + JavaScript（wrapper） / C++（patched Chromium 二进制） |
| **Stars** | ⭐ 19,500 |
| **Forks** | 1,500 |
| **许可证** | MIT（wrapper）/ 免费使用（二进制） |
| **安装** | `pip install cloakbrowser` 或 `npm install cloakbrowser` |
| **最新版本** | v0.3.30 (Chromium 146) |
| **平台** | Linux x64/arm64, macOS arm64/x64, Windows x64 |

## 项目定位

一个在 C++ 源码层修改 Chromium（58 处 source-level patch）的隐身浏览器，让反爬系统认为它就是正常 Chrome。**不是 JS 注入（playwright-stealth 那种），不是配置修改（undetected-chromedriver 那种）**——patches 编译进二进制文件，反爬系统无法从运行时检测。

它是 Drop-in 替代 Playwright/Puppeteer：同一套 API，改个 import 就行。

## 技术分析

### 隐身能力

| 检测服务 | 原生 Playwright | CloakBrowser |
|---------|:---:|:---:|
| reCAPTCHA v3 | 0.1 (bot) | **0.9** (human) |
| Cloudflare Turnstile | FAIL | **PASS** |
| FingerprintJS | DETECTED | **PASS** |
| BrowserScan | DETECTED | **NORMAL** (4/4) |
| deviceandbrowserinfo.com | 6 true flags | **0 true flags** |
| `navigator.webdriver` | `true` | **`false`** |

### 核心特性

1. **58 个 C++ 源码级 patch** — canvas, WebGL, audio, fonts, GPU, screen, WebRTC, 网络时序, automation 信号, CDP 输入行为
2. **humanize=True** — 一行开启人类行为模拟：贝塞尔曲线鼠标轨迹、打字节奏、滚动加减速
3. **指纹管理** — 随机 seed（每启动新身份）或固定 seed（保持同一身份反复访问）
4. **代理支持** — HTTP / SOCKS5 原生支持，`geoip=True` 自动匹配代理 IP 时区/语言环境
5. **自动更新二进制** — 后台更新检查，始终使用最新 stealth build
6. **零配置** — `pip install` → 二进制自动下载 → 直接用

### 额外配套

- **Browser Profile Manager** — 自托管替代 Multilogin/GoLogin/AdsPower，Docker 一键部署 + noVNC
- **CDP Server 模式** — Docker 运行持久隐身浏览器，远程通过 CDP 连接
- **框架集成** — 与 browser-use (70K⭐), Crawl4AI (58K⭐), Scrapling (21K⭐), Stagehand, LangChain, Selenium 等兼容

## 与现有方案对比

| 方案 | 隐身方式 | reCAPTCHA | Turnstile | 维护状态 |
|------|---------|:---:|:---:|:---:|
| playwright-stealth | JS 注入 | 0.3-0.5 | 偶尔 | 停滞 |
| undetected-chromedriver | 配置 patch | 0.3-0.7 | 偶尔 | 停滞 |
| Camoufox | C++ (Firefox) | 0.7-0.9 | PASS | 不稳定 beta |
| **CloakBrowser** | **C++ (Chromium)** | **0.9** | **PASS** | **活跃** |

## 与我相关

| 维度 | 评估 |
|------|------|
| 酒店数据采集 | ⭐⭐⭐⭐ — 爬 OTA 站的数据采集隐身层 |
| Trinity 爬虫 | ⭐⭐⭐⭐ — 可作批量采集的底层引擎 |
| AI Agent 浏览 | ⭐⭐⭐⭐⭐ — 配合 browser-use 做自动化竞对分析 |
| 价值 | 不是当前急需，但有战略储备价值 |

## 个人判断

对于现在 Trip.com UK 的酒店价格采集，CloakBrowser 不会有实质性改善——那站反爬本来就弱，瓶颈在业务逻辑和 IP 信誉，不在隐身能力。但作为工具链储备：等需要爬更硬的站（DataDome/Akamai/Kasada）或做 AI 自主浏览时，`pip install cloakbrowser` 就能上，无需重写代码。

最大的价值在 **browser-use / Crawl4AI 的隐身层** + **指纹管理（固定 seed 做回头客身份）** + **Docker 多实例并发**。

## 竞品/替代

- Camoufox（Firefox 版，但 beta 不稳定）
- playwright-stealth（JS 注入，已停滞）
- 无更成熟的 Chromium 源级隐身方案

## TODO

- [ ] 在 Trinity 有批量采集需求时引入
- [ ] 评估 Docker CDP Server 模式用于并发采集的架构
