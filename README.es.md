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
  <strong>Plataforma de seguridad para APIs con mas de 250 reglas de deteccion basadas en YAML y analisis de amenazas impulsado por IA</strong>
</p>

<p align="center">
  <a href="https://github.com/mhb8436/argus-ai-releases/releases"><img src="https://img.shields.io/github/v/release/mhb8436/argus-ai-releases?label=Latest%20Release" alt="Release"></a>
  <a href="#licencia"><img src="https://img.shields.io/badge/License-AGPL_v3-blue.svg" alt="License"></a>
  <img src="https://img.shields.io/badge/Go-1.21+-00ADD8?logo=go" alt="Go Version">
  <img src="https://img.shields.io/badge/Rules-250+-orange" alt="Rules">
  <img src="https://img.shields.io/badge/OWASP-API_Top_10-red" alt="OWASP">
</p>

<p align="center">
  <a href="#argus-platform">Plataforma</a> |
  <a href="#argus-cli">CLI</a> |
  <a href="#descargas">Descargas</a> |
  <a href="#comparacion">Comparacion</a>
</p>

---

Argus es una plataforma de seguridad para APIs que descubre, analiza y protege sus APIs. Captura trafico HTTP, ejecuta mas de 250 reglas de seguridad basadas en YAML (OWASP API Top 10), detecta datos sensibles (PII, credenciales, tokens) y permite consultar amenazas utilizando lenguaje natural.

## Dos Productos

| | Argus Platform | Argus CLI |
|--|:---:|:---:|
| **Tipo** | Plataforma basada en servidor | Binario independiente |
| **Caso de uso** | Monitoreo continuo de APIs | Pruebas de seguridad bajo demanda |
| **Infraestructura** | Kafka, Elasticsearch, PostgreSQL | Ninguna (SQLite integrado) |
| **Interfaz** | Panel web (React) | Terminal (CLI + TUI) |
| **Ideal para** | Equipos DevSecOps, SOC | Pentesters, auditores, redes aisladas |

---

<h2 id="argus-platform">Argus Platform (Servidor)</h2>

### Ciclo de Seguridad en 3 Pasos

**Descubrir** -- Recopilacion automatica del inventario de APIs e identificacion de APIs ocultas
**Analizar** -- Mas de 250 reglas de seguridad + deteccion de datos sensibles (DLP)
**Actuar** -- Consultas en lenguaje natural impulsadas por IA + alertas en tiempo real

### Funcionalidades Principales

- **Analisis de trafico en tiempo real** -- El Collector captura trafico HTTP a traves de gateway/sidecar/agente
- **Mas de 250 reglas de seguridad YAML** -- SQL Injection, XSS, SSRF, BOLA, Command Injection y mas
- **Modos de escaneo dual** -- Pasivo (sin impacto) + Activo (pruebas de penetracion)
- **Deteccion de datos sensibles** -- Tarjetas de credito (Luhn), SSN, JWT, correo electronico, numeros de telefono
- **Consultas en lenguaje natural con IA** -- "Muestrame las SQL Injection de la semana pasada" a traves de LLM (Azure OpenAI / Ollama)
- **Escaneo automatizado** -- Escaneos programados basados en cron con configuraciones de prueba guardadas
- **Integraciones empresariales** -- Alertas por Slack, correo electronico, Webhooks + RBAC + filtrado por dominio

### Arquitectura

```
Fuentes de Trafico      Plataforma                        Almacenamiento
                    ┌─────────────────────────────────┐
 Gateway ─────┐    │                                   │
              ├───>│  Collector (:8081)                 │
 Sidecar ─────┤    │       │                           │
              │    │       v                           │
 Log Agent ───┘    │     Kafka                         │──> Elasticsearch
                   │       │                           │
                   │       v                           │
                   │  Analyzer                         │──> PostgreSQL
                   │   ├─ Parser (trafico HTTP)        │
                   │   ├─ Detector (250+ reglas YAML)   │
                   │   ├─ Sensitive (deteccion PII)    │──> Redis
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

<h2 id="argus-cli">Argus CLI -- Pruebas de Seguridad Independientes</h2>

Un unico binario de Go con todas las mas de 250 reglas integradas. Sin servidor, sin base de datos, sin internet. Funciona como Burp Suite pero en su terminal.

```bash
# Escaneo pasivo (seguro para produccion)
argus-cli scan passive https://api.example.com

# Escaneo activo (envia payloads de ataque)
argus-cli scan active https://api.example.com

# Escaneo completo (crawl + pasivo + activo)
argus-cli scan full https://api.example.com

# Modo proxy (interceptar trafico del navegador)
argus-cli proxy start --port 8082 --passive-scan

# Evaluacion de vulnerabilidades web KISA (Corea)
argus-cli scan kisa https://api.example.com --level 2 --output report.xlsx

# Generar informe HTML
argus-cli report generate --format html --output report.html
```

### Herramientas CLI

| Herramienta | Descripcion | Equivalente en Burp Suite |
|------|-------------|----------------------|
| **Proxy** | Interceptar e inspeccionar trafico HTTP/HTTPS | Proxy |
| **Repeater** | Editar y reenviar solicitudes | Repeater |
| **Intruder** | Fuzzing automatizado con payloads | Intruder |
| **Scanner** | Mas de 250 reglas YAML, pasivo + activo | Scanner |
| **Crawler** | Descubrir endpoints y formularios | Spider |
| **Decoder** | Codificacion/decodificacion Base64, URL, hex | Decoder |
| **Comparer** | Comparar dos respuestas | Comparer |
| **Sequencer** | Analisis de aleatoriedad de tokens | Sequencer |

### Evaluacion de Vulnerabilidades Web KISA

Soporte integrado para la evaluacion de vulnerabilidades web de [KISA](https://www.kisa.or.kr/) (Agencia de Internet y Seguridad de Corea) -- 28 elementos de verificacion (WEB-001 ~ WEB-028) con escaneo automatizado y generacion de informes en Excel.

| Nivel | Elementos | Descripcion |
|-------|------:|-------------|
| Nivel 0 (Pasivo) | 7 | Indexacion de directorios, divulgacion de informacion, expiracion de sesion, seguridad de cookies |
| Nivel 1 (Semi-activo) | 9 | Verificacion de encabezados, pruebas de autenticacion |
| Nivel 2 (Activo) | 12 | SQLi, XSS, SSRF, Command Injection |

---

<h2 id="descargas">Descargas</h2>

Descargue los binarios mas recientes desde la pagina de [Releases](https://github.com/mhb8436/argus-ai-releases/releases).

| Plataforma | Arquitectura | Binario |
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

<h2 id="comparacion">Comparacion</h2>

| Funcionalidad | Argus | Akto | Burp Suite | ZAP | Nuclei |
|---------|:-----:|:----:|:----------:|:---:|:------:|
| Seguridad enfocada en APIs | Si | Si | Parcial | Parcial | Parcial |
| Reglas basadas en YAML | 250+ | 208 (OSS) | Propietario | No | 8000+ |
| Reglas personalizadas | Si | Si | Extensiones | Scripts | Si |
| Escaneo pasivo + activo | Ambos | Ambos | Ambos | Ambos | Solo activo |
| Deteccion de datos sensibles (DLP) | Si | Si | No | No | No |
| Consultas en lenguaje natural con IA | Si | No | No | No | No |
| CLI independiente (redes aisladas) | Si | No | Si | Si | Si |
| Analisis de trafico en tiempo real | Si | Si | Si | Si | No |
| Cumplimiento KISA (Corea) | 28 elementos | No | No | No | No |
| Herramientas tipo Burp Suite (proxy, intruder, repeater) | Si (CLI) | No | Si (GUI) | Parcial | No |

---

## Reglas de Seguridad (250+)

Todas las reglas estan basadas en YAML y cubren mas de 22 categorias:

| Categoria | Reglas | Ejemplos |
|----------|------:|---------|
| Ataques de Inyeccion (SQLi, NoSQLi, XXE) | 30+ | SQLi basado en errores, basado en union, SQLi ciego |
| Cross-Site Scripting (XSS) | 9 | Reflejado, basado en DOM, evasion de codificacion |
| Command Injection | 18 | Shell, Struts, Spring, Tomcat RCE |
| Server-Side Request Forgery | 8 | Redireccion de metadatos AWS/Azure/GCP |
| Autenticacion Rota | 25+ | JWT, evasion de 2FA, relleno de credenciales |
| Configuracion de Seguridad Incorrecta | 25+ | Credenciales por defecto, paneles expuestos, endpoints de depuracion |
| Inclusion de Archivos Locales | 11 | Recorrido de rutas, recorrido de directorios |
| Deteccion de Datos Sensibles | 5 tipos | Tarjeta de credito, SSN, JWT, correo electronico, telefono |
| ...y mas | | CORS, CRLF, SSTI, limitacion de tasa, GraphQL |

---

## Stack Tecnologico

| Capa | Tecnologia |
|-------|-----------|
| Backend | Go 1.21+, Fiber v2 |
| Frontend | React 18, TypeScript, Vite |
| IA/LLM | Azure OpenAI, Ollama (local) |
| Base de Datos | PostgreSQL, Elasticsearch |
| Cola de Mensajes | Apache Kafka |
| Cache | Redis |
| Almacenamiento CLI | SQLite (integrado) |

---

## Contacto

Para consultas, por favor abra un [issue](https://github.com/mhb8436/argus-ai-releases/issues).

---

## Licencia

AGPL-3.0 con Commons Clause

Copyright (c) 2026 CRAFTICSYSTEMS Co., Ltd.
