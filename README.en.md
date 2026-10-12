# Competitor Teardown & Content Studio — Remote MCP Server

> [中文说明请见 README.md](README.md)

A **hosted remote MCP server** that lets your AI client tear down competitor accounts, extract real
customer pain points, and write short-video / voice-over scripts — for **Chinese-language content**.

**Endpoint:** `https://kpo.hwzs.club/mcp` (Streamable HTTP)

---

## What it does

Four tools. You talk in natural language; the AI picks the right tool.

| Tool | What it does | Example prompt |
|---|---|---|
| `submit_competitor_analysis` | Analyzes a competitor account: positioning, audience, content patterns, monetization path, strengths/weaknesses — **every conclusion cites the source material** | "Analyze this competitor account, I'll paste the content below" |
| `submit_pain_points` | Extracts real pain points, organized across 8 anxiety categories, each written as **a sentence the customer would actually say**, with the situation it occurs in | "Housekeeping industry, middle-aged women entering the field — give me 30 real pain points" |
| `submit_script` | Writes a voice-over / short-video script: 3-second hook, timed segments, shots, subtitles, compliance self-check | "Turn the second pain point into a 30-second Douyin script" |
| `get_task` | Fetches results. The three tools above are asynchronous — they return a task id immediately | — |

All output is in **Chinese** (that is the point — this is for Chinese-market content).

---

## Setup

### 1. Get an API key

This server ships no model credits. Every model call is billed to **your own Yunjinglink account**
(your key, your cost — we never touch it).

Create an account and get a key here:
**https://www.yunjinglink.com/normal/login/?registration=1&code=800285**

### 2. Configure your MCP client

```json
{
  "mcpServers": {
    "yunjinglink-content": {
      "type": "streamableHttp",
      "url": "https://kpo.hwzs.club/mcp",
      "headers": { "Authorization": "Bearer YOUR_API_KEY" }
    }
  }
}
```

### 3. Just talk

No commands to memorize. State your need in plain language (see the table above).

### On WorkBuddy

WorkBuddy users can install **「对标拆解 · 内容获客」** directly from the connector marketplace —
a form will ask for your key. The `connector/` folder in this repo is exactly the package submitted
to that marketplace (`connector-meta.json`, `mcp.json`, `token-schema.json`, `icon.svg`, `skills/`).

---

## How we handle your key

- Your key stays **on your machine**; we do **not** store it, write it to disk, or log it.
- It is only **passed through** from our server to Yunjinglink as a `Bearer` header.
- We log only the **first 8 hex chars of its SHA-256** — to correlate "is it the same user's key failing?".
- Task records (inputs and outputs) are kept on our server for **7 days**, then deleted automatically.
- We never reuse your material or output as training data, and never share it with third parties.

---

## FAQ

**Why does a generation take a while?**
The three `submit_*` tools are asynchronous: submission returns in milliseconds, generation runs in the
background (usually 20 s – 2 min). Poll with `get_task` every ~10 s.

**"Missing or invalid API key"?**
Check the `Authorization` header in your client config; the key must be complete (starts with `sk-`).

**"Insufficient quota"?**
Your Yunjinglink balance is exhausted — top up and retry.

**Very long input?**
Inputs over the limit are **truncated**, and the response includes a `notes` field telling you exactly
what was cut. Nothing is dropped silently.

**Why isn't the prompt/corpus open source?**
The server holds our real industry corpus and language parameters — that is why the output sounds like
a local person instead of a template. This repo only provides **connection config + tool docs**;
the service itself is a hosted, usage-billed capability.

**Does it support English output?**
Not today — it is built for Chinese-market content (Douyin / WeChat Channels / Xiaohongshu).
An output-language option is on the roadmap.

---

## License & disclaimer

- Repo contents (connection config, tool docs, icon) are provided by Jianggang Technology
  (江港科技) — **all rights reserved**. You are welcome to connect to our hosted service using the
  config above.
- This is a content-production aid. We make **no promises about results** (traffic, followers, sales).
- Review platform rules and local regulations before publishing generated content.

Contact: 江港科技 · hwzs.club
