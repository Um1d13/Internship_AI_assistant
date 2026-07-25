# ⚽ AI Football Assistant — Homework 1

A simple AI assistant that takes a football question, fetches **real data from the internet**, and returns a clear answer. Everything lives in a single `index.html` file — the interface and all the logic run in plain JavaScript in the browser, with no backend.

🔗 **Demo:** open `index.html` (double-click) — it works out of the box.

---

## 🚀 How to run

1. Open `index.html` in a browser (double-click the file).
2. Type a question or click one of the example buttons.

Everything is already embedded in the file — the AI key and the background image. Nothing to install.

Example questions:
- "Show the Premier League table"
- "Barcelona's upcoming matches"
- "Real Madrid's latest results"
- "Tell me about Liverpool FC"

The assistant replies in the same language you ask in (English or Russian).

---

## 📄 Short description (for submission)

**Which tool did I use?**
The assistant is a single-page web application (`index.html`) written in plain **JavaScript** —
both the interface and all the logic run directly in the browser, with no separate server.
As the AI layer I use **OpenRouter**, a service that gives unified access to many language
models, including free ones. The OpenRouter key is permanent (it does not expire) and works
directly from the browser.

**Which API or data source did I use?**
Real football data comes from **TheSportsDB** — a free public REST API that works directly
from the browser (CORS enabled, no registration, shared test key `3`). It provides league
tables, upcoming and past matches, and club information.

**How does the assistant work?**
It runs a three-step pipeline, all visible in a live log on the page:

1. **Understand (AI).** The user writes a question in natural language. The AI (via OpenRouter)
   acts as a router and returns strict JSON — which data is needed (`table` / `next` / `last` /
   `team_info`), which league, and the club's English name (e.g. «Барселоны» → `Barcelona`).

2. **Fetch.** Based on that plan, JavaScript sends a `fetch` request to the right TheSportsDB
   endpoint and receives fresh JSON with real results.

3. **Explain (AI).** The AI turns that JSON into a short, clear answer in the same language the
   question was asked in, quoting concrete figures and names — without inventing anything.

```
Question → [AI picks the request] → [TheSportsDB returns data] → [AI summarizes] → Answer
```

**Live log on the page.** Below the answer, a timestamped console shows which model the AI chose,
which requests went to the API, and a **"show JSON"** toggle that reveals the raw request/response
JSON for every call (both the AI and the football API). This makes it visible how the assistant
receives and processes the data.

---

## 🗂️ Structure

```
index.html   # everything: UI + logic (3 steps) + OpenRouter key + embedded background
bg.png       # source of the background image (already embedded inside the page)
README.md    # instructions and description for submission
```

## 🛠️ Technical details
- **AI via OpenRouter.** Request: `POST https://openrouter.ai/api/v1/chat/completions`,
  auth header `Authorization: Bearer <key>`. Uses free models (with the `:free` suffix),
  e.g. `google/gemma-4-31b-it:free`, `openai/gpt-oss-20b:free`.
- **Model fallback.** If one model is overloaded or rate-limited (`HTTP 429`), the assistant
  automatically tries the next one in the list, so an answer still comes through under load.
- **AI-free fallback.** If the AI is completely unavailable, the assistant still fetches the
  real football data and formats it directly — no "wall of errors".
- **Background embedded in `index.html`** (data-URI), so the page is self-contained: a single
  file is enough for everything to work, background included.

## ⚠️ Notes
- The OpenRouter key is visible in the page source — fine for a homework, but in a real project
  a key must not be stored in client-side code.
- TheSportsDB's free key returns a shortened table (top 5 teams).
- OpenRouter's free tier has a daily request limit (~50/day); when it runs out, the AI-free
  fallback kicks in, and the limit resets the next day.
