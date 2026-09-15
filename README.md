# Req2Proof

**Transparent Resume-to-Job Evidence Matcher** — "Don't just match keywords, show the evidence."

Upload a resume and a job description, and Req2Proof breaks the job description into atomic
requirements, then checks each one against the resume and classifies it as **Directly Supported**,
**Partially Supported**, or **Missing Evidence** — with the exact resume text, a confidence score,
and a plain-language explanation behind every decision.

## How it works

- **Frontend**: React + Vite + Tailwind (`src/`). All state lives in the browser for the session —
  nothing is persisted server-side.
- **Backend**: a small Express proxy (`server/index.js`) that holds your Anthropic API key and
  forwards analysis requests to `https://api.anthropic.com/v1/messages`. The key never reaches the
  browser.
- **AI engine**: Claude does the semantic matching — extracting atomic requirements from the job
  description, then matching each one against the resume text, in place of a separate
  sentence-transformers / cross-encoder pipeline.
- **Anti-hallucination check**: every quoted piece of evidence is verified as a literal substring of
  the resume text on the client. If a quote can't be found verbatim, it's shown as "Unverified"
  instead of a real quote.

## Prerequisites

- Node.js 18 or newer
- An Anthropic API key — [console.anthropic.com](https://console.anthropic.com/)

## Setup

```bash
npm install
cp .env.example .env
# then edit .env and paste your key:
# ANTHROPIC_API_KEY=sk-ant-...
```

## Run locally (development)

```bash
npm run dev
```

This runs the Vite dev server (`http://localhost:5173`) and the Express proxy (`http://localhost:3001`)
together. Open `http://localhost:5173`.

## Build for production

```bash
npm run build
npm start
```

`npm start` serves the built frontend and the `/api/messages` proxy from a single Express process on
`http://localhost:3001` (or `$PORT`).

## Scope of this build

This is a hackathon-scope MVP covering the "must have" priority list: resume/JD input, atomic
requirement extraction, semantic evidence matching, direct/partial/missing classification, exact
evidence highlighting, condition validation, missing-evidence explanations, and the evidence
dashboard — plus evidence-strength profiling, the interactive Proof Graph, and CSV/JSON export.

Deliberately left out of this build:
- **PDF upload isn't supported** — paste text, or upload `.txt` / `.docx`.
- **No OCR, no database, no auth/admin panel, no human-review persistence** — everything lives in
  the browser tab for one session.
- Capped at **10 atomic requirements** per analysis to keep each Claude call small and reliable.

Every analysis run makes several real calls to the Anthropic API and will use your API credits.
