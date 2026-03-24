<p align="center">
  <a href="README.md">English</a> |
  <a href="README.ko.md">한국어</a> |
  <a href="README.zh-CN.md">简体中文</a> |
  <a href="README.ja.md">日本語</a> |
  <a href="README.es.md">Español</a>
</p>

<p align="center">
  <img src="argus-combined-demo.gif" alt="Argus AI Demo" width="800">
</p>

<h1 align="center">Argus AI</h1>
<p align="center">
  <strong>基于 250+ YAML 检测规则和 AI 驱动威胁分析的 API 安全平台</strong>
</p>

<p align="center">
  <a href="https://github.com/mhb8436/argus-ai-releases/releases"><img src="https://img.shields.io/github/v/release/mhb8436/argus-ai-releases?label=Latest%20Release" alt="Release"></a>
  <a href="#license"><img src="https://img.shields.io/badge/License-AGPL_v3-blue.svg" alt="License"></a>
  <img src="https://img.shields.io/badge/Go-1.21+-00ADD8?logo=go" alt="Go Version">
  <img src="https://img.shields.io/badge/Rules-250+-orange" alt="Rules">
  <img src="https://img.shields.io/badge/OWASP-API_Top_10-red" alt="OWASP">
</p>

<p align="center">
  <a href="#argus-platform">平台</a> |
  <a href="#argus-cli">CLI</a> |
  <a href="#downloads">下载</a> |
  <a href="#comparison">对比</a>
</p>

---

Argus 是一个 API 安全平台，用于发现、分析和保护您的 API。它捕获 HTTP 流量，运行 250+ 基于 YAML 的安全规则（OWASP API Top 10），检测敏感数据（PII、凭证、令牌），并支持使用自然语言查询威胁。

## 两个产品

| | Argus 平台 | Argus CLI |
|--|:---:|:---:|
| **类型** | 基于服务器的平台 | 独立可执行文件 |
| **使用场景** | 持续 API 监控 | 按需安全测试 |
| **基础设施** | Kafka、Elasticsearch、PostgreSQL | 无需（内嵌 SQLite） |
| **界面** | Web 仪表板（React） | 终端（CLI + TUI） |
| **适用对象** | DevSecOps 团队、SOC | 渗透测试人员、审计人员、隔离网络 |

---

<h2 id="argus-platform">Argus 平台（服务器）</h2>

### 三步安全生命周期

**发现** -- 自动收集 API 资产清单并识别影子 API
**分析** -- 250+ 安全规则 + 敏感数据检测（DLP）
**响应** -- AI 驱动的自然语言查询 + 实时告警

### 核心功能

- **实时流量分析** -- Collector 通过网关/Sidecar/Agent 捕获 HTTP 流量
- **250+ YAML 安全规则** -- SQL Injection、XSS、SSRF、BOLA、Command Injection 等
- **双重扫描模式** -- 被动扫描（零影响）+ 主动扫描（渗透测试）
- **敏感数据检测** -- 信用卡（Luhn 算法）、SSN、JWT、电子邮件、电话号码
- **AI 自然语言查询** -- 通过 LLM（Azure OpenAI / Ollama）执行"显示上周的 SQL 注入攻击"等查询
- **自动化扫描** -- 基于 Cron 的定时扫描，支持保存测试配置
- **企业级集成** -- Slack、邮件、Webhook 告警 + RBAC + 域名过滤

### 架构

```
流量来源               平台                              存储
                    ┌─────────────────────────────────┐
 网关 ─────────┐    │                                   │
              ├───>│  Collector (:8081)                 │
 Sidecar ─────┤    │       │                           │
              │    │       v                           │
 日志 Agent ──┘    │     Kafka                         │──> Elasticsearch
                   │       │                           │
                   │       v                           │
                   │  Analyzer                         │──> PostgreSQL
                   │   ├─ Parser（HTTP 流量解析）        │
                   │   ├─ Detector（250+ YAML 规则）     │
                   │   ├─ Sensitive（PII 检测）          │──> Redis
                   │   └─ Notifier（Slack/邮件/WH）     │
                   │       │                           │
                   │       v                           │
                   │  Dashboard API (:8080)            │
                   │       │                           │
                   │       v                           │
                   │  React UI (:3000)                 │
                   └─────────────────────────────────┘
```

---

<h2 id="argus-cli">Argus CLI -- 独立安全测试工具</h2>

单个 Go 可执行文件，内嵌全部 250+ 规则。无需服务器、无需数据库、无需互联网。在终端中实现类似 Burp Suite 的功能。

```bash
# 被动扫描（适用于生产环境）
argus-cli scan passive https://api.example.com

# 主动扫描（发送攻击载荷）
argus-cli scan active https://api.example.com

# 完整扫描（爬取 + 被动 + 主动）
argus-cli scan full https://api.example.com

# 代理模式（拦截浏览器流量）
argus-cli proxy start --port 8082 --passive-scan

# KISA Web 漏洞评估（韩国标准）
argus-cli scan kisa https://api.example.com --level 2 --output report.xlsx

# 生成 HTML 报告
argus-cli report generate --format html --output report.html
```

### CLI 工具

| 工具 | 描述 | Burp Suite 对应功能 |
|------|------|---------------------|
| **Proxy** | 拦截和检查 HTTP/HTTPS 流量 | Proxy |
| **Repeater** | 编辑并重新发送请求 | Repeater |
| **Intruder** | 使用载荷进行自动化模糊测试 | Intruder |
| **Scanner** | 250+ YAML 规则，被动 + 主动扫描 | Scanner |
| **Crawler** | 发现端点和表单 | Spider |
| **Decoder** | Base64、URL、十六进制编码/解码 | Decoder |
| **Comparer** | 对比两个响应的差异 | Comparer |
| **Sequencer** | 令牌随机性分析 | Sequencer |

### KISA Web 漏洞评估

内置支持 [KISA](https://www.kisa.or.kr/) Web 漏洞评估 -- 28 个检查项目（WEB-001 ~ WEB-028），支持自动化扫描和 Excel 报告生成。

| 级别 | 项目数 | 描述 |
|------|-------:|------|
| Level 0（被动） | 7 | 目录索引、信息泄露、会话过期、Cookie 安全 |
| Level 1（半主动） | 9 | 响应头检查、认证测试 |
| Level 2（主动） | 12 | SQLi、XSS、SSRF、Command Injection |

---

<h2 id="downloads">下载</h2>

从 [Releases](https://github.com/mhb8436/argus-ai-releases/releases) 页面下载最新的可执行文件。

| 平台 | 架构 | 可执行文件 |
|------|------|-----------|
| macOS | Apple Silicon (arm64) | `argus-cli-darwin-arm64` |
| macOS | Intel (amd64) | `argus-cli-darwin-amd64` |
| Linux | amd64 | `argus-cli-linux-amd64` |
| Windows | amd64 | `argus-cli-windows-amd64.exe` |

```bash
# macOS (Apple Silicon)
curl -L -o argus-cli https://github.com/mhb8436/argus-ai-releases/releases/latest/download/argus-cli-darwin-arm64
chmod +x argus-cli
./argus-cli --help

# Linux
curl -L -o argus-cli https://github.com/mhb8436/argus-ai-releases/releases/latest/download/argus-cli-linux-amd64
chmod +x argus-cli
./argus-cli --help
```

---

<h2 id="comparison">对比</h2>

| 功能 | Argus | Akto | Burp Suite | ZAP | Nuclei |
|------|:-----:|:----:|:----------:|:---:|:------:|
| API 专项安全 | 是 | 是 | 部分 | 部分 | 部分 |
| 基于 YAML 的规则 | 250+ | 208 (OSS) | 专有 | 否 | 8000+ |
| 自定义规则 | 是 | 是 | 扩展 | 脚本 | 是 |
| 被动 + 主动扫描 | 两者 | 两者 | 两者 | 两者 | 仅主动 |
| 敏感数据检测（DLP） | 是 | 是 | 否 | 否 | 否 |
| AI 自然语言查询 | 是 | 否 | 否 | 否 | 否 |
| 独立 CLI（隔离网络） | 是 | 否 | 是 | 是 | 是 |
| 实时流量分析 | 是 | 是 | 是 | 是 | 否 |
| KISA 合规（韩国） | 28 项 | 否 | 否 | 否 | 否 |
| 类 Burp Suite 工具（Proxy、Intruder、Repeater） | 是（CLI） | 否 | 是（GUI） | 部分 | 否 |

---

## 安全规则（250+）

所有规则均基于 YAML，涵盖 22+ 个类别：

| 类别 | 规则数 | 示例 |
|------|-------:|------|
| Injection Attacks（SQLi、NoSQLi、XXE） | 30+ | 报错注入、联合查询注入、盲注 |
| Cross-Site Scripting（XSS） | 9 | 反射型、DOM 型、编码绕过 |
| Command Injection | 18 | Shell、Struts、Spring、Tomcat RCE |
| Server-Side Request Forgery | 8 | AWS/Azure/GCP 元数据重定向 |
| Broken Authentication | 25+ | JWT、2FA 绕过、凭证填充 |
| Security Misconfiguration | 25+ | 默认凭证、暴露面板、调试端点 |
| Local File Inclusion | 11 | 路径遍历、目录遍历 |
| 敏感数据检测 | 5 种类型 | 信用卡、SSN、JWT、电子邮件、电话号码 |
| ...更多 | | CORS、CRLF、SSTI、速率限制、GraphQL |

---

## 技术栈

| 层级 | 技术 |
|------|------|
| 后端 | Go 1.21+、Fiber v2 |
| 前端 | React 18、TypeScript、Vite |
| AI/LLM | Azure OpenAI、Ollama（本地） |
| 数据库 | PostgreSQL、Elasticsearch |
| 消息队列 | Apache Kafka |
| 缓存 | Redis |
| CLI 存储 | SQLite（内嵌） |

---

## 联系我们

如有疑问，请提交 [Issue](https://github.com/mhb8436/argus-ai-releases/issues)。

---

## 许可证

AGPL-3.0 with Commons Clause

Copyright (c) 2026 CRAFTICSYSTEMS Co., Ltd.
