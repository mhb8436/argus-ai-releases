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
  <strong>250以上のYAMLベース検出ルールとAI駆動の脅威分析を備えたAPIセキュリティプラットフォーム</strong>
</p>

<p align="center">
  <a href="https://github.com/mhb8436/argus-ai-releases/releases"><img src="https://img.shields.io/github/v/release/mhb8436/argus-ai-releases?label=Latest%20Release" alt="Release"></a>
  <a href="#license"><img src="https://img.shields.io/badge/License-AGPL_v3-blue.svg" alt="License"></a>
  <img src="https://img.shields.io/badge/Go-1.21+-00ADD8?logo=go" alt="Go Version">
  <img src="https://img.shields.io/badge/Rules-250+-orange" alt="Rules">
  <img src="https://img.shields.io/badge/OWASP-API_Top_10-red" alt="OWASP">
</p>

<p align="center">
  <a href="#argus-platform">プラットフォーム</a> |
  <a href="#argus-cli">CLI</a> |
  <a href="#downloads">ダウンロード</a> |
  <a href="#comparison">比較</a>
</p>

---

Argusは、APIを検出・分析・保護するAPIセキュリティプラットフォームです。HTTPトラフィックをキャプチャし、250以上のYAMLベースセキュリティルール（OWASP API Top 10）を実行し、機密データ（PII、認証情報、トークン）を検出し、自然言語で脅威を照会できます。

## 2つの製品

| | Argusプラットフォーム | Argus CLI |
|--|:---:|:---:|
| **種類** | サーバーベースのプラットフォーム | スタンドアロンバイナリ |
| **用途** | 継続的なAPIモニタリング | オンデマンドのセキュリティテスト |
| **インフラ** | Kafka, Elasticsearch, PostgreSQL | 不要（SQLite組み込み） |
| **インターフェース** | Webダッシュボード（React） | ターミナル（CLI + TUI） |
| **対象ユーザー** | DevSecOpsチーム、SOC | ペンテスター、監査人、エアギャップネットワーク |

---

<h2 id="argus-platform">Argusプラットフォーム（サーバー）</h2>

### 3ステップのセキュリティライフサイクル

**検出** -- APIインベントリを自動収集し、シャドーAPIを特定
**分析** -- 250以上のセキュリティルール + 機密データ検出（DLP）
**対応** -- AI駆動の自然言語クエリ + リアルタイムアラート

### 主な機能

- **リアルタイムトラフィック分析** -- コレクターがゲートウェイ/サイドカー/エージェント経由でHTTPトラフィックをキャプチャ
- **250以上のYAMLセキュリティルール** -- SQL Injection、XSS、SSRF、BOLA、Command Injectionなど
- **デュアルスキャンモード** -- パッシブ（影響ゼロ） + アクティブ（ペネトレーションテスト）
- **機密データ検出** -- クレジットカード（Luhnアルゴリズム）、SSN、JWT、メールアドレス、電話番号
- **AI自然言語クエリ** -- LLM（Azure OpenAI / Ollama）経由で「先週のSQL Injectionを表示」
- **自動スキャン** -- 保存されたテスト設定によるCronベースのスケジュールスキャン
- **エンタープライズ統合** -- Slack、メール、Webhookアラート + RBAC + ドメインフィルタリング

### アーキテクチャ

```
トラフィックソース        プラットフォーム                      ストレージ
                    ┌─────────────────────────────────┐
 ゲートウェイ ────┐    │                                   │
              ├───>│  Collector (:8081)                 │
 サイドカー ─────┤    │       │                           │
              │    │       v                           │
 ログエージェント ┘    │     Kafka                         │──> Elasticsearch
                   │       │                           │
                   │       v                           │
                   │  Analyzer                         │──> PostgreSQL
                   │   ├─ Parser（HTTPトラフィック）       │
                   │   ├─ Detector（250以上のYAMLルール）   │
                   │   ├─ Sensitive（PII検出）            │──> Redis
                   │   └─ Notifier（Slack/メール/WH）     │
                   │       │                           │
                   │       v                           │
                   │  Dashboard API (:8080)            │
                   │       │                           │
                   │       v                           │
                   │  React UI (:3000)                 │
                   └─────────────────────────────────┘
```

---

<h2 id="argus-cli">Argus CLI -- スタンドアロンセキュリティテスト</h2>

250以上のルールがすべて組み込まれた単一のGoバイナリ。サーバー不要、データベース不要、インターネット不要。Burp Suiteのようにターミナルで動作します。

```bash
# パッシブスキャン（本番環境でも安全）
argus-cli scan passive https://api.example.com

# アクティブスキャン（攻撃ペイロードを送信）
argus-cli scan active https://api.example.com

# フルスキャン（クロール + パッシブ + アクティブ）
argus-cli scan full https://api.example.com

# プロキシモード（ブラウザトラフィックをインターセプト）
argus-cli proxy start --port 8082 --passive-scan

# KISA Webセキュリティ脆弱性診断（韓国）
argus-cli scan kisa https://api.example.com --level 2 --output report.xlsx

# HTMLレポート生成
argus-cli report generate --format html --output report.html
```

### CLIツール

| ツール | 説明 | Burp Suite相当機能 |
|------|-------------|----------------------|
| **Proxy** | HTTP/HTTPSトラフィックのインターセプトと検査 | Proxy |
| **Repeater** | リクエストの編集と再送信 | Repeater |
| **Intruder** | ペイロードを使用した自動ファジング | Intruder |
| **Scanner** | 250以上のYAMLルール、パッシブ + アクティブ | Scanner |
| **Crawler** | エンドポイントとフォームの検出 | Spider |
| **Decoder** | Base64、URL、16進数のエンコード/デコード | Decoder |
| **Comparer** | 2つのレスポンスの差分比較 | Comparer |
| **Sequencer** | トークンのランダム性分析 | Sequencer |

### KISA Webセキュリティ脆弱性診断

[KISA](https://www.kisa.or.kr/)（韓国インターネット振興院）のWeb脆弱性診断に対応 -- 28項目の診断チェック（WEB-001 ~ WEB-028）を自動スキャンし、Excelレポートを生成します。

| レベル | 項目数 | 説明 |
|-------|------:|-------------|
| Level 0（パッシブ） | 7 | ディレクトリインデキシング、情報漏洩、セッション有効期限、Cookie セキュリティ |
| Level 1（セミアクティブ） | 9 | ヘッダーチェック、認証テスト |
| Level 2（アクティブ） | 12 | SQLi、XSS、SSRF、Command Injection |

---

<h2 id="downloads">ダウンロード</h2>

最新のバイナリは[リリース](https://github.com/mhb8436/argus-ai-releases/releases)ページからダウンロードできます。

| プラットフォーム | アーキテクチャ | バイナリ |
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

<h2 id="comparison">比較</h2>

| 機能 | Argus | Akto | Burp Suite | ZAP | Nuclei |
|---------|:-----:|:----:|:----------:|:---:|:------:|
| API特化型セキュリティ | はい | はい | 部分的 | 部分的 | 部分的 |
| YAMLベースのルール | 250以上 | 208（OSS） | プロプライエタリ | なし | 8000以上 |
| カスタムルール | はい | はい | 拡張機能 | スクリプト | はい |
| パッシブ + アクティブスキャン | 両方 | 両方 | 両方 | 両方 | アクティブのみ |
| 機密データ検出（DLP） | はい | はい | なし | なし | なし |
| AI自然言語クエリ | はい | なし | なし | なし | なし |
| スタンドアロンCLI（エアギャップ対応） | はい | なし | はい | はい | はい |
| リアルタイムトラフィック分析 | はい | はい | はい | はい | なし |
| KISAコンプライアンス（韓国） | 28項目 | なし | なし | なし | なし |
| Burp Suite相当ツール（proxy、intruder、repeater） | はい（CLI） | なし | はい（GUI） | 部分的 | なし |

---

## セキュリティルール（250以上）

すべてのルールはYAMLベースで、22以上のカテゴリをカバーしています:

| カテゴリ | ルール数 | 例 |
|----------|------:|---------|
| Injection Attacks (SQLi, NoSQLi, XXE) | 30以上 | エラーベース、ユニオンベース、ブラインドSQLi |
| Cross-Site Scripting (XSS) | 9 | 反射型、DOMベース、エンコーディングバイパス |
| Command Injection | 18 | Shell、Struts、Spring、Tomcat RCE |
| Server-Side Request Forgery | 8 | AWS/Azure/GCPメタデータリダイレクト |
| Broken Authentication | 25以上 | JWT、2FAバイパス、クレデンシャルスタッフィング |
| Security Misconfiguration | 25以上 | デフォルト認証情報、公開パネル、デバッグエンドポイント |
| Local File Inclusion | 11 | パストラバーサル、ディレクトリトラバーサル |
| 機密データ検出 | 5種類 | クレジットカード、SSN、JWT、メールアドレス、電話番号 |
| ...その他 | | CORS、CRLF、SSTI、レートリミッティング、GraphQL |

---

## 技術スタック

| レイヤー | 技術 |
|-------|-----------|
| バックエンド | Go 1.21+, Fiber v2 |
| フロントエンド | React 18, TypeScript, Vite |
| AI/LLM | Azure OpenAI, Ollama（ローカル） |
| データベース | PostgreSQL, Elasticsearch |
| メッセージキュー | Apache Kafka |
| キャッシュ | Redis |
| CLIストレージ | SQLite（組み込み） |

---

## お問い合わせ

お問い合わせは[Issue](https://github.com/mhb8436/argus-ai-releases/issues)を作成してください。

---

## ライセンス

AGPL-3.0 with Commons Clause

Copyright (c) 2026 CRAFTICSYSTEMS Co., Ltd.
