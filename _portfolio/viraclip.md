---
title: "ViraClip — AI Video Processing Platform"
excerpt: "Plataforma SaaS para automatizar la creación y procesamiento de vídeo con IA para redes sociales. Stack: FastAPI, FFmpeg, Redis, ComfyUI, Rust.<br/><img src='/images/500x300.png'>"
collection: portfolio
---

## ViraClip

**ViraClip** es una plataforma SaaS de procesamiento de vídeo con IA diseñada para automatizar la creación de contenido corto para TikTok, Instagram Reels y YouTube Shorts.

### Problema que resuelve

Los creadores de contenido y marketers invierten horas editando vídeos manualmente: añadir subtítulos, recortar clips virales, adaptar formatos, generar voiceovers. ViraClip automatiza todo este flujo.

### Stack técnico

- **Backend:** FastAPI (Python)
- **Procesamiento de vídeo:** FFmpeg con pool manager personalizado para concurrencia
- **IA:** Groq API, OpenAI, ComfyUI workflows, LTX Video
- **Cola de trabajos:** Redis con tracking de progreso en tiempo real
- **Base de datos:** PostgreSQL + Alembic migrations
- **Orquestación:** Docker Compose (backend, worker, comfyui, redis, postgres)
- **Agente Rust:** Watchdog auto-healing que monitoriza y restaura servicios caídos

### Arquitectura destacada

El `ffmpeg_pool.py` gestiona slots de trabajo dinámicamente según disponibilidad de CPU/RAM. El agente Rust corre diagnósticos, reinicia contenedores fallidos y notifica vía webhook, todo sin intervención humana.

### Links

- [GitHub](https://github.com/sebsv123/ViraClip)
