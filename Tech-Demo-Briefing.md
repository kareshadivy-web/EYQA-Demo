# EY QA Tool — Tech Demo Briefing
**Audience:** Technology & Costing Discussion | **Date:** 1 July 2026

---

## 1. Current Demo Tool — Tech Stack

| Layer | Technology | Notes |
|---|---|---|
| **Architecture** | Single-file SPA (HTML + JS + CSS) | ~10,000 lines in one `.html` file |
| **Frontend** | Vanilla JavaScript (ES2020+) | No framework; custom component system |
| **Styling** | Inline CSS + CSS Variables | DM Sans font via Google Fonts |
| **Routing** | Custom client-side router (`nav()`) | Hash-free, in-memory |
| **State Management** | Global `S` object (in-memory) | Reset on page refresh |
| **Persistence** | Browser `localStorage` | Session-only; no server storage |
| **Document Handling** | Browser `FileReader` API | PDFs/DOCX read client-side; no server upload |
| **PDF Extraction** | `pdf.js` (Mozilla CDN) | Text extracted in-browser |
| **DOCX Extraction** | `mammoth.js` (CDN) | Word docs parsed in-browser |
| **AI — QA Engine** | Anthropic Claude Opus 4.8 | 29-checkpoint analysis engine |
| **AI — Summarisation** | Anthropic Claude Haiku 4.5 | Observation / recommendation rewriting |
| **AI — Refinement** | Anthropic Claude Haiku 4.5 | User-prompted language refinement |
| **API Auth** | API key stored in `localStorage` | User enters key manually |
| **Export** | Browser `window.print()` to PDF | No server-side PDF generation |
| **Hosting** | GitHub Pages (static) | `main` branch auto-deployed |
| **CI/CD** | None (manual git push) | — |
| **Security** | None | No auth, no access control, no encryption |
| **Database** | None | — |
| **Backend** | None | All processing is client-side |

---

## 2. Full Production Tech Stack (Company Environment)

### 2a. Frontend

| Component | Recommended Technology | Rationale |
|---|---|---|
| Framework | **React 18 + Next.js 14** | SSR support, routing, enterprise-grade |
| UI Library | **Tailwind CSS + ShadCN** | Rapid build, accessible, themeable |
| State Management | **Zustand** or **Redux Toolkit** | Predictable state, easy debug |
| PDF Viewer | **react-pdf** (pdf.js wrapper) | In-browser preview |
| Export / Report | **React-PDF** or server-side PDF | Professional formatted output |
| Hosting | **Azure Static Web Apps** | Integrates with Azure AD, CI/CD |

### 2b. Backend / API

| Component | Recommended Technology | Rationale |
|---|---|---|
| Runtime | **Node.js 20 (TypeScript)** or **Python 3.12 (FastAPI)** | Strong Azure SDK support |
| API Layer | **REST API** (Express / FastAPI) | Stateless, scalable |
| Document Processing | **Azure Document Intelligence** (Form Recognizer) | Structured extraction from PDF/Word/Excel |
| File Handling | **Azure Blob Storage** | Secure, scalable document storage |
| Background Jobs | **Azure Service Bus + Azure Functions** | Async QA processing (long-running AI calls) |
| Hosting | **Azure App Service** (or **AKS** for scale) | Managed, auto-scaling |

### 2c. AI / LLM

| Component | Recommended Technology | Notes |
|---|---|---|
| LLM — QA Engine | **Azure OpenAI GPT-4o** | Replaces Claude Opus for main QA analysis |
| LLM — Summarisation | **Azure OpenAI GPT-4o-mini** | Replaces Claude Haiku for fast rewriting |
| Embedding / Search | **Azure OpenAI text-embedding-3-large** | For smart doc-to-checkpoint routing (cost optimisation) |
| Deployment | **Azure OpenAI Service** (private endpoint) | Data stays within tenant; no data leaves Azure |
| Prompt Management | Config files / Azure App Configuration | Prompts versioned separately from code |

> **Key difference vs demo:** The demo sends ALL documents to every checkpoint (expensive). Production should use **embedding-based retrieval** — embed each doc chunk once, then retrieve only the top-K relevant chunks per checkpoint. This reduces token cost by ~60–70%.

### 2d. Security & Access

| Component | Recommended Technology | Notes |
|---|---|---|
| Authentication | **Azure Active Directory (Entra ID)** | SSO with existing EY credentials |
| Authorisation | **Role-Based Access Control (RBAC)** | Roles: QA Reviewer, Team Lead, Admin, Read-only |
| API Security | **Azure API Management** | Rate limiting, key management, logging |
| Secrets Management | **Azure Key Vault** | API keys, DB credentials never in code |
| Data Encryption | **Azure-managed keys (AES-256)** | At-rest encryption for Blob + DB |
| Network | **Azure Private Endpoints + VNet** | No public internet exposure |
| Audit Logging | **Azure Monitor + Log Analytics** | Who accessed what, when |

### 2e. Data & Storage

| Component | Recommended Technology | Notes |
|---|---|---|
| Primary Database | **Azure SQL Database** (or **Cosmos DB**) | Engagements, findings, users, audit log |
| Document Storage | **Azure Blob Storage** | Uploaded workpapers, RCMs, evidence files |
| Cache | **Azure Cache for Redis** | Session cache, repeated doc embeddings |
| Search Index | **Azure AI Search** | Vector + keyword search across documents |

### 2f. DevOps & Infrastructure

| Component | Technology | Notes |
|---|---|---|
| Source Control | **Azure DevOps / GitHub Enterprise** | Existing EY tooling |
| CI/CD | **Azure DevOps Pipelines** | Build → Test → Deploy per environment |
| Environments | Dev → UAT → Production | Separate resource groups |
| Monitoring | **Azure Application Insights** | Performance, errors, usage telemetry |
| IaC | **Terraform** or **Bicep** | Reproducible infrastructure |

---

## 3. Costing

### 3a. Build Cost — Hours & Effort to Build Full Production Tool

| Module | Effort (Person-Days) | Notes |
|---|---|---|
| Requirements & Architecture | 10 | Detailed design, Azure architecture sign-off |
| Frontend (React SPA) | 30 | All 6 wizard panes, dashboard, reports, exports |
| Backend API | 35 | REST endpoints, business logic, job queue |
| Azure OpenAI Integration + Prompt Engineering | 20 | GPT-4o QA engine, summarisation, refinement, token optimisation |
| Document Processing Pipeline | 20 | Blob upload, Azure Document Intelligence, text chunking |
| Embedding + Smart Retrieval (cost optimiser) | 15 | Embed docs, retrieve per-checkpoint (replaces send-all approach) |
| Auth — Azure AD SSO + RBAC | 15 | Login, roles, access control |
| Database Schema + API | 15 | Engagements, findings, users, audit log |
| Export & PDF Report Generation | 10 | Formatted audit QA report |
| Testing (Unit + Integration + UAT) | 20 | Includes test data preparation |
| DevOps, CI/CD, Infrastructure (IaC) | 15 | Pipelines, environments, monitoring |
| Project Management + BA | 15 | Scoping, stakeholder reviews, sign-offs |
| **Total** | **~220 person-days** | **~10–11 months for a 1-person team; ~5 months for a 2-person team** |

**Cost by rate card (blended dev + QA + PM):**

| Rate Scenario | Blended Rate | Estimated Cost |
|---|---|---|
| EY Internal / Offshore Delivery Centre | USD 60–80 / hr | **USD 105,600 – 140,800** |
| Mid-market vendor | USD 100–130 / hr | **USD 176,000 – 228,800** |
| EY Advisory / Onshore | USD 180–220 / hr | **USD 316,800 – 387,200** |

> 220 person-days = 1,760 hours (assuming 8 hrs/day)

---

### 3b. API Cost — Per QA Run (Azure OpenAI)

**Assumptions for a single QA run (one engagement):**

| Parameter | Demo / Test | Real-Life (Estimated) |
|---|---|---|
| No. of checkpoints | 29 | 29 |
| Docs uploaded | ~50% of real (no evidence) | Full set incl. evidence files |
| Approx. total document pages | 30–50 pages | 80–150 pages |
| Approx. total document tokens | ~50,000 | ~120,000 – 180,000 |
| Approach | All docs to all CPs | Smart retrieval: ~30% of docs per CP |

**Current (demo) approach — all docs to all checkpoints:**

| Item | Tokens | Azure GPT-4o Cost (USD) |
|---|---|---|
| Input — 29 CPs × (50K doc tokens + 1K prompt) | ~1,479,000 | $3.70 |
| Output — 29 CPs × ~600 tokens | ~17,400 | $0.70 |
| Summarisation — Haiku equivalent (GPT-4o-mini) | ~30,000 | $0.05 |
| **Total (demo scale, all-docs approach)** | **~1,526,400** | **~$4.45** |

**Real-life (full docs, all-docs approach — not recommended):**

| Item | Tokens | Azure GPT-4o Cost (USD) |
|---|---|---|
| Input — 29 CPs × (150K doc tokens + 1K prompt) | ~4,379,000 | $10.95 |
| Output — 29 CPs × ~600 tokens | ~17,400 | $0.70 |
| Summarisation (GPT-4o-mini) | ~40,000 | $0.07 |
| **Total (real-life, all-docs approach)** | **~4,436,400** | **~$11.72** |

**Real-life with smart retrieval (recommended production approach):**

| Item | Tokens | Azure GPT-4o Cost (USD) |
|---|---|---|
| Input — 29 CPs × (45K avg relevant tokens + 1K prompt) | ~1,334,000 | $3.34 |
| Output — 29 CPs × ~600 tokens | ~17,400 | $0.70 |
| Embedding (one-time per engagement, text-embedding-3-large) | ~180,000 | $0.02 |
| Summarisation (GPT-4o-mini) | ~40,000 | $0.07 |
| **Total (real-life, smart retrieval)** | **~1,571,400** | **~$4.13** |

> **Recommendation:** Implement smart retrieval in production. It keeps per-run cost at ~$4 regardless of document volume, versus $12+ with the brute-force approach.

**Azure GPT-4o pricing used above:**
- Input: $2.50 / 1M tokens
- Output: $10.00 / 1M tokens
- GPT-4o-mini input: $0.15 / 1M tokens
- text-embedding-3-large: $0.13 / 1M tokens
*(Pricing as of June 2026 — verify with Azure portal for negotiated enterprise rates)*

---

### 3c. Token Cost — Per QA Run Breakdown

| Stage | Model | Input Tokens | Output Tokens | Notes |
|---|---|---|---|---|
| Main QA Engine (29 CPs) | GPT-4o | 1,334,000 | 17,400 | With smart retrieval |
| Observation Summarisation | GPT-4o-mini | 20,000 | 10,000 | ~20 findings, obs rewrite |
| Recommendation Summarisation | GPT-4o-mini | 20,000 | 10,000 | ~20 findings, rec rewrite |
| AI Refinement (if used) | GPT-4o-mini | 15,000 | 8,000 | Optional user-triggered |
| Document Embedding (one-time) | text-embedding-3-large | 180,000 | — | Stored, reused per engagement |
| **Total (per QA run)** | | **~1,569,000** | **~45,400** | |

**Monthly volume cost (indicative):**

| QA Runs / Month | Approx. Monthly LLM Cost |
|---|---|
| 10 runs | ~$41 |
| 50 runs | ~$207 |
| 200 runs | ~$826 |
| 1,000 runs | ~$4,130 |

> This is LLM API cost only. Add Azure infrastructure (App Service, SQL, Blob, etc.) — estimate **$500–$1,500/month** for a production environment depending on scale and redundancy configuration.

---

## Key Decisions for Tech Discussion

| Decision | Options | Recommendation |
|---|---|---|
| LLM Provider | Azure OpenAI only (GPT-4o) vs. multi-model | **Azure OpenAI** — data stays in tenant, simpler compliance |
| Doc retrieval strategy | All-docs vs. smart embedding retrieval | **Smart retrieval** — 3× cheaper, more accurate |
| Deployment model | SaaS vs. internal hosted | **Internal Azure** — data governance, SSO, EY policy |
| Build approach | Internal build vs. vendor vs. EY Tech | Discuss — affects timeline and IP ownership |
| Phasing | MVP (core wizard + AI engine) first, then access mgmt | **Phased** — MVP in 3 months, full in 5–6 months |
