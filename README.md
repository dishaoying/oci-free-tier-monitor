# OCI Free Tier Monitor

Personal ops automation for **my own Oracle Cloud tenancy**: monitors Always Free
A1 (Arm) capacity in my home region via the official OCI Python SDK, and
provisions a single VM instance when capacity becomes available. Also keeps an
eye on the running instance's keep-alive watchdog.

- Polls the OCI API with adaptive backoff (rate-limit aware, honors `Retry-After`)
- Sends one daily status summary (Beijing 02:00) via ServerChan push
- No third-party resources involved — all API calls target my own OCI account only

## Usage (GitHub Actions)

Runs every 6 hours via `schedule` (max 5.5 h per run). Manual `workflow_dispatch`
is available with `rounds` / `max_minutes` / `dry_run` inputs.

Required repository secrets:

| Secret | Purpose |
|---|---|
| `OCI_CONFIG_JSON` | OCI SDK config (user / fingerprint / tenancy / region) |
| `OCI_PRIVATE_KEY` | OCI API signing key (PEM) |
| `SSH_PUB_KEY` | SSH public key injected into instance metadata |
| `SERVERCHAN_KEY` | ServerChan SendKey for push notifications |
| `VPS_SSH_HOST` / `VPS_SSH_USER` / `VPS_SSH_KEY` | keep-alive watchdog SSH access |

## Local run

```bash
pip install -r requirements.txt
export OCI_CONFIG_JSON='...' OCI_PRIVATE_KEY="$(cat oci_api_key.pem)"
python scripts/oci_monitor.py --rounds 3 --dry-run
```

Personal infrastructure tooling — not intended as a general-purpose service.
