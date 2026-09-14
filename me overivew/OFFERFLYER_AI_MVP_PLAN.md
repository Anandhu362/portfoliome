# 🛒 OfferFlyer AI — Production MVP Architecture & Implementation Plan

**Project Title**: OfferFlyer AI — Multi-Catalog Retail & Flyer GenAI Search Engine  
**Lead Architect**: Anandhu V S  
**Deployment Region**: Google Cloud Platform (me-west1 Dubai / europe-west1)  
**Primary User Medium**: WhatsApp (via `@whiskeysockets/baileys`)  
**Session State Engine**: Cloud Firestore  
**GCP Credit Target**: $1,000 GenAI App Builder / Vertex AI Search & Conversation Trial Credit  

---

## 📑 1. Executive Summary & MVP Scope

OfferFlyer AI transforms static, multi-page weekly supermarket PDF flyers (Carrefour, LuLu, Nesto, Viva, West Zone) across the UAE/GCC into an interactive, zero-friction **WhatsApp AI Search Engine**. 

### MVP Core Capabilities:
1. **WhatsApp Conversational Interface**: Users send text queries (*"Find Nutella 250g under 15 AED in Sharjah"*) or product photos to a dedicated WhatsApp business number powered by `@whiskeysockets/baileys`.
2. **Real-Time Session Management**: User session state, multi-turn dialogue history, location preferences (e.g., Sharjah, Dubai, Abu Dhabi), and query contexts are stored in **Cloud Firestore**.
3. **3-Tier Zero-Cost Hybrid Search**:
   * **Tier 1 ($0.00 Cost)**: Local PostgreSQL `pg_trgm` Trigram & Regex Weight-Lock lookup for repeat hits (<20ms latency).
   * **Tier 2 (GCP Credit)**: Vertex AI Search Data Store (Commerce & Multimodal Indexing) with structured price/weight facets.
   * **Tier 3 (Fallback)**: Gemini 2.5 Flash query disambiguation and intent parser.
4. **Visual Deal Delivery**: Returns a cropped flyer image, exact store branch location, price comparison, page number, and discount validity dates directly in WhatsApp.

---

## 🛠️ 2. Technology Stack & Infrastructure Matrix

| Layer | Technology / Library | Purpose & Rationale |
| :--- | :--- | :--- |
| **User Interface** | `@whiskeysockets/baileys` | Zero-cost open-source WhatsApp Web socket connection (eliminates Twilio per-message fees). |
| **Backend Runtime** | Node.js (v20+) + TypeScript | High concurrency event loop for WebSocket messaging and async cloud APIs. |
| **Session Management** | Cloud Firestore (`@google-cloud/firestore`) | Realtime session tracking, user state machines, search history, and rate-limiting buckets. |
| **Cloud AI Search Core** | GCP Vertex AI Search & Discovery Engine | **Consumes $1,000 GenAI App Builder Credit**. Ingests flyer PDFs/JSON catalogs with hybrid vector embeddings & structured facets. |
| **Fast Local Cache** | PostgreSQL (`pg_trgm`) / Redis | Instant zero-cost local fuzzy lookup for 80% of top consumer queries (Nutella, Milk, Rice). |
| **Document & Crop Storage** | Google Cloud Storage (GCS) | Hosts raw flyer PDFs and auto-cropped deal images with CDN URLs. |
| **LLM & OCR Engine** | `@google/genai` (Gemini 2.5 Flash) | 1-time write phase flyer structuring and visual query intent parsing. |
| **Container & Hosting** | GCP Cloud Run | Serverless container auto-scaling from 0 to N instances with HTTPS webhooks. |
| **Secrets & Security** | GCP Secret Manager | Encrypts Baileys authentication state credentials and GCP service account keys. |

---

## 📂 3. Complete Project Folder & File Structure

Below is the production-ready directory layout for the `offerflyer-ai` project codebase:

```
offerflyer-ai/
├── .github/
│   └── workflows/
│       └── deploy-cloudrun.yml       # Automated CI/CD pipeline to GCP Cloud Run
├── Dockerfile                        # Multi-stage container build for Baileys + Node.js
├── docker-compose.yml                # Local dev setup with Postgres + Redis
├── package.json                      # Node dependencies
├── tsconfig.json                     # TypeScript strict configuration
├── .env.example                      # Environment variables template
├── .dockerignore
├── src/
│   ├── config/
│   │   ├── env.ts                    # Envalidated environment variables loader
│   │   ├── gcp.ts                    # Vertex AI & GCS client initializers
│   │   ├── database.ts               # PostgreSQL connection pool & pg_trgm init
│   │   └── firestore.ts              # Firestore admin initialization
│   ├── whatsapp/
│   │   ├── client.ts                 # Baileys socket client connection manager
│   │   ├── authState.ts              # Custom Firestore auth state storage for Baileys
│   │   ├── handlers/
│   │   │   ├── textMessageHandler.ts # Text query parser & response dispatcher
│   │   │   └── imageMessageHandler.ts# Multimodal image query handler
│   │   └── templates/
│   │       └── messageTemplates.ts   # Formatted WhatsApp deal message layouts
│   ├── services/
│   │   ├── search/
│   │   │   ├── searchOrchestrator.ts # 3-Tier Search router (Cache -> Vertex -> Gemini)
│   │   │   ├── localTrigramService.ts# Tier 1: PostgreSQL pg_trgm + Weight-Lock query
│   │   │   ├── vertexSearchService.ts# Tier 2: GCP Discovery Engine API client
│   │   │   └── geminiFallbackService.ts# Tier 3: Gemini 2.5 query intent parser
│   │   ├── ingestion/
│   │   │   ├── flyerIngestion.ts     # PDF page extractor & GCS cropper
│   │   │   └── dataStoreIndexer.ts   # Pushes structured JSON to Vertex Data Store
│   │   └── session/
│   │       └── sessionManager.ts     # Firestore user session state & location tracking
│   ├── security/
│   │   ├── rateLimiter.ts            # Token-bucket rate limiter per WhatsApp ID
│   │   └── inputSanitizer.ts         # Regex sanitizer against prompt injection & spam
│   ├── types/
│   │   ├── product.ts                # TypeScript interfaces for Deals, Catalogs, Facets
│   │   └── session.ts                # Session state interfaces
│   └── index.ts                      # Main Application Entry Point
└── scripts/
    ├── init_db.sql                   # SQL script initializing PostgreSQL pg_trgm indexes
    └── test_search.ts                # CLI runner to benchmark Tier 1 vs Tier 2 latency
```

---

## ⚡ 4. AI Cost Optimization Engine (3-Tier Protocol)

To ensure your **$1,000 GCP credit** lasts for months and cloud bills remain under **$20/month** in production, OfferFlyer AI implements a **Strict 3-Tier Search Protocol**:

```
                       [ USER QUERY: "Nutella 250g in Sharjah" ]
                                           │
                                           ▼
 ┌──────────────────────────────────────────────────────────────────────────────────┐
 │ TIER 1: PostgreSQL pg_trgm + Regex Weight Lock ($0.00 LLM COST)                  │
 │  • Extract Numeric Weight: 250g -> Hard lock weight_val = 250                    │
 │  • SQL: SELECT * FROM product_cache WHERE weight_val = 250 AND name % 'nutella'  │
 └─────────────────────────────────────────┬────────────────────────────────────────┘
                                           │
                       ┌───────────────────┴───────────────────┐
                       ▼                                       ▼
             [ Score >= 0.65 (HIT) ]                 [ Score < 0.65 (MISS) ]
                       │                                       │
                       ▼                                       ▼
            Return Cached Deal PDF Crop             TIER 2: Vertex AI Search API
            Latency: ~15ms                          (Consumes $1,000 GenAI Credit)
            Cost: $0.00 USD                         Filters facets & re-ranks vectors
                                                               │
                                                   ┌───────────┴───────────┐
                                                   ▼                       ▼
                                            [ Match Found ]         [ Ambiguous Match ]
                                                   │                       │
                                                   ▼                       ▼
                                            Save to Tier 1 Cache    TIER 3: Gemini 2.5
                                            Return Deal to User     Disambiguation Agent
```

### 🔒 Mandatory Weight Lock Rules:
1. Soft AI fuzzy matching frequently confuses `Nutella 250g` with `Nutella 750g` because of 90% character similarity.
2. **Execution**: The input parser extracts weight parameters (`250g`, `1kg`, `500ml`) using regex *before* sending queries to search algorithms:
   ```sql
   SELECT * FROM products 
   WHERE weight_val = 250 
     AND weight_unit = 'g' 
     AND similarity(canonical_name, 'nutella') > 0.4 
   LIMIT 3;
   ```
3. **Result**: Eliminates size mismatch hallucination and prevents unnecessary LLM processing calls.

---

## 📲 5. WhatsApp & Session Management Implementation

### A. WhatsApp Web Sockets via Baileys (`src/whatsapp/client.ts`)
Using `@whiskeysockets/baileys`, OfferFlyer AI connects directly to WhatsApp Web without paying third-party messaging costs per interaction. 

### B. Firestore Auth State Persistence (`src/whatsapp/authState.ts`)
To prevent logging out when GCP Cloud Run scales or restarts containers, Baileys authentication state keys (`creds.json`, `keys`) are automatically backed up to **Cloud Firestore**:

```typescript
// Firestore Baileys Session State Structure
// Firestore Path: /baileys_sessions/{phoneNumber}/auth_state/creds
{
  "noiseKey": "...",
  "signedIdentityKey": "...",
  "registrationId": 4821,
  "updatedAt": "2026-08-26T18:45:00Z"
}
```

### C. User Session Machine (`src/services/session/sessionManager.ts`)
Each user's active session is tracked in Firestore (`/user_sessions/{whatsappNumber}`):

```json
{
  "whatsapp_id": "971501234567@s.whatsapp.net",
  "preferred_emirate": "Sharjah",
  "preferred_branches": ["Carrefour City Centre", "Nesto Muweilah"],
  "last_query": "Nutella 250g",
  "search_count_today": 4,
  "last_active_timestamp": "2026-08-26T18:40:00Z"
}
```

---

## 🛡️ 6. Security & Anti-Abuse Controls

To prevent API credit exhaustion, prompt injection, and WhatsApp account bans, the following security controls are active:

1. **Token-Bucket Rate Limiter per User**:
   * Maximum **10 searches per minute** per WhatsApp ID.
   * Maximum **50 searches per day** for standard tier users.
   * Exceeding limits triggers an automated WhatsApp message: *"Daily search limit reached. Limits reset at midnight."*
2. **Prompt Injection & Input Sanitization**:
   * Truncate incoming query messages to **max 120 characters**.
   * Strip executable syntax, SQL injection tokens, and LLM override prefixes (`Ignore previous instructions`).
3. **Cloud IAM Minimum Privilege**:
   * The Cloud Run Service Account holds *only* the `roles/discoveryengine.viewer` and `roles/datastore.user` roles.
4. **Baileys Anti-Spam Queue**:
   * Implement a 1.5 to 3.0-second randomized delay between WhatsApp message deliveries to mimic human behavior and avoid WhatsApp anti-automation flags.

---

## 🚀 7. Step-by-Step Implementation Roadmap

```
  ┌────────────────────────────────────────────────────────────────────────┐
  │                           PHASED ROADMAP                               │
  ├────────────────────────────────────────────────────────────────────────┤
  │ Phase 1: Environment & Database Layer (Days 1–3)                       │
  │ • Setup GCP Project, GCS Bucket, & Firestore                           │
  │ • Spin up PostgreSQL with pg_trgm extension                            │
  │                                                                        │
  │ Phase 2: Vertex AI Search Data Store Ingestion (Days 4–7)              │
  │ • Upload 5 sample UAE supermarket flyer PDFs to GCS                    │
  │ • Configure Vertex AI Search Commerce Data Store & Schema              │
  │                                                                        │
  │ Phase 3: Baileys WhatsApp & Session Integration (Days 8–11)             │
  │ • Build Baileys socket client with Firestore session persistence      │
  │ • Implement 3-Tier Search Orchestrator                                 │
  │                                                                        │
  │ Phase 4: Containerization & Cloud Run Deployment (Days 12–14)          │
  │ • Build multi-stage Dockerfile                                         │
  │ • Deploy to GCP Cloud Run (me-west1 Dubai region)                      │
  └────────────────────────────────────────────────────────────────────────┘
```

---

## 📄 8. Required Environment Variables (`.env.example`)

```env
# GCP Settings
GCP_PROJECT_ID=your-gcp-project-id
GCP_REGION=me-west1
VERTEX_DATASTORE_ID=offerflyer-datastore-v1
GCS_BUCKET_NAME=offerflyer-flyer-assets

# PostgreSQL Credentials (Tier 1 Cache)
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_USER=offerflyer_user
POSTGRES_PASSWORD=secure_password
POSTGRES_DB=offerflyer_db

# Security & Rate Limiting
MAX_SEARCHES_PER_MINUTE=10
MAX_SEARCHES_PER_DAY=50
```

---

## 🏆 Summary of Career & Technical Impact

By implementing OfferFlyer AI according to this blueprint:
1. **GCP Credit Utilization**: You efficiently spend your **$1,000 GenAI App Builder credit** on high-value Vertex AI Search workloads without bill overruns.
2. **Production-Grade Engineering**: You showcase WhatsApp Web integration (`Baileys`), Firestore session state management, and Cloud Run serverless deployment.
3. **Optimized AI Architecture**: Proves you are a Lead Architect who understands how to cut cloud bills by >90% using hybrid local caching + cloud AI search.
