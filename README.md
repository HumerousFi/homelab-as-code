# homelab-as-code

Infrastructure-as-code for a two-machine (soon three) home cybersecurity lab, built as a
portfolio project alongside OSCP study: a blue-team detection stack, an isolated offensive
pentest range, and hardened personal services, all reproducible from this repo. Two Ubuntu
Server 24.04 laptops, joined over Tailscale, managed entirely remotely.

See [`docs/architecture.md`](docs/architecture.md) for the full write-up — design decisions,
network topology, and the real bugs hit (and fixed) along the way.

## Machines

| Host | Role | Specs |
|---|---|---|
| `coldcoffee` | Personal services + blue-team detection/observability stack | i5-8265U, 8GB RAM, ~930GB disk |
| `masalachai` | Isolated offensive pentest range, GPU-assisted cracking | i3-3217U, 3.7GB RAM, NVIDIA GT 740M |

A third machine (16GB RAM) is planned to take over SIEM-class workloads once available.

## Layout

- `compose/` — per-service Docker Compose stacks. Each service's secrets live in a gitignored
  `.env` next to its compose file — never commit those.
- `ansible/` — host provisioning and configuration (playbooks + inventory). This is the source
  of truth for everything except container secrets — every service and firewall rule described
  below is reproducible by re-running these playbooks against a clean host.
- `docs/` — architecture notes and write-ups.

## Status (2026-08-09)

**`coldcoffee`** — personal services (Immich, Vaultwarden behind `tailscale serve` for TLS,
Homepage, Portainer) plus the detection/observability stack: Suricata (network IDS) → CrowdSec
(behavioral detection + auto-block) → Loki/Vector/Grafana (log visualization), Prometheus +
node_exporter, Uptime Kuma. `ufw` enabled, default-deny incoming.

**`masalachai`** — isolated pentest range: two libvirt networks (`range-nat` for the Kali attack
box's internet access, `range-isolated` — no forward/route at all — for vulnerable targets),
headless Kali attack box, DVWA + Juice Shop (containers), Metasploitable2 (VM). `ufw` enabled,
same default-deny posture, with the range's libvirt bridges explicitly allowed.

Everything above is bound to the Tailscale interface, localhost, or the range's private
libvirt bridges only — nothing is reachable from the raw LAN or public internet.

**Not yet built:** Cloudflare Tunnel for the one or two services planned to go public, and the
third (16GB) machine's SIEM-class upgrade.
