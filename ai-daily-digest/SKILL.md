---
name: ai-daily-digest
description: Daily AI/LLM news digest from modular RSS+API sources with dedup, local scoring, and chunked ntfy inline posting.
---

# AI Daily Digest

Daily multi-source AI news digest: fetch → dedup → score → markdown → post to ntfy as inline chunks.

## Architecture

- `digest/sources/<name>.py` — one plugin per source (`SOURCE` dict + `fetch() -> [{title, link, date, text}]`). Shipped: `marktechpost` (RSS, browser UA), `decoder` (RSS), `hf_papers` (JSON API, `min_upvotes: 5`).
- `digest/run.py` — driver: fetch all enabled sources → skip links in `sent.json` → local keyword score → top-N fresh per source → markdown → post → record. Flags: `--send`, `--source NAME`, `--force`, `--top-n N`.
- `digest/config.yaml` — enables sources, sets `top_n`, HF upvote threshold.
- `digest/sent.json` — URL → {date, title, source}. Only never-sent links go out; slow days send fewer (or none).
- `post_digest.py` — posts to `ntfy.sh/<topic>` with `Markdown: yes`, body via `-d "$CONTENT"` (never `-d @file`/`-T`, which create attachments), packed into chunks ≤3800 bytes so ntfy renders inline.
- `fetch_mtp_digest.sh` — thin cron wrapper around `run.py --send`.

## Scoring (local, zero LLM tokens)

Keyword score on title+text, capped at 10. High-value (+3): codegen, debugging, testing, CI/CD, model serving, local LLM, vLLM, agents, RAG, fine-tune, quantization, k8s, MoE, open weights. General (+1): ai/ml, transformer, dataset, benchmark.

## Token savers

Local scoring, 500-char text truncation, sent-URL skip before scoring, HF upvote filter, date filter, fully scripted cron (no agent context per run).

## Add a source

1. New `digest/sources/<name>.py` with `SOURCE` + `fetch()`. 2. One line in `config.yaml`.
