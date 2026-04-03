# 🔒 Herozion

**Outil d'analyse de sécurité pour développeurs** — Scannez votre code source pour détecter les vulnérabilités de sécurité et les problèmes de performance avant le déploiement.

Aucune installation de dépendances requise. Téléchargez un seul fichier binaire et lancez l'analyse.

![Version](https://img.shields.io/badge/Version-1.0.16-blue)
![Platforms](https://img.shields.io/badge/Platforms-Windows%20|%20Linux%20|%20macOS-brightgreen)
![License](https://img.shields.io/badge/License-Proprietary-red)

---

##  Table des matières

- [Fonctionnalités](#-fonctionnalités)
- [Installation](#-installation)
- [Utilisation](#-utilisation)
- [Catégories d'analyse](#-catégories-danalyse)
- [Configuration](#-configuration)
- [Intégration CI/CD](#-intégration-cicd)
- [FAQ](#-faq)

---

##  Fonctionnalités

- **14 catégories d'analyse** — 13 catégories de sécurité (OWASP API Top 10+) et 1 catégorie de performance
- **Score de sécurité** de 0 à 100 avec notation de A à F
- **Zéro dépendance** — binaire autonome, aucun runtime Python/Node requis
- **Multi-plateforme** — Windows, Linux, macOS
- **Multi-langue** — Interface en anglais, français ou portugais (`--lang en|fr|pt`)
- **Interface CLI riche** avec sortie colorée et tableaux formatés
- **Sortie JSON** pour intégration CI/CD et automatisation
- **Historique des scans** persisté localement
- **Dashboard SaaS** — envoi optionnel des résultats vers une API centralisée (à venir)

---

##  Installation

### Windows

1. Téléchargez `herozion-windows-amd64.exe` depuis la [page Releases](https://github.com/herozion/herozion/releases/latest)
2. Renommez le fichier en `herozion.exe`
3. Placez-le dans un dossier de votre PATH (ex: `C:\Tools\`)
4. Vérifiez :

```powershell
herozion --version
```

### Linux

```bash
# Télécharger
curl -fSL -o herozion https://github.com/herozion/herozion/releases/latest/download/herozion-linux-amd64

# Rendre exécutable
chmod +x herozion

# Déplacer dans le PATH
sudo mv herozion /usr/local/bin/

# Vérifier
herozion --version
```

### macOS (recommandé — via Homebrew)

Homebrew gère automatiquement les mises à jour et détecte si votre Mac est Intel ou Apple Silicon.

```bash
brew tap Herozion/herozion
brew install herozion
herozion --version
```

Pour mettre à jour vers une nouvelle version :

```bash
brew upgrade herozion
```

### macOS (installation manuelle)

Si vous ne souhaitez pas utiliser Homebrew :

```bash
# Apple Silicon (M1/M2/M3/M4)
curl -fSL -o herozion https://github.com/Herozion/scanner-releases/releases/latest/download/herozion-macos-arm64

# Intel
curl -fSL -o herozion https://github.com/Herozion/scanner-releases/releases/latest/download/herozion-macos-amd64

# Rendre exécutable et déplacer dans le PATH
chmod +x herozion
sudo mv herozion /usr/local/bin/

# Vérifier
herozion --version
```

> **Note macOS** : Si macOS bloque le binaire avec un message "développeur non identifié", exécutez une fois :
> ```bash
> xattr -d com.apple.quarantine $(which herozion)
> ```

### Vérification d'intégrité (recommandé)

Chaque release inclut un fichier `CHECKSUMS.sha256`. Après téléchargement :

```bash
# Linux / macOS
sha256sum -c CHECKSUMS.sha256

# Windows (PowerShell)
(Get-FileHash herozion-windows-amd64.exe -Algorithm SHA256).Hash
# Comparez avec la valeur dans CHECKSUMS.sha256
```

---

##  Utilisation

### Scanner un projet

```bash
# Scanner le répertoire courant
herozion scan .

# Choisir la langue de sortie (en, fr, pt)
herozion --lang fr scan .

# Scanner un répertoire spécifique
herozion scan /chemin/vers/projet

# Exclure des dossiers supplémentaires
herozion scan . -e vendor -e generated

# Sortie JSON (pour CI/CD)
herozion scan . -o json

# Sortie JSON dans un fichier
herozion scan . -o json > rapport.json

# Scanner et envoyer au dashboard
herozion scan . --push
```

### Exemple de sortie

```
╔══════════════════════════════════════╗
║     🔒  Herozion — Security Scan    ║
╚══════════════════════════════════════╝

📁 Scanning: /chemin/vers/projet
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

### Consulter l'historique

```bash
herozion history
```

### Aide — lister toutes les commandes

```bash
herozion help
```

### Informations sur l'outil

```bash
herozion info
```

### Codes de sortie

| Code | Signification |
|------|---------------|
| `0`  | Score ≥ 60 — Le projet est acceptable |
| `1`  | Score < 60 — Des vulnérabilités critiques doivent être corrigées |

---

##  Catégories d'analyse

Herozion détecte **14 catégories** de problèmes :

### Sécurité (13 catégories)

| # | Catégorie | Description |
|---|-----------|-------------|
| 1 | **BOLA** | Broken Object Level Authorization — accès direct aux objets sans vérification |
| 2 | **Broken Authentication** | Mots de passe en dur, tokens statiques, secrets exposés |
| 3 | **BFLA** | Broken Function Level Authorization — endpoints admin non protégés |
| 4 | **Mass Assignment** | Données utilisateur passées directement aux modèles |
| 5 | **Injection** | SQL injection, command injection, XSS, `eval()` |
| 6 | **Rate Limiting** | Endpoints sans limitation de débit |
| 7 | **Security Misconfiguration** | `DEBUG=True`, `SECRET_KEY` exposée, CORS permissif |
| 8 | **Excessive Data Exposure** | Sérialisation complète d'objets, champs sensibles exposés |
| 9 | **MITM** | Vérification SSL désactivée, connexions HTTP non sécurisées |
| 10 | **Replay Attacks** | Tokens sans expiration, absence de nonce |
| 11 | **Webhook Abuse** | Webhooks sans vérification de signature |
| 12 | **DDoS/Flood** | Timeouts absents, lecture complète en mémoire |
| 13 | **Insecure File Upload** | Absence de validation MIME type, chemins non sécurisés |

### Performance (1 catégorie)

| # | Catégorie | Description |
|---|-----------|-------------|
| 14 | **Performance** | Requêtes N+1, `list()` sur queryset complet, `import *`, anti-patterns |

---

## ⚙️ Configuration

Herozion fonctionne sans configuration. Pour personnaliser le comportement, créez des variables d'environnement ou un fichier `.env` dans le répertoire de travail :

```env
# Dashboard API (optionnel — pour --push)
HEROZION_API_URL=https://api.herozion.example.com
HEROZION_API_KEY=votre-cle-api

# Répertoire de stockage des résultats (défaut: ~/.herozion)
HEROZION_DATA_DIR=~/.herozion

# Score minimum acceptable (défaut: 60)
HEROZION_MIN_SCORE=60

# Langue de l'interface (en, fr, pt — défaut: en)
HEROZION_LANG=fr
```

---

## 🔄 Intégration CI/CD

### GitHub Actions

```yaml
- name: Security Scan
  run: |
    curl -fSL -o herozion https://github.com/herozion/herozion/releases/latest/download/herozion-linux-amd64
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
    - curl -fSL -o herozion https://github.com/herozion/herozion/releases/latest/download/herozion-linux-amd64
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

**Q: Herozion nécessite-t-il Python ou d'autres dépendances ?**
Non. Le binaire est entièrement autonome. Téléchargez et exécutez directement.

**Q: Quels langages sont analysés ?**
Python, JavaScript/TypeScript, Java, Go, Ruby, PHP, C#, Rust, ainsi que les fichiers de configuration (YAML, JSON, TOML, .env).

**Q: Mes données de code sont-elles envoyées quelque part ?**
Non. L'analyse est 100% locale. L'option `--push` est optionnelle et envoie uniquement le rapport (pas votre code source) au dashboard.

**Q: Comment mettre à jour ?**

**Via Homebrew (macOS/Linux) :**
```bash
brew update && brew upgrade herozion
```
> `brew update` est obligatoire — il récupère la dernière formule du tap. Sans lui, Homebrew ne détecte pas la nouvelle version.

**Manuellement :** Téléchargez la dernière version depuis la [page Releases](https://github.com/Herozion/scanner-releases/releases/latest) et remplacez le binaire existant.

**Q: `brew upgrade` me dit "already installed" mais la version affichée est ancienne, pourquoi ?**
Le pipeline de build CI prend quelques minutes après chaque push. Si `brew upgrade` retourne "already installed" mais que `herozion --version` affiche une vieille version, cela signifie que la nouvelle release n'est pas encore publiée. Attendez quelques minutes, puis relancez `brew update && brew upgrade herozion`.

---

## 📄 License

Logiciel propriétaire — voir [LICENSE](LICENSE) pour les conditions d'utilisation.

Copyright (c) 2026 Herozion Team. Tous droits réservés.

---

## 🗺️ Roadmap

- [ ] Dashboard SaaS avec visualisations
- [ ] Mode watch (surveillance continue)
- [ ] Plugin VS Code
- [ ] Rapports HTML exportables
- [ ] Configuration `.herozionignore`
