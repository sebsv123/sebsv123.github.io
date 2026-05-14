---
title: 'Building a WhatsApp Lead Qualification Bot with n8n and LLMs'
date: 2026-05-14
permalink: /posts/2026/05/whatsapp-bot-n8n-llm/
tags:
  - n8n
  - WhatsApp
  - AI
  - automation
  - insurance
---

For the agente_seguros_ai project, I built a WhatsApp bot that qualifies insurance leads automatically — 24/7, no human needed unless the lead is hot. Here's how the architecture works.

## Why n8n

n8n is a self-hostable workflow automation tool (think Zapier, but open source and deployable on your own VPS). It handles the WhatsApp webhook, conversation state, and routing logic without writing a single line of code for those parts.

I only write code for the parts that need it: the LLM prompt, the database writes, and the lead scoring.

## The Flow

```
WhatsApp message
    ↓
n8n webhook trigger
    ↓
Load conversation history from PostgreSQL
    ↓
Build prompt with context + user message
    ↓
Groq API (Llama 3.1) generates response
    ↓
Parse structured data (name, phone, interest)
    ↓
Update PostgreSQL + score lead
    ↓
Send reply via WhatsApp Business API
    ↓
If hot lead → notify agent via Telegram
```

## The Prompt Strategy

The system prompt has two modes:

1. **Qualification mode**: The bot asks 4–5 questions naturally (not as a form) to gather: full name, type of insurance needed, current coverage, budget range.
2. **Handoff mode**: Once qualified, the bot says a human advisor will contact them and stops generating AI responses.

The trick is injecting the conversation history as context so the model doesn't repeat questions already answered.

## Handling State in PostgreSQL

Each conversation has a `state` field: `qualifying`, `qualified`, `handed_off`, `cold`. n8n checks this before deciding whether to call the LLM or just send a static message.

## Lessons Learned

- **Groq is fast enough for real-time chat** — typical latency is 400–800ms including the WhatsApp API round-trip.
- **Structured output matters** — ask the LLM to return JSON for extracted data, not free text.
- **Test edge cases manually** — users who answer in voice notes, send images, or write in regional dialect.
- **n8n's error handling is weak** — wrap your custom function nodes in try/catch and log to a separate table.
