# pentagi-lab

Personal isolated evaluation-lab deployment of the open-source **PentAGI** platform on **GitHub Codespaces**, running on the **Google Gemini free tier**. This repository contains only deployment notes and results.

- Upstream project: https://github.com/vxcontrol/pentagi (authorship: VXControl, MIT license)
- No upstream code is vendored here and no authorship of PentAGI is claimed.
- Deployed version: PentAGI v2.2.0-ce

## Environment

- GitHub Codespaces, default 2-core machine, Docker Compose stack: `pentagi` (port 8443), `pgvector`, `pgexporter`, `scraper`
- LLM provider: Google Gemini (AI Studio free-tier API key), model `gemini-3.5-flash`
  - Model availability was verified against the live `models.list` API. `gemini-3.8-flash` returned transient `503 high demand` at deploy time; `gemini-2.0-flash` is retired.
- Free-only setup: no card, no billing, no paid tiers. Codespaces free quota: 120 core-hours + 15 GB-month storage per month.

## Verified scope (2026-11-27)

Verified working:

- Full stack boots; Web UI reachable through a private forwarded port (8443), not publicly exposed.
- Admin account hardened: default `admin@pentagi.com` / `admin` credentials replaced at forced first-login change; new password stored in a password vault.
- End-to-end model-backed flow: a knowledge-only assistant task ("explain the TCP three-way handshake") completed with a real Gemini model response. No external target, no scanning, no tool containers.

Not yet validated (do not treat this deployment as security-testing capable):

- Vulnerability/tool sandbox workflows (agent Docker tooling, execution containers)
- Multi-agent / automation flows against any target
- No testing of any external system has been performed or is planned from this lab.

## Secrets handling

- The Gemini API key lives only in the codespace `.env` (gitignored) and in the owner's password vault. **Never commit `.env` or any key material.**
- Key hygiene incident on 2026-11-27: an initial key was accidentally displayed in a terminal during setup. It was rotated the same day through the official Google Cloud Console rotation flow; all predecessor credentials were revoked (API-verified dead).

## Stop / resume

- Stop: `gh codespace stop -c <codespace-name>` or github.com/codespaces -> Stop. Stopping ends compute usage; a stopped codespace still counts storage against the 15 GB-month quota while it exists. Delete the codespace to free storage.
- Resume: github.com/codespaces -> Resume (or `gh codespace ssh` / web), then:
  `cd /workspaces/pentagi-lab/pentagi && docker compose up -d`
  The stack (and `.env`) persists across stops. Keep the forwarded port visibility **Private**.
- UI: `https://<codespace>-8443.app.github.dev/`

## Data caution (Gemini free tier)

Google may use free-tier prompts and responses to improve its products, with possible human review. Use synthetic lab content only: never personal, company, or target data in flows.
