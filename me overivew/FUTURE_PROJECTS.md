# 🛒 OfferFlyer AI — Zero-Cost Hybrid Fuzzy Asset Search Engine

> 📄 **Production Implementation Plan**: See detailed file structure, tech stack, WhatsApp Baileys integration, and Firestore session architecture in [`OFFERFLYER_AI_MVP_PLAN.md`](file:///c:/Users/amegh/Downloads/Projects/Profile%20Overview/OFFERFLYER_AI_MVP_PLAN.md).

**Project Title**: OfferFlyer Zero-Cost Hybrid Asset Search & Weight-Locked Directory  
**Architect**: Anandhu V S  
**Core Goal**: Cut monthly LLM API costs by **>95%** by building a local deterministic Fuzzy Keyword & Trigram Indexing Engine with strict Weight-Lock filtering, using LLM APIs *only* on upload and local search failure fallback.

---

## 📌 Problem Analysis & Architectural Cost Challenge

* **The Cost Problem**: Calling LLM Vision / Embeddings APIs on every single retrieval search query on WhatsApp generates massive API bills at month end ($0.002 to $0.01 per query x thousands of daily searches).
* **The Engineering Solution**: **2-Tier Hybrid Architecture**:
  1. **Write Phase (Save Time)**: Execute a 1-time AI enrichment to generate a local **Fuzzy Keyword Directory** & normalized **Weight Lock Index**.
  2. **Read Phase (Search Time - $0 API Cost)**: Search against the local PostgreSQL `pg_trgm` / Fuse.js index. **Zero LLM API calls on successful local hits!**
  3. **Fallback Phase**: Call LLM API *only* when local fuzzy confidence falls below threshold (<65%), and automatically learn the new search alias for future 0-cost hits.

---

## 🗺️ System Architecture Diagram

```
 ┌──────────────────────────────────────────────────────────────────────────────────┐
 │                        WRITE PHASE (Image Upload & Indexing)                     │
 │  • Send Image + Caption ("nutella 250gm") on WhatsApp                             │
 │  • Save Raw Image to Google Cloud Storage (GCS)                                  │
 │  • 1-Time AI Extractor & Keyword Generator:                                      │
 │    - Normalize & Hard Lock Weight: 250g (unit: GRAM, val: 250)                    │
 │    - Generate Fuzzy Aliases: ['nutella', 'nutela', 'nutella 250', 'nutella jar']   │
 │    - Store in Local PostgreSQL Trigram Index (pg_trgm)                           │
 └─────────────────────────────────────────┬────────────────────────────────────────┘
                                           │
                                           ▼
 ┌──────────────────────────────────────────────────────────────────────────────────┐
 │                       READ PHASE (Product Retrieval - $0 API Cost)               │
 │  • User types query on WhatsApp (e.g. "nutela 250g")                             │
 │                                                                                  │
 │   ┌──────────────────────────────────────────────────────────────────────────┐   │
 │   │ LEVEL 1: Local Trigram & Weight Lock Filter ($0.00 COST)                  │   │
 │   │  • Filter 1: Extract Weight '250g' -> Hard match weight_val = 250        │   │
 │   │  • Filter 2: Fuzzy Trigram Similarity on 'nutela' -> Confidence Score     │   │
 │   └────────────────────────────────────┬─────────────────────────────────────┘   │
 │                                        │                                         │
 │                       ┌────────────────┴────────────────┐                        │
 │                       ▼                                 ▼                        │
 │            [ Score >= 65% (MATCH) ]             [ Score < 65% (MISS) ]           │
 │                       │                                 │                        │
 │                       ▼                                 ▼                        │
 │           Return GCS Image Instantly           LEVEL 2: Gemini LLM Fallback      │
 │           ($0.00 LLM API Cost)                 Disambiguate & update local index │
 └──────────────────────────────────────────────────────────────────────────────────┘
```

---

## 💡 Detailed Component Mechanics

### 🔒 1. The Mandatory Weight Preservation Engine (Hard Lock)
Weight and volume tokens (`250gm`, `250g`, `0.25kg`, `1L`, `500ml`, `5kg`) are **HARD MANDATORY FILTERS**, not soft string metrics.
* **Why**: Soft fuzzy metrics frequently confuse `Nutella 250g` with `Nutella 750g` due to 90% character similarity.
* **Mechanism**:
  1. Regex Extractor parses numeric value & unit at upload: `250gm` -> `normalized_weight_grams = 250`.
  2. During query parsing, if user types `nutela 250`, the search engine extracts `250` and applies SQL condition:  
     `WHERE normalized_weight_grams = 250 AND trigram_similarity(name, 'nutela') > 0.4`
  3. **Result**: `Nutella 750g` and `Nutella 1KG` are immediately excluded, eliminating copycat size mismatches!

---

### 📇 2. Save Time: Local Fuzzy Permutation & Trigram Indexing
When saving a product (`Nutella Hazelnut Spread 250g`), the backend builds a local lookup directory:

```json
{
  "product_id": "PROD-NUTELLA-250G",
  "canonical_name": "Nutella Hazelnut Spread 250g",
  "weight_grams": 250,
  "gcs_image_url": "https://storage.googleapis.com/offerflyer/nutella-250g.jpg",
  "fuzzy_keywords": [
    "nutella",
    "nutela",
    "nutella 250",
    "nutela 250g",
    "nutella hazelnut",
    "nutella 250gm",
    "hazelnut spread 250g"
  ],
  "phonetic_code": "NTL HSLNT SPRD"
}
```

---

### 🔍 3. Read Phase: 3-Stage Zero-Cost Search Protocol

```
 [ Query: "nutela 250g" ]
           │
           ▼
 [ Stage 1: Regex Weight Parser ] ──► Extracted Weight: 250g
           │
           ▼
 [ Stage 2: SQL Trigram Match (pg_trgm) ]
 SELECT * FROM products 
 WHERE weight_grams = 250 
   AND (canonical_name % 'nutela' OR 'nutela' = ANY(fuzzy_keywords))
 ORDER BY similarity(canonical_name, 'nutela') DESC LIMIT 1;
           │
           ├───────────────────────────────┐
           ▼ (Score >= 0.65)               ▼ (Score < 0.65)
 [ RETURN IMAGE DIRECTLY ]      [ LEVEL 2: CALL LLM API ]
   Cost: $0.00 USD                 • LLM parses ambiguous intent
                                   • Saves result to fuzzy_keywords
                                   • Future searches hit $0.00 path!
```

---

## 💰 Cost Analysis: 100% LLM vs. Zero-Cost Hybrid Engine

| Metric | 100% LLM Search Architecture | Zero-Cost Hybrid Trigram Engine |
| :--- | :--- | :--- |
| **Search API Cost per Query** | ~$0.005 – $0.015 USD | **$0.00 USD** (Local PostgreSQL query) |
| **Monthly Bill (10k searches)**| **$50.00 – $150.00 USD / month** | **<$2.00 USD / month** (98.7% Cost Savings) |
| **Response Latency** | 1,500ms – 3,000ms (Network delay) | **15ms – 40ms** (Instant Local Database Hit) |
| **Offline Search Capability** | ❌ No (Fails on API outage) | ✅ Yes (Local index always available) |
| **Weight Precision** | ⚠️ Soft LLM guessing | 🛡️ **100% Hard Numeric Lock Guarantee** |
