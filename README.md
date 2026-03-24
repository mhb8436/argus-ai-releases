<p align="center">
  <a href="README.md">English</a> |
  <a href="README.ko.md">한국어</a>
</p>

<p align="center">
  <img src="argus-combined-demo.gif" alt="Argus AI Demo" width="800">
</p>

<h1 align="center">Argus AI</h1>
<p align="center">
  <strong>API security platform with 250+ YAML-based detection rules and AI-powered threat analysis</strong>
</p>

<p align="center">
  <a href="https://github.com/mhb8436/argus-ai-releases/releases"><img src="https://img.shields.io/github/v/release/mhb8436/argus-ai-releases?label=Latest%20Release" alt="Release"></a>
  <a href="#license"><img src="https://img.shields.io/badge/License-AGPL_v3-blue.svg" alt="License"></a>
  <img src="https://img.shields.io/badge/Go-1.21+-00ADD8?logo=go" alt="Go Version">
  <img src="https://img.shields.io/badge/Rules-250+-orange" alt="Rules">
  <img src="https://img.shields.io/badge/OWASP-API_Top_10-red" alt="OWASP">
</p>

<p align="center">
  <a href="#argus-platform">Platform</a> |
  <a href="#argus-cli">CLI</a> |
  <a href="#downloads">Downloads</a> |
  <a href="#comparison">Comparison</a>
</p>

---

Argus is an API security platform that discovers, analyzes, and protects your APIs. It captures HTTP traffic, runs 250+ YAML-based security rules (OWASP API Top 10), detects sensitive data (PII, credentials, tokens), and lets you query threats using natural language.

## Two Products

| | Argus Platform | Argus CLI |
|--|:---:|:---:|
| **Type** | Server-based platform | Standalone binary |
| **Use case** | Continuous API monitoring | On-demand security testing |
| **Infrastructure** | Kafka, Elasticsearch, PostgreSQL | None (SQLite embedded) |
| **Interface** | Web dashboard (React) | Terminal (CLI + TUI) |
| **Best for** | DevSecOps teams, SOC | Pentesters, auditors, air-gapped networks |

---

<h2 id="argus-platform">Argus Platform (Server)</h2>

### 3-Step Security Lifecycle

**Discover** -- Auto-collect API inventory and identify shadow APIs
**Analyze** -- 250+ security rules + sensitive data detection (DLP)
**Act** -- AI-powered natural language queries + real-time alerts

### Key Features

- **Real-time traffic analysis** -- Collector captures HTTP traffic via gateway/sidecar/agent
- **250+ YAML security rules** -- SQL Injection, XSS, SSRF, BOLA, Command Injection, and more
- **Dual scan modes** -- Passive (zero-impact) + Active (penetration testing)
- **Sensitive data detection** -- Credit cards (Luhn), SSN, JWT, email, phone numbers
- **AI natural language queries** -- "Show me SQL injections from last week" via LLM (Azure OpenAI / Ollama)
- **Automated scanning** -- Cron-based scheduled scans with saved test configurations
- **Enterprise integrations** -- Slack, Email, Webhooks alerts + RBAC + domain filtering

### Architecture

```
Traffic Sources          Platform                          Storage
                    ┌─────────────────────────────────┐
 Gateway ─────┐    │                                   │
              ├───>│  Collector (:8081)                 │
 Sidecar ─────┤    │       │                           │
              │    │       v                           │
 Log Agent ───┘    │     Kafka                         │──> Elasticsearch
                   │       │                           │
                   │       v                           │
                   │  Analyzer                         │──> PostgreSQL
                   │   ├─ Parser (HTTP traffic)        │
                   │   ├─ Detector (250+ YAML rules)    │
                   │   ├─ Sensitive (PII detection)    │──> Redis
                   │   └─ Notifier (Slack/Email/WH)    │
                   │       │                           │
                   │       v                           │
                   │  Dashboard API (:8080)            │
                   │       │                           │
                   │       v                           │
                   │  React UI (:3000)                 │
                   └─────────────────────────────────┘
```

---

<h2 id="argus-cli">Argus CLI -- Standalone Security Testing</h2>

A single Go binary with all 250+ rules embedded. No server, no database, no internet required. Works like Burp Suite but in your terminal.

```bash
# Passive scan (safe for production)
argus-cli scan passive https://api.example.com

# Active scan (sends attack payloads)
argus-cli scan active https://api.example.com

# Full scan (crawl + passive + active)
argus-cli scan full https://api.example.com

# Proxy mode (intercept browser traffic)
argus-cli proxy start --port 8082 --passive-scan

# KISA web vulnerability assessment (Korea)
argus-cli scan kisa https://api.example.com --level 2 --output report.xlsx

# Generate HTML report
argus-cli report generate --format html --output report.html
```

### CLI Tools

| Tool | Description | Burp Suite Equivalent |
|------|-------------|----------------------|
| **Proxy** | Intercept & inspect HTTP/HTTPS traffic | Proxy |
| **Repeater** | Edit and resend requests | Repeater |
| **Intruder** | Automated fuzzing with payloads | Intruder |
| **Scanner** | 250+ YAML rules, passive + active | Scanner |
| **Crawler** | Discover endpoints and forms | Spider |
| **Decoder** | Base64, URL, hex encoding/decoding | Decoder |
| **Comparer** | Diff two responses | Comparer |
| **Sequencer** | Token randomness analysis | Sequencer |

### KISA Web Vulnerability Assessment

Built-in support for [KISA](https://www.kisa.or.kr/) web vulnerability assessment -- 28 check items (WEB-001 ~ WEB-028) with automated scanning and Excel report generation.

| Level | Items | Description |
|-------|------:|-------------|
| Level 0 (Passive) | 7 | Directory indexing, info disclosure, session expiry, cookie security |
| Level 1 (Semi-active) | 9 | Header checks, authentication tests |
| Level 2 (Active) | 12 | SQLi, XSS, SSRF, command injection |

---

<h2 id="downloads">Downloads</h2>

Download the latest binaries from the [Releases](https://github.com/mhb8436/argus-ai-releases/releases) page.

| Platform | Architecture | Binary |
|----------|-------------|--------|
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

<h2 id="comparison">Comparison</h2>

| Feature | Argus | Akto | Burp Suite | ZAP | Nuclei |
|---------|:-----:|:----:|:----------:|:---:|:------:|
| API-focused security | Yes | Yes | Partial | Partial | Partial |
| YAML-based rules | 250+ | 208 (OSS) | Proprietary | No | 8000+ |
| Custom rules | Yes | Yes | Extensions | Scripts | Yes |
| Passive + Active scan | Both | Both | Both | Both | Active only |
| Sensitive data detection (DLP) | Yes | Yes | No | No | No |
| AI natural language query | Yes | No | No | No | No |
| Standalone CLI (air-gapped) | Yes | No | Yes | Yes | Yes |
| Real-time traffic analysis | Yes | Yes | Yes | Yes | No |
| KISA compliance (Korea) | 28 items | No | No | No | No |
| Burp Suite-like tools (proxy, intruder, repeater) | Yes (CLI) | No | Yes (GUI) | Partial | No |

---

## Security Rules (250+)

All rules are YAML-based covering 22+ categories:

| Category | Rules | Examples |
|----------|------:|---------|
| Injection Attacks (SQLi, NoSQLi, XXE) | 30+ | Error-based, union-based, blind SQLi |
| Cross-Site Scripting (XSS) | 9 | Reflected, DOM-based, encoding bypass |
| Command Injection | 18 | Shell, Struts, Spring, Tomcat RCE |
| Server-Side Request Forgery | 8 | AWS/Azure/GCP metadata redirect |
| Broken Authentication | 25+ | JWT, 2FA bypass, credential stuffing |
| Security Misconfiguration | 25+ | Default creds, exposed panels, debug endpoints |
| Local File Inclusion | 11 | Path traversal, directory traversal |
| Sensitive Data Detection | 5 types | Credit card, SSN, JWT, email, phone |
| ...and more | | CORS, CRLF, SSTI, rate limiting, GraphQL |

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend | Go 1.21+, Fiber v2 |
| Frontend | React 18, TypeScript, Vite |
| AI/LLM | Azure OpenAI, Ollama (local) |
| Database | PostgreSQL, Elasticsearch |
| Message Queue | Apache Kafka |
| Cache | Redis |
| CLI Storage | SQLite (embedded) |

---

## Contact

For inquiries, please open an [issue](https://github.com/mhb8436/argus-ai-releases/issues).

---

## License

AGPL-3.0 with Commons Clause

Copyright (c) 2026 Argus Contributors
