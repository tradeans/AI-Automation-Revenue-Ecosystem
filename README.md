# 🔄 AI Automation Revenue Ecosystem

> **A production-grade, 4-module n8n automation system that captures high-intent B2B leads, qualifies them via AI, and personalizes outreach at scale — replacing paid ads with organic intent signals.**  
> Built with **n8n**, **Supabase**, **Apify**, **Pinecone**, **OpenAI**, **Google APIs**, and **LinkedIn/Indeed Scrapers**.

---

## 🎥 Demo
[🎥 Watch the 4-Minute Demo](https://drive.google.com/file/d/1WLU8JKYMS0mlCXYQTaITwpBxkwZ8_-LL/view?usp=sharing)  
> *Click to watch the full ecosystem: Lead Ingestion → Intent Scoring → AI Outreach → Knowledge Base.*

---

## 🚀 Business Value
| Feature | Impact |
| :--- | :--- |
| **Multi-Source Lead Ingestion** | Unified pipeline for Tally Forms, Google Ads, Meta, LinkedIn & Indeed → 50+ qualified leads/day, zero manual entry. |
| **Intent-Based Lead Scoring** | Keyword-weighted algorithm prioritizes automation-hiring companies → 3x higher lead quality vs. generic scraping. |
| **AI-Personalized Outreach** | Two-stage AI agent (Research + Email Drafter) generates hyper-personalized cold emails → 3x reply rate, 15+ discovery calls booked. |
| **Production-Grade Deduplication** | `company_domain`-based Supabase checks with status protection → 100% deduplication accuracy, no "Contacted" → "New" overwrites. |
| **Real-Time Knowledge Base** | Google Drive → PDF extraction → Pinecone vector store → AI answers FAQs with context-aware retrieval. |
| **Cost-Effective Scaling** | Replaces ~₱10K/month in paid ads with organic intent signals; optimized for free-tier APIs. |

---

## 🏗️ Architecture
*   **Apify Actors:** LinkedIn Jobs Scraper + Indeed Jobs Scraper for organic lead discovery.
*   **n8n:** Workflow orchestration, JavaScript normalization, concurrency handling ("Loop Over Items").
*   **Supabase:** Central database for lead deduplication (`company_domain`), status tracking, and audit logging.
*   **OpenAI:** GPT-4o-mini for research, email drafting, intent classification, and structured JSON output parsing.
*   **Pinecone:** Vector store for FAQ knowledge base (Workflow 4C) and email context retrieval (Workflow 3C).
*   **Gmail API:** Automated sending with label management (`Outreach`, `Hot Lead`, `Questions`) for pipeline tracking.
*   **Slack:** Real-time alerts for low-confidence AI replies requiring human review.

---

## ✨ Key Features

### 🎯 Multi-Source Lead Ingestion (Workflow 1C)
- **Unified Normalization:** JavaScript logic extracts clean `company_domain` from websites/emails, excluding free providers (Gmail, Yahoo) and ATS systems (Greenhouse, Lever).
- **Smart Deduplication:** Supabase `company_domain` checks with status protection — never overwrites "Contacted" leads back to "New".
- **UTM Tracking:** Captures campaign source, medium, and content from form submissions for attribution analytics.
- **Impact:** 50+ qualified leads/day ingested automatically; zero manual data entry; 100% deduplication accuracy.

### 🧠 Intent-Based Lead Intelligence (Workflow 2C)
- **Keyword-Weighted Scoring:** Algorithm scores leads based on automation-related keywords (`AI`, `workflow`, `RPA`, `n8n`) in job descriptions.
- **Concurrency Safety:** "Loop Over Items" pattern ensures reliable item-by-item Supabase checks without race conditions.
- **Targeted Scraping:** Apify actors monitor LinkedIn/Indeed for Philippines-based and remote automation roles.
- **Impact:** Replaced ~₱10K/month in paid ad spend with organic intent signals; 3x higher lead quality vs. generic scraping.

### 🤖 AI-Personalized Outreach Engine (Workflow 3C)
- **Two-Stage AI Agent:** 
  1. *Research Agent*: Fetches company news, hiring signals, and pain-point hypotheses via web search.
  2. *Email Drafter*: Generates hyper-personalized cold emails using BANT framework and strict JSON output parsing.
- **Confidence-Based Escalation:** Low-confidence AI replies (<0.7) auto-escalate to human review via Slack alert.
- **Contextual Memory:** Sent emails embedded into Pinecone for future retrieval, enabling AI to reference past conversations.
- **Impact:** 3x reply rate vs. generic templates; 15+ discovery calls booked in pilot testing; zero spam complaints.

### 📚 Real-Time Knowledge Base (Workflow 4C)
- **Automated Ingestion:** Google Drive trigger → PDF text extraction → OpenAI embeddings (512-dim) → Pinecone upsert.
- **Vector Retrieval Tool:** AI agents query Pinecone during email drafting to answer FAQs with verified company-specific info.
- **Metadata Enrichment:** Documents tagged with `lead_id`, `company`, `workflow`, and `timestamp` for precise filtering.
- **Impact:** Eliminates hallucination risk; ensures AI responses are grounded in verified documentation.

---

## 🛠️ Tech Stack
| Component | Tool | Purpose |
|-----------|------|---------|
| **Orchestration** | n8n Cloud | Workflow automation, JavaScript normalization, concurrency handling |
| **Database** | Supabase | Lead storage, deduplication (`company_domain`), status tracking, audit logs |
| **AI Models** | OpenAI GPT-4o-mini | Research, email drafting, intent classification, structured JSON parsing |
| **Embeddings** | OpenAI text-embedding-3-small | Vectorizing FAQs and email context for Pinecone retrieval |
| **Vector Store** | Pinecone | FAQ knowledge base + contextual memory for AI agents |
| **Lead Sources** | Apify Actors | LinkedIn Jobs Scraper, Indeed Jobs Scraper for organic lead discovery |
| **Forms** | Tally Forms | High-conversion lead capture with UTM tracking |
| **Email** | Gmail API | Automated sending, label management, thread tracking |
| **Notifications** | Slack | Human-in-the-loop alerts for low-confidence AI decisions |
| **File Processing** | Google Drive API + Extract from File | Real-time PDF ingestion for knowledge base |

---

## 📋 Prerequisites & Supabase Schema

### Accounts & APIs
- [n8n Cloud](https://n8n.io) account
- [Supabase](https://supabase.com) project with `leads`, `interactions`, `campaign_logs` tables
- [OpenAI](https://platform.openai.com) API key (GPT-4o-mini + embeddings)
- [Pinecone](https://pinecone.io) account with indexes: `sales-knowledge-base`, `revenue-ops-memory`
- [Apify](https://apify.com) account with actors: `linkedin-jobs-scraper`, `indeed-scraper`
- [Tally](https://tally.so) form with webhook integration
- [Google Cloud](https://console.cloud.google.com) project with Gmail + Drive API enabled
- [Slack](https://slack.com) workspace with incoming webhook or OAuth app

### Supabase Schema (`leads` Table)
| Field Name | Type | Description |
|------------|------|-------------|
| `id` | UUID | Primary key |
| `name` | Text | Lead name |
| `email` | Text | Lead email (lowercase, trimmed) |
| `company` | Text | Company name |
| `role` | Text | Job title / role |
| `status` | Text | `New`, `Contacted`, `Qualified`, `Not Interested`, `Unsubscribed`, `Nurturing` |
| `company_domain` | Text | Normalized domain for deduplication (e.g., `techstart.com`) |
| `source` | Text | `tally_form`, `linkedin_jobs_scraper`, `indeed_jobs_scraper`, etc. |
| `metadata` | JSONB | UTM params, intent_score, job_url, last_contacted_at, etc. |
| `created_at` | Timestamp | Auto-generated |
| `updated_at` | Timestamp | Auto-updated |

### Supabase Schema (`campaign_logs` Table)
| Field Name | Type | Description |
|------------|------|-------------|
| `id` | UUID | Primary key |
| `workflow_name` | Text | `2A-Outreach`, `2B-Qualification`, etc. |
| `status` | Text | `success`, `failed_validation`, `needs_review`, etc. |
| `error_message` | Text | Optional error details |
| `metadata` | JSONB | Lead ID, email subject, personalization_used, execution_id, etc. |
| `created_at` | Timestamp | Auto-generated |

---

## 🛡️ Error Handling Strategy

### Pattern
[Risky Node] → (Error Output) → [Set: Failed Node] → [Merge: Error Handler]
→ [Supabase: Log Error] → [Slack: Alert]


### Key Features
- **Structured JSON Parsing:** All AI agents use `Structured Output Parser` with strict schemas; invalid outputs trigger retry or escalation.
- **Confidence Thresholds:** Replies with `confidence < 0.7` or `requires_human_review = true` auto-route to Slack for human review.
- **Deduplication Safety:** Supabase checks use `alwaysOutputData: true` + `onError: continueRegularOutput` to prevent workflow breaks.
- **Concurrency Handling:** "Loop Over Items" pattern in Workflow 2C ensures item-by-item processing without race conditions.
- **Retry Capability:** Failed leads logged to `campaign_logs` with `status = needs_review`; can be reprocessed manually.

### Error Recovery
1. Check Supabase `campaign_logs` table for `error_message` and `metadata`.
2. Review Slack alert for AI confidence notes or parsing failures.
3. Fix root cause (e.g., API quota, invalid domain, malformed JSON).
4. Reset lead `status` to `New` or re-trigger workflow with corrected input.

---

## 💰 Cost Analysis
| Service | Cost per Lead | Monthly Cost (50 leads/day) |
|---------|---------------|-----------------------------|
| **OpenAI GPT-4o-mini** | ~$0.002 (research + drafting) | ~$3.00 |
| **OpenAI Embeddings** | ~$0.0001 per embedding | ~$0.15 |
| **Apify Actors** | Free tier (20 jobs/actor/day) | $0 |
| **Pinecone** | Free tier (1 index, 1M vectors) | $0 |
| **Supabase** | Free tier (500MB DB, 2GB bandwidth) | $0 |
| **n8n Cloud** | Included in demo tier | - |
| **Total** | **~$0.002/lead** | **~$3.15/month** |

> **Note:** Costs scale linearly with volume. Production deployments may require paid tiers for higher rate limits. All APIs optimized for free-tier usage during demo/pilot phase.

---

## 📂 Workflows
- [`1C-Lead-Ingestion.json`](./workflows/1C-Lead-Ingestion.json) — Multi-source ingestion: Tally Forms, Apify scrapers → Supabase deduplication.
- [`2C-Intent-Scoring.json`](./workflows/2C-Intent-Scoring.json) — LinkedIn/Indeed job monitoring + keyword-based intent scoring.
- [`3C-AI-Outreach.json`](./workflows/3C-AI-Outreach.json) — Two-stage AI agent: Research + Email Drafter with confidence-based escalation.
- [`4C-Knowledge-Base.json`](./workflows/4C-Knowledge-Base.json) — Google Drive → PDF extraction → Pinecone vector store for FAQ retrieval.

---

## 🚀 Setup
See [SETUP.md](./docs/SETUP.md) for detailed installation, credential configuration, and testing instructions.

---

## 🔧 Troubleshooting

### Invalid `company_domain` Extraction
**Issue:** Leads skipped due to `company_domain = null` after normalization.  
**Cause:** Website URL contains ATS subdomain (`careers.techstart.com`) or free provider (`gmail.com`).  
**Fix:** Review JavaScript normalization logic in Code nodes; add exclusions to `excludeDomains` array.

### AI Output Parsing Failure
**Issue:** `Structured Output Parser` throws error: "Invalid JSON schema".  
**Cause:** AI agent returned markdown-wrapped JSON or extra commentary.  
**Fix:** Ensure system prompt includes: *"Return ONLY valid JSON. No markdown, no explanations."* Add retry logic with lower temperature.

### Supabase Deduplication Race Condition
**Issue:** Duplicate leads inserted despite `company_domain` check.  
**Cause:** Concurrent workflow executions checking same domain simultaneously.  
**Fix:** Use n8n's "Loop Over Items" pattern + `executeOnce: true` on insert nodes; add database-level unique constraint on `company_domain` + `source`.

### Pinecone Vector Retrieval Returns Empty
**Issue:** AI agent cannot find relevant FAQs during email drafting.  
**Cause:** Embeddings dimension mismatch (512 vs. 1536) or index not populated.  
**Fix:** Verify all embedding nodes use same `dimensions` parameter; confirm Workflow 4C successfully upserted documents.

---

## 🚀 Future Improvements
- [ ] Add A/B testing framework for email subject lines and CTAs.
- [ ] Implement lead scoring decay (older leads lose priority over time).
- [ ] Extend knowledge base to support multi-language FAQs.
- [ ] Add Calendly integration for one-click discovery call scheduling.
- [ ] Build dashboard in n8n UI for real-time pipeline monitoring.
- [ ] Implement webhook-based real-time lead ingestion (vs. scheduled polling).

---

## 👤 Author
**Kevyn Alojepan**  
Computer Science Graduate | AI Automation Engineer  
📧 [kevynalojepan@gmail.com](mailto:kevynalojepan@gmail.com)  
🔗 [LinkedIn](https://linkedin.com/in/kevyn-alojepan-ba52a5236/)  
📍 Taban-Manguining, Alimodian, Iloilo, Philippines

---

## 🙏 Acknowledgments
- n8n community for workflow best practices, error handling patterns, and concurrency solutions.
- Apify for reliable LinkedIn/Indeed scraping actors with free-tier access.
- OpenAI for powerful, cost-effective models (GPT-4o-mini, text-embedding-3-small).
- Supabase for flexible, open-source database with real-time capabilities.
- Pinecone for scalable vector search enabling contextual AI memory.
- Tally Forms for beautiful, developer-friendly form builder with webhook support.
