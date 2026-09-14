# Detailed Projects & Engineering Showcase - Anandhu V S

This document provides a comprehensive, untruncated breakdown of **all 24 production projects, SaaS platforms, cloud architectures, AI pipelines, DevOps case studies, UI/UX designs, and commercial platforms** built and documented by **Anandhu V S**.

---

## 🏦 Project 1: Ferrari Foods LLC – Enterprise Financial & Cash Vault Operations System (>1M AED Cash Managed)

### 📌 Summary & Production Scale
* **Production Status**: Deployed enterprise digital backbone for Ferrari Foods LLC across **2 enterprise branches** (Sharjah & Abu Dhabi / Al Fajar AUH).
* **Capital & Workload**:
  * **>AED 1,000,000+ ($272,000+ USD)** in active corporate cash vaults tracked in real time.
  * **50 to 60 daily high-value ledger entries** processed continuously.
  * **5 Field Collection Agents** (e.g. *Thavab*, *Shuhaib*) syncing batched collection sessions to `ferrari-backend-vm` on GCP (`me-central1-a`).
  * **Denomination Inventory Tracking**: Real-time breakdown of 1,000, 500, 200, 100 AED notes and coins across Accountant Vault, CEO Vault, Bank Vault, and Petty Cash.
* **Modules**: Dashboard Overview (Live Sync AED 103,462 balance), Mass Inflow, Accountant Vault, CEO Vault, Bank Vault, Sales Entry, Expenses, Petty Cash, Exchange, Master Ledger, Employee Management, Inventory, LPO Generator, Vendors.

---

## 📱 Project 2: Enterprise Multimodal WhatsApp AI Assistant & Cross-Project Ledger Engine

### 📌 Summary & Code Repository
* **GitHub Repository**: [https://github.com/Anandhu362/Whatsapp-AI-Bot](https://github.com/Anandhu362/Whatsapp-AI-Bot)
* **Tech Stack**: TypeScript 5.4, Node.js 20.x, Google Gemini 2.5 Flash, Firebase Admin SDK, Google BigQuery, Google Sheets API, Docker Multi-stage builds.
* **Architecture**: Cross-Project IAM Service Account Application Default Credentials (ADC) bridging Project A (Local Bot Engine: Baileys WS, Firebase Realtime DB session state, LID authorization) and Project B (Remote Corporate Accounting System for Ferrari Foods LLC).
* **Features**: Automated Ledger Reversal Engine (atomic 5-collection Firestore batch deletions, non-repudiation audit logging) & Multimodal Sales CRM (Gemini 2.5 Flash vision OCR, `Asia/Dubai` timezone math, dual-write to Firestore & Google Sheets).

---

## 📉 Project 3: GCP Cloud Run Serverless Cost Optimization Case Study (90% Reduction)

### 📌 Published Case Studies
* 📝 **Blogger Case Study**: [https://lnkd.in/dnfPV66P](https://lnkd.in/dnfPV66P)
* ✍️ **Medium Summary**: [https://lnkd.in/d4Jqp2-m](https://lnkd.in/d4Jqp2-m)
* 🌐 **Portfolio**: [https://lnkd.in/g4b95Qja](https://lnkd.in/g4b95Qja)
* **Impact**: Reduced monthly cloud backend bill from **~$17.92/month** (Compute Engine VM) to **~$1.40/month** (Cloud Run serverless) for a production Node.js + TypeScript backend.

---

## 🛠️ Project 4: Production Backend Migration (Serverless GCP Cloud Run to Dedicated VM)

### 📌 Summary & Architecture
* **Problem Solved**: Cloud Run CPU billing spikes, 24/7 WebSocket drops (`@whiskeysockets/baileys` disconnect 409), and frozen 08:00 AM `node-cron` jobs during scale-to-zero.
* **Solution**: GCP Compute Engine VM (`e2-medium`, `me-central1-a`, Ubuntu 22.04 LTS) + Caddy Reverse Proxy (Auto-SSL) with 4-stage zero-downtime rollback pipeline (`ferrari-backend-old`, 35s schema evaluation, Docker `HEALTHCHECK`).
* **Impact**: >50% cut in GCP cloud bills, 100% WebSocket uptime.

---

## 🍽️ Project 5: MenuSent – On-the-Fly Edge Image Delivery Network & Cloud CDN

### 📌 Summary & Architecture
* **Target System**: MenuSent B2B Restaurant SaaS.
* **Solution**: Node.js + Sharp microservice on GCP Cloud Run behind Google Global HTTP(S) Load Balancer & Google Cloud CDN.
* **Impact**: Dynamically resizes heavy 5MB images into ~30KB WebP assets cached at UAE edge nodes for **0ms response time**.

---

## 🖨️ Project 6: MenuSent – Cloud-to-Kitchen Hardware POS Printing Integration

### 📌 Summary & Architecture
* **Solution**: Direct cloud triggers for Wi-Fi static IP and Bluetooth thermal POS printers with user-defined control in the MenuSent admin panel.
* **Impact**: 100% order ticket printing delivery reliability with zero lost orders.

---

## 🤖 Project 7: Enterprise "Dual-Write" AI Document Ingestion Pipeline

### 📌 Summary & Architecture
* Node.js + React + GCP architecture streaming audited Vertex AI (Gemini 2.5) extractions concurrently via `Promise.all` to Google Sheets (operational) AND Google BigQuery (analytical ML warehouse) with HITL React verification.

---

## 📄 Project 8: AI Invoice Ingestion Pipeline (Sharp Compression & Confidence Scoring)

### 📌 Summary & Architecture
* Collaborated with Senior Accountant Muhammed Faris Chakkalakkunnan. Node.js `sharp` library pre-processes (compresses & converts to grayscale) 3MB+ invoice photos before GCS vaulting; Gemini 2.5 Flash calculates `confidence_score` to flag low-confidence documents.

---

## 📁 Project 9: CloudDrive SaaS Platform (Phases 1 & 2 Complete)

### 📌 Summary & Architecture
* **GitHub Repository**: [https://lnkd.in/gRnsut3Y](https://lnkd.in/gRnsut3Y)
* Phase 1: Password hashing, user-isolated GCS buckets, storage quota tracking.
* Phase 2: Metadata-based account-to-account file transfers (zero file duplication, zero transfer delay), shared folder collaboration, link expiry, and API rate limiting.

---

## 🔒 Project 10: 24x7 Cloud WireGuard VPN Server Infrastructure (GCP Mumbai Region)

### 📌 Summary & Architecture
* Deployed Ubuntu 24.04 LTS VM on GCP (`asia-south1` region) with static external IP, custom firewall rules (`UDP Port 51820`), NAT setup, IP forwarding, and security hardening (`wg show` validation).

---

## 📱 Project 11: n8n AI Agent WhatsApp Assistant (Twilio + Gemini 2.5 Flash Lite)

### 📌 Summary & Architecture
* n8n AI Agent with Gemini 2.5 Flash Lite processing WhatsApp messages via Twilio Webhooks into structured Google Sheets rows (10k req/day on Google AI Studio Paid Tier 1).

---

## 🛠️ Project 12: Windows-to-Linux Cloud Build & Cloud Run Troubleshooting Case Study

### 📌 Summary & Findings
* Documented fixes for Windows → Linux file permission mismatches, Cloud Build `tsc` permission errors using `npx tsc`, `.dockerignore` context size minimization (>80% faster context upload), and env secret injection into Cloud Run.

---

## 🏢 Project 13: AdsPro Designing LLC – Cloud-Native Production Web Platform

### 📌 Summary & Deployment
* **Live Website**: [https://www.adsprodxb.com](https://www.adsprodxb.com)
* Built with React, Node.js, containerized with Docker, deployed on GCP Cloud Run serverless (1,135+ impressions).

---

## 🌍 Project 14: RR Business Group – Dubai Export Industry Web Platform

### 📌 Summary & Deployment
* **Live Demo**: [http://bit.ly/404mtRe](http://bit.ly/404mtRe)
* React.js + Tailwind + Vite platform hosted on GCP Compute Engine VM (`n2-standard` instance) featuring real-time product category filters and detail modals.

---

## 🔗 Project 15: MERN Stack Geo-Analytics URL Shortener Web Application

### 📌 Summary & Deployment
* **Live Demo**: [https://lnkd.in/gZt-b8uh](https://lnkd.in/gZt-b8uh) | **GitHub Repo**: [https://lnkd.in/gJTa-36Z](https://lnkd.in/gJTa-36Z)
* MERN Stack app with real-time Geo API tracking visitor location, device type, and IP address with night mode analytics dashboard.

---

## 🌐 Project 16: Azam Weddings – Commercial Creative Platform

### 📌 Summary & Deployment
* **Live Website**: [https://azamweddings.in](https://azamweddings.in) (790+ impressions).

---

## 💒 Project 17: Teza Events – Intimate Wedding Digital Platform

### 📌 Summary & Deployment
* Digital storytelling platform in Kerala with direct WhatsApp contact routing.

---

## 🧃 Project 18: Dubai Fresh Juice Manufacturing Brand B2B Web Platform

### 📌 Summary & Deployment
* **Live Demo**: [https://lnkd.in/d6uXGeXG](https://lnkd.in/d6uXGeXG) (596+ impressions).

---

## 🎨 Project 19: Developer Portfolio Platform (Web, AR/VR & Cybersecurity Showcase)

### 📌 Summary & Deployment
* **Live Portfolio**: [https://lnkd.in/g4b95Qja](https://lnkd.in/g4b95Qja) (2,385+ impressions). Built with React.js, Vite, and Tailwind CSS.

---

## 📱 Project 20: Figma Mobile UI/UX Design System – Book Ordering App

### 📌 Summary & Prototypes
* High-fidelity mobile UI prototypes for Home (discounts & book categories), Details, Checkout, and Order tracking.

---

## ☕ Project 21: Figma Mobile UI/UX Design System – Coffee Ordering App

### 📌 Summary & Prototypes
* High-fidelity mobile UI prototypes for Splash screen, Menu ("Buy 1 Get 1"), Custom Brew Selector, and Checkout.

---

## 📝 Project 22: ALTOS Technologies – Interactive To-Do List Application

### 📌 Summary & Links
* **Live Preview**: [https://lnkd.in/gFJ8j8dV](https://lnkd.in/gFJ8j8dV) | **GitHub Repo**: [https://lnkd.in/gK-YjAWF](https://lnkd.in/gK-YjAWF)

---

## 🧮 Project 23: ALTOS Technologies – Mini Calculator App with Audio Feedback

### 📌 Summary & Links
* **Live Preview**: [https://lnkd.in/g5R6pFes](https://lnkd.in/g5R6pFes) (HTML5, CSS3, JS with click sound audio feedback).

---

## 🎙️ Project 24: Web Accessibility Text-to-Speech Converter Application

### 📌 Summary & Tech Stack
* HTML5, CSS3, JavaScript Web SpeechSynthesis API application for text-to-speech conversion.
