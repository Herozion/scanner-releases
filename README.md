# 🔒 Herozion

**Security audit and performance analysis CLI for developers** — Scan your source code to detect security vulnerabilities and performance issues before deployment.

No dependencies required. Download a single binary and start scanning.

![Version](https://img.shields.io/badge/Version-1.0.39-blue)
![Platforms](https://img.shields.io/badge/Platforms-Windows%20|%20Linux%20|%20macOS-brightgreen)
![License](https://img.shields.io/badge/License-Proprietary-red)

---

## Table of Contents

- [Features](#-features)
- [Installation](#-installation)
- [Usage](#-usage)
- [Analysis Categories](#-analysis-categories)
- [Configuration](#️-configuration)
- [CI/CD Integration](#-cicd-integration)
- [FAQ](#-faq)

---

## Features

- **14 analysis categories** — 13 security categories (OWASP API Top 10+) and 1 performance category
- **Security score** from 0 to 100 with A–F grading
- **Zero dependencies** — standalone binary, no Python/Node runtime required
- **Cross-platform** — Windows, Linux, macOS
- **Multi-language** — Interface in English, French or Portuguese (`--lang en|fr|pt`)
- **Rich CLI output** with colored output and formatted tables
- **JSON output** for CI/CD integration and automation
- **Persistent scan history** stored locally
- **SaaS Dashboard** — optional result upload to a centralized API (coming soon)

---

## Installation

### Windows

1. Download `herozion-windows-amd64.exe` from the [Releases page](https://github.com/Herozion/scanner-releases/releases/latest)
2. Rename the file to `herozion.exe`
3. Place it in a folder on your PATH (e.g. `C:\Tools\`)
4. Verify:

```powershell
herozion --version
```

### Linux

```bash
# Download
curl -fSL -o herozion https://github.com/Herozion/scanner-releases/releases/latest/download/herozion-linux-amd64

# Make executable
chmod +x herozion

# Move to PATH
sudo mv herozion /usr/local/bin/

# Verify
herozion --version
```

### macOS (recommended — via Homebrew)

Homebrew automatically handles updates and detects whether your Mac is Intel or Apple Silicon.

```bash
brew tap Herozion/herozion
brew install herozion
herozion --version
```

To update to a newer version:

```bash
brew update && brew upgrade herozion
```

> **Important:** Always run `brew update` before `brew upgrade`. Without it, Homebrew does not detect that a new version is available.

### macOS (manual installation)

If you prefer not to use Homebrew:

```bash
# Apple Silicon (M1/M2/M3/M4)
curl -fSL -o herozion https://github.com/Herozion/scanner-releases/releases/latest/download/herozion-macos-arm64

# Intel
curl -fSL -o herozion https://github.com/Herozion/scanner-releases/releases/latest/download/herozion-macos-amd64

# Make executable and move to PATH
chmod +x herozion
sudo mv herozion /usr/local/bin/

# Verify
herozion --version
```

> **macOS Gatekeeper note:** If macOS blocks the binary with an "unidentified developer" message, run once:
> ```bash
> xattr -d com.apple.quarantine $(which herozion)
> ```

### Integrity verification (recommended)

Each release includes a `CHECKSUMS.sha256` file. After downloading:

```bash
# Linux / macOS
sha256sum -c CHECKSUMS.sha256

# Windows (PowerShell)
(Get-FileHash herozion-windows-amd64.exe -Algorithm SHA256).Hash
# Compare with the value in CHECKSUMS.sha256
```

---

## Usage

### Scan a project

```bash
# Scan the current directory
herozion scan .

# Choose output language (en, fr, pt)
herozion --lang fr scan .

# Scan a specific directory
herozion scan /path/to/project

# Exclude additional folders
herozion scan . -e vendor -e generated

# JSON output (for CI/CD)
herozion scan . -o json

# JSON output to a file
herozion scan . -o json > report.json

# Scan and push results to the dashboard
herozion scan . --push
```

### Example output

```
╔══════════════════════════════════════╗
║     🔒  Herozion — Security Scan    ║
╚══════════════════════════════════════╝

📁 Scanning: /path/to/project
✅ Scanned 42 files in 0.85s

┌─────────────────────────────────┐
│   Security Score: 72/100 (C)    │
└─────────────────────────────────┘

┌──────────┬──────┬───────────────────────────────────────────┬──────────┬──────┐
│ Severity │ Cat. │ Message                                   │ File     │ Line │
├──────────┼──────┼───────────────────────────────────────────┼──────────┼──────┤
│ CRITICAL │ INJ  │ SQL injection: string formatting in query │ db.py    │  15  │
│ HIGH     │ AUTH │ Hardcoded password detected               │ config.py│   8  │
│ MEDIUM   │ MISC │ Debug mode enabled in production config   │ app.py   │   3  │
└──────────┴──────┴───────────────────────────────────────────┴──────────┴──────┘
```

### View scan history

```bash
herozion history
```

### List all commands

```bash
herozion help
```

### Tool information

```bash
herozion info
```

### Exit codes

| Code | Meaning |
|------|---------|
| `0`  | Score ≥ 60 — Project is acceptable |
| `1`  | Score < 60 — Critical vulnerabilities must be fixed |

---

## Analysis Categories

Herozion detects **14 categories** of issues:

### Security (13 categories)

| # | Category | Description |
|---|----------|-------------|
| 1 | **BOLA** | Broken Object Level Authorization — direct object access without authorization checks |
| 2 | **Broken Authentication** | Hardcoded passwords, static tokens, exposed secrets |
| 3 | **BFLA** | Broken Function Level Authorization — unprotected admin endpoints |
| 4 | **Mass Assignment** | User-supplied data passed directly to models |
| 5 | **Injection** | SQL injection, command injection, XSS, `eval()` |
| 6 | **Rate Limiting** | Endpoints with no rate limiting |
| 7 | **Security Misconfiguration** | `DEBUG=True`, exposed `SECRET_KEY`, permissive CORS |
| 8 | **Excessive Data Exposure** | Full object serialization, sensitive fields exposed in responses |
| 9 | **MITM** | SSL verification disabled, unencrypted HTTP connections |
| 10 | **Replay Attacks** | Tokens without expiration, missing nonce |
| 11 | **Webhook Abuse** | Webhooks without signature verification |
| 12 | **DDoS/Flood** | Missing timeouts, full in-memory reads |
| 13 | **Insecure File Upload** | Missing MIME type validation, unsafe file paths |

### Performance (1 category)

| # | Category | Description |
|---|----------|-------------|
| 14 | **Performance** | N+1 queries, `list()` on full queryset, `import *`, anti-patterns |

---

## ⚙️ Configuration

Herozion works out of the box with no configuration. To customize its behavior, set environment variables or create a `.env` file in your **home directory** (`~/.env`) — not in the project being scanned:

```env
# Dashboard API (optional — for --push)
HEROZION_API_URL=https://api.herozion.io
HEROZION_API_KEY=your-api-key

# Report storage directory (default: .herozion/reports)
HEROZION_REPORT_DIR=~/.herozion/reports

# Interface language (en, fr, pt — default: en)
HEROZION_LANG=en
```

> **Note:** Herozion only reads variables prefixed with `HEROZION_`. Any other variables in a `.env` file (e.g. from a Laravel or Node.js project) are safely ignored.

---

## 🔄 CI/CD Integration

### GitHub Actions

```yaml
- name: Security Scan
  run: |
    curl -fSL -o herozion https://github.com/Herozion/scanner-releases/releases/latest/download/herozion-linux-amd64
    chmod +x herozion
    ./herozion scan . -o json > security-report.json
    ./herozion scan .

- name: Upload Security Report
  if: always()
  uses: actions/upload-artifact@v4
  with:
    name: security-report
    path: security-report.json
```

### GitLab CI

```yaml
security_scan:
  stage: test
  before_script:
    - curl -fSL -o herozion https://github.com/Herozion/scanner-releases/releases/latest/download/herozion-linux-amd64
    - chmod +x herozion
  script:
    - ./herozion scan . -o json > security-report.json
    - ./herozion scan .
  artifacts:
    paths:
      - security-report.json
    when: always
```

---

## ❓ FAQ

**Q: Does Herozion require Python or any other runtime?**
No. The binary is fully self-contained. Download and run directly.

**Q: Which languages are analyzed?**
Python, JavaScript/TypeScript, Java, Go, Ruby, PHP, C#, Rust, as well as configuration files (YAML, JSON, TOML, .env).

**Q: Is my source code sent anywhere?**
No. Analysis is 100% local. The `--push` option is optional and only sends the report (not your source code) to the dashboard.

**Q: How do I update?**

**Via Homebrew (macOS/Linux):**
```bash
brew update && brew upgrade herozion
```
> `brew update` is required — it fetches the latest formula from the tap. Without it, Homebrew won't detect the new version.

**Manually:** Download the latest release from the [Releases page](https://github.com/Herozion/scanner-releases/releases/latest) and replace the existing binary.

**Q: `brew upgrade` says "already installed" but `--version` shows an old version, why?**
The CI build pipeline takes a few minutes after each push. If `brew upgrade` returns "already installed" but `herozion --version` shows an old version, the new release is not yet published. Wait a few minutes, then run `brew update && brew upgrade herozion` again.

---

## 📄 License

Proprietary software — see [LICENSE](LICENSE) for terms of use.

Copyright (c) 2026 Herozion Team. All rights reserved.

---

## 🗺️ Roadmap

- [ ] SaaS Dashboard with visualizations
- [ ] Watch mode (continuous monitoring)
- [ ] VS Code plugin
- [ ] Exportable HTML reports
- [ ] `.herozionignore` configuration file
