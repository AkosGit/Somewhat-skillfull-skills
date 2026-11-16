# Somewhat-skillfull-skills

Personal pi agent skills.

## Skills

- **[ai-daily-digest](ai-daily-digest/)** — daily AI/LLM news digest from MarkTechPost, The Decoder & HF Daily Papers: per-source top-10 fresh items, local scoring, dedup via sent-log, posted to ntfy as inline markdown.

## Scheduling (pi extension)

Scheduling uses pi's **scheduler** (`schedule_prompt`, recurring kind) with cron `0 8 * * *` for the daily 08:00 digest run. Note: scheduled tasks currently expire after ~3 days, so re-create them when digests stop arriving — or use a host cron/systemd timer calling `fetch_mtp_digest.sh` as fallback.
