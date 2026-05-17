# 🚀 Setup Guide

Follow this guide to configure and run the AI Automation Revenue Ecosystem.

---

## 📋 Prerequisites

Ensure you have completed the prerequisites listed in the [README](README.md#-prerequisites) before proceeding.

---

## 🔧 Setup Steps

### 1. Import Workflows
1. Clone this repository.
2. In n8n, import all four workflow JSON files:
   - `1C-Lead-Ingestion.json`
   - `2C-Intent-Scoring.json`
   - `3C-AI-Outreach.json`
   - `4C-Knowledge-Base.json`
3. Activate all workflows.

### 2. Configure Credentials
In n8n, create credentials for:
- **Supabase:** API key with `data.records:write` scope.
- **OpenAI:** API key (ensure access to GPT-4o-mini and embeddings).
- **Pinecone:** API key + environment; create indexes `sales-knowledge-base` and `revenue-ops-memory`.
- **Apify:** OAuth2 token; ensure access to `linkedin-jobs-scraper` and `indeed-scraper` actors.
- **Tally:** API key + form ID (`eq2oZQ` for demo).
- **Google:** OAuth2 credentials for Gmail + Drive APIs.
- **Slack:** OAuth2 app or incoming webhook URL for alert channels.

### 3. Update Node References
- Replace Supabase table IDs (`leads`, `interactions`, `campaign_logs`) in all Supabase nodes.
- Update Pinecone index names in vector store nodes.
- Set your Gmail address and Slack channel IDs in notification nodes.
- Configure Apify actor IDs if using custom deployments.

### 4. Test the Ecosystem
#### Test Workflow 1C (Lead Ingestion)
1. Submit a test lead via Tally Form or trigger Apify scraper manually.
2. Verify:
   - `company_domain` extracted correctly (no Gmail/Yahoo domains).
   - Supabase `leads` table contains new record with `status = New`.
   - No duplicate created if same domain already exists.

#### Test Workflow 2C (Intent Scoring)
1. Trigger LinkedIn/Indeed scraper manually.
2. Verify:
   - Leads with automation keywords receive `intent_score ≥ 30`.
   - Low-score leads filtered out; high-score leads inserted as `New`.

#### Test Workflow 3C (AI Outreach)
1. Change a `New` lead's status to `Contacted` to trigger outreach.
2. Verify:
   - Research Agent fetches company news/hiring signals.
   - Email Drafter generates personalized email with valid JSON output.
   - Gmail sends email with correct subject/body; label `Outreach` applied.
   - Email body embedded into Pinecone `revenue-ops-memory` index.

#### Test Workflow 4C (Knowledge Base)
1. Upload a PDF FAQ to the watched Google Drive folder.
2. Verify:
   - PDF text extracted and chunked.
   - Embeddings generated (512-dim) and upserted to Pinecone `sales-knowledge-base`.
   - AI agents can retrieve relevant FAQ snippets during email drafting.

---

## 🛡️ Error Handling & Recovery

### Error Recovery Steps
1. Check Supabase `campaign_logs` table for `status = needs_review` or `failed_validation`.
2. Review Slack alerts for AI confidence notes or parsing failures.
3. Fix root cause (e.g., API quota, invalid domain, malformed JSON).
4. Reset lead `status` to `New` or re-trigger workflow with corrected input.

### Common Issues
| Issue | Cause | Fix |
|-------|-------|-----|
| **Invalid `company_domain`** | ATS subdomain or free email provider | Add exclusions to normalization JS logic |
| **AI JSON parsing failure** | Markdown-wrapped output or extra text | Strengthen system prompt; add retry with lower temperature |
| **Supabase duplicate insert** | Race condition in concurrent checks | Use "Loop Over Items" + `executeOnce: true`; add DB unique constraint |
| **Pinecone empty retrieval** | Dimension mismatch or unpopulated index | Verify embedding dimensions; confirm Workflow 4C upsert succeeded |
| **Gmail send failure** | OAuth token expired or quota exceeded | Refresh credentials; implement exponential backoff retry |

---

## 💡 Pro Tips

- **Sanitize Credentials:** Never commit API keys or tokens to version control. Use n8n's credential encryption.
- **Demo Video:** Record a Loom video showing end-to-end flow (form submission → AI email → Slack alert) for portfolio showcase.
- **Highlight Reliability:** Emphasize the `company_domain` deduplication logic and confidence-based escalation in interviews.
- **Document Learnings:** Add a "Challenges & Solutions" section noting how you solved concurrency, parsing, and domain normalization issues.
- **Cost Monitoring:** Set up Supabase row count alerts and OpenAI usage dashboards to track spend as you scale.

---

## 📞 Support

For questions or issues:
- Open an issue in this repository with workflow name + error logs.
- Contact the author: [kevynalojepan@gmail.com](mailto:kevynalojepan@gmail.com)

---

## 📄 License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

> ✨ **Built by Kevyn Alojepan** — Turning AI automation into revenue, one workflow at a time.
