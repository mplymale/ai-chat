# ai-chat

Conversational AI that powers the chat experience on [mikeplymale.com](https://www.mikeplymale.com). Visitors can ask questions about my background, work, and approach, and get answers in my voice — grounded in my resume and the live content of my site.

## How it works

The chat runs on serverless functions (Vercel). When a visitor sends a message, the backend:

1. **Rate-limits** the request by IP to prevent abuse (in-memory sliding window).
2. **Validates** the payload (message length, history size, shape).
3. **Assembles context** by combining a structured resume with freshly fetched text from live site pages, so answers stay current.
4. **Calls the model** (OpenAI GPT-4o) with a system prompt that defines tone and guardrails, then returns the reply plus a few suggested follow-up questions.

All model calls happen server-side — the API key never reaches the browser.

## Stack

- **Runtime:** Node.js serverless functions on Vercel
- **Model:** OpenAI GPT-4o (chat) + GPT-4o-mini (follow-up suggestions)
- **Logging:** Upstash Redis (optional)

## Project structure

```
api/
  chat.js          Main chat endpoint — context assembly, model call, response
  context.js       Returns structured resume + live site content as JSON
  _resumeData.js   Resume source of truth (imported by chat.js)
  _rateLimiter.js  In-memory per-IP rate limiter
  logs.js          Password-protected view of logged questions
```

## Environment variables

| Variable | Purpose |
|----------|---------|
| `OPENAI_API_KEY` | OpenAI API access |
| `LOGS_SECRET` | Protects the /api/logs endpoint |
| `KV_REST_API_URL` / `KV_REST_API_TOKEN` | Upstash Redis for question logging (optional) |

No secrets are committed to the repository — all keys are configured in the deployment environment.
