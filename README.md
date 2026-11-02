# Nexa Support

**Nexa Support** is an AI-powered customer-support assistant for e-commerce stores. It automates first-line support: answering product and policy questions with cited sources, looking up order status, drafting policy-correct refund/return decisions, and escalating sensitive cases to a human. Every metric on the operations dashboard is computed by replaying seeded tickets through the live agent — not hard-coded.

Part of the **Nexa** portfolio.

## Highlights

- Answers inquiries with citations using retrieval over the store's own product catalog and policy documents.
- Looks up order status via a mock Orders API (swappable for a real OMS/Shopify endpoint in one file).
- Drafts return/refund decisions with deterministic, unit-tested logic that respects the written policy (return window, final-sale, worn/used, return-shipping fee, defect → warranty).
- Escalates to a human when confidence is low or the situation is sensitive (defect claims, billing disputes, manager requests).
- Proves ROI on an ops dashboard: deflection rate, time saved, and projected monthly savings.

Runs fully **offline** with a deterministic stub provider — no API keys required.

## Quick start

```bash
# 1. Install (Python 3.10+)
python -m venv .venv && source .venv/bin/activate
pip install -r requirements-dev.txt

# 2. Run the test suite (offline, green)
python -m pytest -q        # or: make test

# 3. Print the ROI report
python -m supportcopilot.cli roi   # or: make roi

# 4. Launch the app (chat + dashboard)
python -m supportcopilot.cli demo  # or: make demo
# -> http://localhost:8000/  (chat)  +  /dashboard  (ROI)
```

Ask questions from the command line:

```bash
python -m supportcopilot.cli ask "what is your return policy?"
python -m supportcopilot.cli ask "return my tent, it's unused" --order NW-1001 --email alex@example.com
```

## Using a real LLM (optional)

Install provider extras and set a key; the app switches from the stub automatically:

```bash
pip install -r requirements-llm.txt
export ANTHROPIC_API_KEY=sk-ant-...   # or OPENAI_API_KEY=sk-...
make demo
```

## Configuration

Environment variables (see `.env.example`):

| Variable | Description | Default |
|---|---|---|
| `SUPPORTCOPILOT_DATA_DIR` | Path to data directory | `data/` |
| `SUPPORTCOPILOT_TOP_K` | Retrieval top-k | `3` |
| `SUPPORTCOPILOT_ESCALATION_THRESHOLD` | Escalation confidence threshold | `0.12` |
| `SUPPORTCOPILOT_REFERENCE_DATE` | Reference date (YYYY-MM-DD) | `2026-06-05` |
| `LLM_PROVIDER` | `stub` (offline) or a real provider | `stub` |
| `ANTHROPIC_API_KEY` / `OPENAI_API_KEY` | Real LLM credentials (optional) | — |

## Testing

```bash
python -m pytest -q
```

49 tests covering retrieval, policy/refund logic, agent routing, ROI math, and the web layer.

## Tech stack

- Python 3.10+, FastAPI + Jinja2 + htmx (single-container web app, no JS build step)
- scikit-learn TF-IDF cosine retrieval, with a dependency-free pure-Python fallback
- Provider interface with real Anthropic/OpenAI backends and a deterministic offline stub
- pytest, Docker + docker-compose, GitHub Actions CI

## Deploy

Single container, deployable anywhere that runs a Docker image. No secrets required to run (uses the offline stub).

```bash
make docker && make docker-run   # http://localhost:8000
# or: docker compose up --build
```

Set provider keys as environment variables to enable a real model.

## License

MIT License. See [LICENSE](LICENSE).
