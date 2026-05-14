---
permalink: /
title: ""
author_profile: true
redirect_from:
  - /about/
  - /about.html
---

<style>
.terminal-intro {
  font-family: 'Courier New', Courier, monospace;
  background: #0d0d0d;
  border: 1px solid #00ff88;
  border-radius: 6px;
  padding: 1.5rem 2rem;
  margin-bottom: 2rem;
  color: #e0e0e0;
}
.terminal-intro .prompt { color: #00ff88; }
.terminal-intro .cursor {
  display: inline-block;
  width: 10px;
  height: 1em;
  background: #00ff88;
  animation: blink 1s step-end infinite;
  vertical-align: text-bottom;
  margin-left: 2px;
}
@keyframes blink { 50% { opacity: 0; } }
.project-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 1.2rem;
  margin: 1.5rem 0;
}
.project-card {
  background: #111;
  border: 1px solid #1a1a1a;
  border-left: 3px solid #00ff88;
  border-radius: 4px;
  padding: 1.2rem 1.4rem;
  transition: border-color 0.2s;
}
.project-card:hover { border-left-color: #00ccff; }
.project-card h3 { margin: 0 0 .4rem 0; font-size: 1rem; font-family: monospace; color: #00ff88; }
.project-card p { margin: 0 0 .8rem 0; font-size: .875rem; color: #aaa; line-height: 1.5; }
.project-card .tags { font-size: .75rem; color: #555; font-family: monospace; }
.project-card a { color: #00ccff; text-decoration: none; font-size: .8rem; }
.project-card a:hover { color: #00ff88; }
.stack-badges { display: flex; flex-wrap: wrap; gap: .5rem; margin: 1rem 0; }
.stack-badge {
  background: #111;
  border: 1px solid #222;
  color: #00ff88;
  font-family: monospace;
  font-size: .75rem;
  padding: .2rem .6rem;
  border-radius: 3px;
}
</style>

<div class="terminal-intro">
  <span class="prompt">sebsv123@madrid:~$</span> whoami<br/>
  <br/>
  <strong style="color:#fff">Sebastián Sifontes</strong> &mdash; Full-Stack AI Engineer<br/>
  Economist turned engineer. I build things that ship.<br/>
  <br/>
  <span class="prompt">sebsv123@madrid:~$</span> cat projects.txt<span class="cursor"></span>
</div>

## Shipping

<div class="project-grid">
  <div class="project-card">
    <h3>🎬 ViraClip</h3>
    <p>AI video clipping engine. URL in, viral shorts out. Self-hostable, async queue, GPU-ready.</p>
    <div class="tags">FastAPI · Next.js 15 · Whisper · ComfyUI · FFmpeg · arq · Docker</div><br/>
    <a href="https://github.com/sebsv123/ViraClip" target="_blank">→ github.com/sebsv123/ViraClip</a>
  </div>
  <div class="project-card">
    <h3>🤖 Agente Rosa</h3>
    <p>Conversational AI agent that qualifies insurance leads via Instagram DM and web widget, then fires a WhatsApp handoff.</p>
    <div class="tags">FastAPI · pgvector · n8n · Groq · Cloudflare Tunnel · Docker</div><br/>
    <a href="https://github.com/sebsv123/agente_seguros_ai" target="_blank">→ github.com/sebsv123/agente_seguros_ai</a>
  </div>
</div>

## Stack

<div class="stack-badges">
  <span class="stack-badge">Python</span>
  <span class="stack-badge">TypeScript</span>
  <span class="stack-badge">FastAPI</span>
  <span class="stack-badge">Next.js</span>
  <span class="stack-badge">PostgreSQL</span>
  <span class="stack-badge">Redis</span>
  <span class="stack-badge">Docker</span>
  <span class="stack-badge">Cloudflare</span>
  <span class="stack-badge">ComfyUI</span>
  <span class="stack-badge">FFmpeg</span>
  <span class="stack-badge">Groq</span>
  <span class="stack-badge">OpenAI</span>
  <span class="stack-badge">n8n</span>
  <span class="stack-badge">Linux</span>
</div>

## About

I studied Economics, then taught myself to build full-stack systems. Now I architect and ship production AI products — from GPU video pipelines to WhatsApp bots with lead scoring.

Based in **Alcorcón, Madrid**. Open to remote contracts and collaborations.

📬 [sebastiansvsv@gmail.com](mailto:sebastiansvsv@gmail.com) &nbsp;·&nbsp; [LinkedIn](https://www.linkedin.com/in/sebastian-sifontes-valentin-752665187) &nbsp;·&nbsp; [GitHub](https://github.com/sebsv123)
