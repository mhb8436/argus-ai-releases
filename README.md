# Argus AI

[![Version](https://img.shields.io/badge/version-v1.0.0-blue.svg)](https://github.com/mhb8436/argus-ai-releases/releases/tag/v1.0.0)
[![License](https://img.shields.io/badge/License-AGPL_v3_+_Commons_Clause-green.svg)](#license)

**자연어로 API 보안을 분석하는 AI 플랫폼**

> "지난 주에 발견된 SQL Injection 취약점 보여줘"
> "HIGH 심각도 보안 이슈 검색해줘"
> "신용카드 정보가 노출된 API 찾아줘"

복잡한 쿼리 문법 없이, 자연어로 보안 데이터를 검색하고 분석하세요.

---

## Demo

![Argus AI Demo](argus-combined-demo.gif)

---

## AI 자연어 쿼리

LLM이 자연어를 Elasticsearch Query DSL로 변환하여 실행합니다.

| 기능 | 설명 |
|------|------|
| **보안 취약점 검색** | 카테고리, 심각도, 기간별 필터링 |
| **민감정보 탐지 조회** | 신용카드, 이메일, JWT 등 데이터 유형별 검색 |
| **API 트래픽 분석** | 응답코드, 메서드, 경로별 트래픽 조회 |
| **LLM 지원** | Azure OpenAI, Ollama (로컬 LLM) 지원 |

---

## Features

| 기능 | 설명 |
|------|------|
| **API 인벤토리** | API 엔드포인트 자동 수집 및 카탈로그 관리 |
| **위협 탐지** | 206개 이상의 YAML 기반 보안 규칙 (OWASP API Top 10) |
| **민감정보 탐지** | PII, 자격증명, 토큰 등 실시간 탐지 |
| **도메인 관리** | 모니터링 대상 도메인 설정 및 연동 |
| **알림** | Slack, Email, Webhook 채널 지원 |

---

## Architecture

```
Traffic Sources → Collector (8081) → Kafka → Analyzer → Elasticsearch/PostgreSQL → Dashboard API (8080) → React UI (3000)
```

### Core Services

| 서비스 | 설명 | 포트 |
|--------|------|------|
| **Collector** | HTTP 트래픽 수집, Kafka 발행 | 8081 |
| **Analyzer** | 트래픽 파싱, 보안 규칙 매칭, 민감정보 탐지 | - |
| **Dashboard API** | REST API, JWT 인증, AI 쿼리 처리 | 8080 |
| **UI** | React 기반 대시보드 | 3000 |

---

## Tech Stack

| 구분 | 기술 |
|------|------|
| AI/LLM | Azure OpenAI, Ollama |
| Backend | Go 1.21+, Fiber v2 |
| Frontend | React 18, TypeScript, Vite |
| Database | PostgreSQL, Elasticsearch |
| Message Queue | Kafka |
| Cache | Redis |

---

## Releases

### v1.0.0 (2025-01-29) - Initial Release

첫 번째 정식 릴리즈

**주요 기능:**
- AI 자연어 쿼리 (Azure OpenAI, Ollama 지원)
- 실시간 API 트래픽 수집 및 분석
- 206개 OWASP 기반 보안 규칙
- 민감정보 자동 탐지 (SSN, 신용카드, JWT 등)
- API 인벤토리 자동 생성
- Slack, Email, Webhook 알림

---

## Quick Start

```bash
# 전체 환경 시작 (인프라 + 앱)
./dev.sh all

# 브라우저에서 접속
open http://localhost:3000

# 로그인
# ID: admin@example.com
# PW: admin123
```

---

## Contact

문의사항이 있으시면 이슈를 등록해 주세요.

---

## License

AGPL-3.0 with Commons Clause

이 소프트웨어는 사용, 수정, 배포가 가능하지만 판매하거나 유료 서비스로 제공할 수 없습니다.

Copyright (c) 2026 Argus Contributors
