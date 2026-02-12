<div align="center">
  <img src="https://github.com/user-attachments/assets/56691739-33d5-44b2-83f6-ea68fb55e985" alt="Sift" width="100" />
  <h1>Sift</h1>
  <p><strong>A personal AI assistant for students and professionals — available over iMessage, SMS, and web.</strong></p>
  <p>Text <strong>+1 (303) 994-9654</strong> to get started</p>
  <p>
    <a href="https://app.usesift.app">App</a> &middot;
    <a href="https://usesift.app">Website</a> &middot;
    <a href="https://usesift.app/pricing">Pricing</a>
  <br />
</div>

<br />

Sift syncs with Canvas, Google Calendar, Gmail, and more to give students one AI assistant that knows their assignments, deadlines, events, and emails — and helps them plan around all of it through natural conversation.

No tab-switching. No manual entry. Just text Sift what you need.

## What It Does

- **Syncs your academic life** — Canvas assignments, calendar events, Gmail, and more stay up to date automatically in the background.
- **Plans your day** — generates time-blocked schedules around your classes, deadlines, and preferences.
- **Works where you are** — chat on the web, over iMessage, or via SMS. Same AI, same context, every channel.
- **Remembers you** — learns your habits, preferences, and patterns so suggestions improve over time.
- **Automates the boring stuff** — daily briefings, deadline reminders, email triage, and smart notifications you can set up in plain English.
- **Manages your tasks** — create, prioritize, and track todos alongside Canvas assignments in one place.

## Architecture

```
Web App (React 19)  ·  iOS App (Expo)  ·  iMessage  ·  SMS
                          │
                    FastAPI Backend
                          │
              ┌───────────┼───────────┐
              │           │           │
        Orchestrator   Workers    Middleware
        Agent (LLM)   (18 jobs)  (7 layers)
              │
        10 Sub-Agents
        100+ Tools
              │
    ┌─────────┼─────────┐
    │         │         │
 Supabase   Redis    External
 (Postgres  (Cache,  (Canvas, Google,
  pgvector, Locks,   Gemini, Notion,
  Auth)     Queues)  Slack, SignalWire)
```

### Highlights

**Hierarchical agent system** — A central orchestrator delegates to 10 specialized sub-agents (scheduling, email, memory, notes, tasks, etc.), each with isolated tool access. 100+ tools auto-registered at class definition time. Mid-processing message injection via Redis-backed steer signals lets users redirect the agent while it's still thinking.

**Native iMessage relay** — A custom Mac Mini HTTP relay (not a third-party wrapper) enables full bidirectional iMessage: chunked streaming with natural pacing, Gemini Vision for photo attachments, tapback reactions, per-user privacy isolation in group chats, and automatic SMS fallback via SignalWire.

**3-tier classifier** — Every inbound signal (email, calendar event, Canvas update) passes through a progressive classifier: keyword hash (~1ms) → embedding similarity via pgvector (~10ms) → Gemini Flash LLM (~500ms). Results above 0.80 confidence feed back into the cache, so repeated patterns get faster over time.

**18 background workers** — APScheduler jobs with Redis distributed locking, exponential backoff, and timeout enforcement. Handles calendar sync (down to 30s intervals), Canvas polling, reminder firing (~10s latency), entity graph decay, weather, email watch renewals, and more.

**Real-time streaming UI** — Token-by-token chat rendering with `requestAnimationFrame` buffering at 60fps. 22 message types (schedule drafts, email drafts, thinking blocks, workflow progress) rendered via polymorphic components. Socket.IO with offline buffering and 15-attempt exponential reconnection.

**Security stack** — 7 middleware layers including hierarchical rate limiting (4-level sliding window), Redis-backed idempotency, and request size gating. AES-256-GCM token encryption with PBKDF2 key derivation. Supabase Auth with row-level security on all tables.

## Tech Stack

| | |
|---|---|
| **Backend** | Python, FastAPI, 26 API routers, 40+ services, 24 repository domains |
| **AI** | Claude (Anthropic), GPT-4.1-mini (OpenAI), Gemini 2.5 Flash (Google) |
| **Database** | Supabase (PostgreSQL + pgvector + Row Level Security + Auth) |
| **Cache** | Redis (Upstash) — caching, distributed locks, message queues, rate limiting |
| **Web** | React 19, Vite, TypeScript, TailwindCSS, Radix UI, TanStack Query |
| **Real-time** | Socket.IO with offline buffering and batched reconnection delivery |
| **Infra** | Fly.io (backend), Vercel (web), Supabase Cloud, Upstash Redis |

## Scale

- ~240K lines Python backend, 192 React components, 84 custom hooks
- 10 sub-agents, 100+ tools, 18 background workers
- 4 channels (web, iMessage, SMS, automation) through a unified agent pipeline
- 7 middleware layers, 9 notification channels, 9 entity graph types

## About

Built by **[Lucence Labs](https://lucence.so)**.

<div align="center">
  <sub>Sift is closed-source. This repository is not accepting contributions.</sub>
</div>
