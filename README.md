# CI/CD Pipeline — Counter API Service (JavaScript)

Eine kleine Node.js/Express-API (**Counter Service**), begleitet von einer vollständigen CI/CD-Pipeline: automatisiertes Linting und Testing via GitHub Actions sowie einer Sammlung von **Tekton**-Pipeline-Labs, die schrittweise eine Cloud-native CI/CD-Pipeline auf Kubernetes aufbauen.

Das Projekt entstand im Rahmen des Kurses **IBM CD0215EN — Introduction to CI/CD** (IBM Skills Network).

## Was macht der Service?

Ein einfacher In-Memory-Zähler-Service mit einer REST-API:

| Methode | Endpoint | Beschreibung |
|---|---|---|
| `GET` | `/health` | Health-Check, liefert `{ status: "OK" }` |
| `GET` | `/` | Index-Route mit Service-Infos |
| `GET` | `/counters` | Listet alle vorhandenen Zähler |
| `POST` | `/counters/:name` | Legt einen neuen Zähler mit Namen `name` an (Startwert 0) |
| `GET` | `/counters/:name` | Liest den aktuellen Wert eines Zählers |
| `PUT` | `/counters/:name` | Erhöht einen Zähler um 1 |
| `DELETE` | `/counters/:name` | Löscht einen Zähler |

Middleware: [Helmet](https://helmetjs.github.io/) (Security-Header), [CORS](https://www.npmjs.com/package/cors), [Morgan](https://www.npmjs.com/package/morgan) (Request-Logging) sowie eine zentrale Fehlerbehandlung (`common/errorHandlers.js`) und ein eigenes Logging-Modul (`common/logger.js`).

## Tech-Stack

- **Node.js** ≥ 16, **Express** 4
- **Jest** + **Supertest** für Unit-/Integrationstests (Coverage-Schwelle: 80 % für Branches, Functions, Lines, Statements)
- **ESLint** (`eslint-config-standard`) für Codequalität
- **Docker** — mehrstufig optimiertes, mit Non-Root-User gehärtetes Image
- **GitHub Actions** für Continuous Integration (Lint + Test bei jedem Push/PR auf `main`)
- **Tekton** (Labs) für eine Kubernetes-native CI/CD-Pipeline

## Projektstruktur

```
service/
├── app.js                    # Express-App, Middleware-Setup, Server-Start
├── routes.js                 # REST-Endpunkte des Counter-Service
└── common/
    ├── errorHandlers.js       # zentrale Fehlerbehandlung (404, 500, ...)
    ├── logger.js               # Logging-Utility
    └── status.js                # lesbare HTTP-Statuscode-Konstanten
tests/                        # Jest-Testsuite (Unit- & Integrationstests)
labs/                         # Tekton-CI/CD-Pipeline-Übungen (siehe unten)
.github/workflows/workflow.yml # GitHub-Actions-CI-Workflow
Dockerfile
jest.config.js
.eslintrc.js
```

## Erste Schritte

### Voraussetzungen

- Node.js ≥ 16
- npm

### Installation

```bash
npm install
```

### Service starten

```bash
npm start        # Produktion
npm run dev       # Entwicklung mit nodemon (Auto-Reload)
```

Der Service läuft standardmäßig unter `http://localhost:8000`.

### Tests & Linting

```bash
npm test               # Jest-Testsuite ausführen
npm run test:watch     # Tests im Watch-Modus
npm run test:coverage  # Tests mit Coverage-Report
npm run lint            # ESLint-Check
npm run lint:fix        # ESLint-Autofix
```

### Mit Docker ausführen

```bash
docker build -t counter-service .
docker run -p 8000:8000 counter-service
```

## Continuous Integration

Der Workflow [`.github/workflows/workflow.yml`](.github/workflows/workflow.yml) läuft bei jedem Push und Pull Request auf `main` und führt automatisch aus:

1. Checkout des Repositories
2. `npm ci` — reproduzierbare Installation der Dependencies
3. `npm run lint` — Codequalitätsprüfung mit ESLint
4. `npm test -- --coverage` — Testsuite mit Coverage-Report

## CI/CD-Labs (Tekton auf Kubernetes)

Der Ordner [`labs/`](labs/) enthält eine Reihe aufeinander aufbauender Übungen, die zeigen, wie derselbe Service mit **Tekton** in einer Kubernetes-Umgebung gebaut, getestet und deployt werden kann:

1. **[01_base_pipeline](labs/01_base_pipeline)** — Grundgerüst einer Tekton-Pipeline
2. **02_add_git_trigger** — Auslösen der Pipeline über Git-Events (EventListener, TriggerBinding, TriggerTemplate)
3. **03_use_tekton_catalog** — Wiederverwendung vorgefertigter Tasks aus dem Tekton-Katalog
4. **04_unit_test_automation** — Automatisierte Ausführung der Unit-Tests innerhalb der Pipeline
5. **05_build_an_image** — Bauen eines Container-Images als Pipeline-Schritt
6. **06_deploy_to_kubernetes** — Deployment des Images auf einen Kubernetes-Cluster

## Lizenz

Dieses Projekt steht unter der [Apache-2.0-Lizenz](LICENSE).
