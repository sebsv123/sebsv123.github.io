---
title: "Agente Rosa"
excerpt: "AI lead capture agent for insurance brokers — Instagram DM, web widget, WhatsApp handoff."
collection: portfolio
date: 2025-06-01
header:
  teaser:
---

## Agente Rosa

Conversational AI agent that qualifies insurance leads automatically. A prospect messages on Instagram or clicks a web widget — Rosa engages them, scores the lead using RAG over a product catalog, and fires a WhatsApp handoff when the score is high enough.

### Key features

- **RAG pipeline** with pgvector + sentence-transformers over insurance product catalog
- LLM-based **lead scoring** (configurable threshold)
- **Instagram DM webhook** integration via Meta API
- **Embeddable web widget** (drop-in HTML button)
- **n8n workflow** automation — lead intake, CRM push, notifications
- **Cloudflare Tunnel** — no exposed ports, production-safe
- Swappable LLM backend: OpenAI GPT-4o-mini or Groq Llama 3.3

### Stack

`Python` `FastAPI` `PostgreSQL` `pgvector` `sentence-transformers` `n8n` `Cloudflare` `Docker` `Next.js`

### Links

- [GitHub Repository](https://github.com/sebsv123/agente_seguros_ai)
